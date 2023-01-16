# Prometheus와 Grafana를 이용한 Monitoring

## 구성 방법
* Local의 Kind cluster에 Helm Chart를 사용하여 배포


![모니터링3](https://user-images.githubusercontent.com/47857304/212628650-f97cca4f-5a81-4cd6-8ed7-a471128ade99.png)




## 모니터링 중인 지표
1. CPU 사용량 제한 없이 배포된, namespace 별 파드 수 
```
count by (namespace)(sum by (namespace,pod,container)(kube_pod_container_info{container!=""}) unless sum by (namespace,pod,container)(kube_pod_container_resource_limits{resource="cpu"}))
```
2. 배포된 namespace 별 파드 수 
```
sum by (namespace) (kube_pod_info)
```

3. Cluster 전체의 CPU 여유
```
100-sum((rate(container_cpu_usage_seconds_total{container!="POD",container!=""}[30m]) - on (namespace,pod,container) group_left avg by (namespace,pod,container)(kube_pod_container_resource_requests{resource="cpu"})) * -1 >0)
```
