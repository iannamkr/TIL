1. cpu 사용량에 따른 automatic horizontal scaling
2. custom metric을 통한 automatic horizontal scaling


![image](https://user-images.githubusercontent.com/13671946/98316444-bf445900-201d-11eb-820a-d3dd8f263619.png)


autoscaling은 다음 세 단계를 통해 진행됨

### 1. pod metric 수집
![image](https://user-images.githubusercontent.com/13671946/98315374-61af0d00-201b-11eb-9511-47a577f7602c.png)
- `cAdvisor`는 각 node에 구동되며, pod, node에 대한 metric 정보를 수집함
- `Heapster`는 cluster-wide 컴포넌트로 `cAdvisor`가 수집한 metric 정보를 집계함
- `Horizontal Pod Autoscaler`는 REST 호출을 통해 `Heapster`로부터 모든 pod에 대한 메트릭 정보를 얻음

### 2. 필요한 pod 갯수를 계산
> set(podmetric) → v (=required number of replicas)

![image](https://user-images.githubusercontent.com/13671946/98315707-2c56ef00-201c-11eb-9d3c-4523cd7259dc.png)

- `AutoScaler`는 metric 별로 필요한 replica 수를 계산하고, 가장 큰 값을 취함
  - QPS metric: (15 + 30 + 12) / 20 = 47 / 20 = 3
  - CPU metric: (60 + 90 + 50) / 50 = 200 / 50 = 4
  ∴ Max(4, 3) = Replicas 4
  
### 3. scaled resource의 replica count 갱신
![image](https://user-images.githubusercontent.com/13671946/98316355-9ae87c80-201d-11eb-94d0-739a9f3ca00d.png)

- 마지막 단계로 `scaled resource object`의 `replicas` 필드를 업데이트함
- `Autoscaler controller`는 `Scale sub-resource`를 통해 `replicas` 필드를 갱신하며, `Autoscaler`를 붙일 수 있는 것은 아래 4개로 제한한다.
  - Deployments
  - ReplicaSets
  - ReplicationControllers
  - StatefulSets
 
