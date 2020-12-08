### Background
- 높은 동시성 시스템을 보호하는 효과적인 세가지 방법은 캐싱, 다운그레이드, 쓰로틀링이 있다.
- 쓰로틀링은 여러 시나리오에서 동시성과 요청 수를 제한하기 위해 사용한다. 
- 쓰토를링의 목적은 접근요청을 제한 및 지정된 시간 내의 요청 수에 대한 임계값을 초과하지 않도록 시스템을 보호하는 것이다. 
- 쓰로틀링의 조절 방법은 다음과 같다.

1. Limit the **total concurrency**: 총 동시성을 제한한다. database connection pool 이나 thread pool의 크기를 제한
2. Limit the **instantaneous concurrency** : NGINX의 Limit_conn_module이나 Java의 Semaphore 클래스로 구현 가능
3. Limit the **average access rate of a time window**: Guava Ratelimiter나 NGINX의 limit_req module로 구현 가능
4. Limit the remote **API invocation rate**: 리모트 API의 단위시간내 호출횟수를 제한한다.
5. Limit the **MQ consumption rate**: MQ 소비율을 제한한다.

### Throttling Algorithm
쓰로틀링 조절에 사용되는 알고리즘은 크게 Leaky Bucket Algorithm과 Token Bucket Algorithm이 있다.


#### 1. Leaky Bucket Algorithm

![image](https://user-images.githubusercontent.com/13671946/101449482-38f89b00-396c-11eb-9aeb-a5bbe3868754.png)

`Leaky Bucket Algorithm` 은 큐에 의존하는데, 일단 새로운 요청을 수신하면 대기열에 넣고, 고정된 빈도로 요청을 처리한다. 
만약 인바운드 액세스의 요청이 크고 queue가 가득차면, 새 요청은 삭제될 수 있다.


#### 2. Token Bucket Algorithm

![image](https://user-images.githubusercontent.com/13671946/101449556-59c0f080-396c-11eb-878d-1de8593fcc32.png)

`Token Bucket Algorithm`은 고정된 수의 토큰을 저장하는 버킷을 두고, 토큰을 고정된 속도로 추가되며 버킷 내 최대 토큰 수가 초과되면 새 토큰이 삭제되거나 거부된다. 
새로운 요청이 도착하면 하나의 토큰이 버킷에서 제거되며 직접 처리된다. 토큰을 가져오지 못한 요청은 제한된다.

※ Leaky Bucket Algorithm과 Token Bucket Algorithm의 차이는 Token Bucket Algorithm은 어느정도 급격한 트래픽 증가를 허용하는 반면
Leaky Bucket Algorithm은 안정적인 유출 속도를 보장하는데 사용한다.


### Guava RateLimiter
`RateLimiter`는 `SmoothBursty`, `SmoothWarmingUp` 의 두가지 token bucket algorithm 구현체를 제공한다.

![image](https://user-images.githubusercontent.com/13671946/101450179-74e03000-396d-11eb-953f-246f54e1dc11.png)

#### 1. SmoothBursty
```java
public void testSmoothBursty() {
    RateLimiter r = RateLimiter.create(5);
    while (true) {
      System.out.println("get 1 tokens: " + r.acquire() + "s");
    }
    /**
     * output: Basically, the limiter is executed every 0.2s, which complies with the setting of releasing five tokens per second.
     * get 1 tokens: 0.0s 
     * get 1 tokens: 0.182014s
     * get 1 tokens: 0.188464s
     * get 1 tokens: 0.198072s
     * get 1 tokens: 0.196048s
     * get 1 tokens: 0.197538s
     * get 1 tokens: 0.196049s
     */
}
```
- `RateLimiter` bucket의 token 수를 5개로 생성한다.
- 초당 5개 이하의 토큰을 고정된 속도로 버킷에 배치하여 트래픽을 제어한다.


```java
public void testSmoothBursty2() {
    RateLimiter r = RateLimiter.create(2);
    while (true)
    {
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      try {
        Thread.sleep(2000);
      } catch (Exception e) {}
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("end");
      /**
       * output:
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * end
       * get 1 tokens: 0.499796s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       */
    }
}
```
- 토큰 사용 빈도가 낮은 경우, 토큰을 누적시켜 대기 없이 처리된다.  
- 이는 갑작스러운 트래픽 증가에 대처할 수 있도록 설계되어있기 때문이다. 

```java
public void testSmoothBursty3() {
    RateLimiter r = RateLimiter.create(5);
    while (true)
    {
      System.out.println("get 5 tokens: " + r.acquire(5) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("end");
      /**
       * output:
       * get 5 tokens: 0.0s
       * get 1 tokens: 0.996766s As a result of the delayed processing method, this thread waits for additional time on behalf of a previous thread.
       * get 1 tokens: 0.194007s
       * get 1 tokens: 0.196267s
       * end
       * get 5 tokens: 0.195756s
       * get 1 tokens: 0.995625s As a result of the delayed processing method, this thread waits for additional time on behalf of a previous thread.
       * get 1 tokens: 0.194603s
       * get 1 tokens: 0.196866s
       */
    }
}
```
- 갑작스런 트래픽을 처리해야하는 경우 일시에 버킷에 할당된 토큰 전부(위의 경우 5개)를 획득하여 빠르게 응답한다. 
- 사용 가능한 토큰 수가 부족하므로 그 이후 요청은 토큰 획득을 기다린 후에 처리되는, 지연처리방식이 사용된다. 

#### 2. SmoothWarmingUp
![image](https://user-images.githubusercontent.com/13671946/101451818-1c5e6200-3970-11eb-8d28-7cdac3b44442.png)

`RateLimiterSmoothWarmingUp` 방식은 시작 직후 예열시간을 두는 방식이다.
```java
public void testSmoothwarmingUp() {
    RateLimiter r = RateLimiter.create(2, 3, TimeUnit.SECONDS);
    while (true)
    {
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("get 1 tokens: " + r.acquire(1) + "s");
      System.out.println("end");
      /**
       * output:
       * get 1 tokens: 0.0s
       * get 1 tokens: 1.329289s
       * get 1 tokens: 0.994375s
       * get 1 tokens: 0.662888s  The total amount of time that has been taken for acquiring these three tokens is 3s.
       * end
       * get 1 tokens: 0.49764s  Tokens are acquired at the normal rate of two tokens/s.
       * get 1 tokens: 0.497828s
       * get 1 tokens: 0.49449s
       * get 1 tokens: 0.497522s
       */
    }
}
```
- 토큰 생성이 초당 2개, warm-up 시간이 3초인 RateLimiter의 경우, 시작 직후 0.5초마다 토큰을 생성하지 않고 점진적으로 생성률이 높아지며
3초 이후에 고정된 속도로 토큰을 생성한다. 



### Reference

- https://www.alibabacloud.com/blog/detailed-explanation-of-guava-ratelimiters-throttling-mechanism_594820#comment
