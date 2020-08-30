# Replicaset

ReplicaSet -> Find pod by labels -> Create pod from template

## 예제

### 기본 예제

guide-03/task-03/whoami-rs.yml

```yml
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: whoami-rs
spec:
  replicas: 1
  selector:
    matchLabels:
      type: app
      service: whoami
  template:
    metadata:
      labels:
        type: app
        service: whoami
    spec:
      containers:
      - name: whoami
        image: subicura/whoami:1
        livenessProbe:
          httpGet:
            path: /
            port: 4567
# selector : type은 'app' 이면서 service는 'whoami'인 pod가 있는지를 체크하겠다.
# template : selector에서 체크한 pod가 없는경우 아래 형식으로 만들겠다.
# 오류가 발생하는 경우 ==>  error: unable to recognize "whoami-rs.yml": no matches for kind "ReplicaSet" in version "apps/v1beta2"
#   -> v1beta2 -> v1 으로 변경
```

```
kubectl get pods --show-labels
k label po/whoami-rs-fqqgx type-   # type제거->제거후 다시 조회를 하면 하나가 더 생김
k delete pod/whoami-rs-g2js9       # pod제거 ->제거후 다시 조회를 하면 하나가 더 생김 
kubectl label pod/whoami-rs-<xxxx> service-        # service제거->제거후 다시 조회를 하면 하나가 더 생김
kubectl label pod/whoami-rs-<xxxx> service=whoami  # service재적용-> 직전에 생겼던 pod가 종료된다.
kubectl scale --replicas=3 -f whoami.yml  # 일반적으로는 yml 파일을 수정후 "kubectl apply -f whoami-rs.yml" 을 수행하여 재반영한다.
```

### 스케일 아웃 예제

guide-03/task-03/whoami-rs-scaled.yml

```yml
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: whoami-rs
spec:
  replicas: 4
  selector:
    matchLabels:
      type: app
      service: whoami
  template:
    metadata:
      labels:
        type: app
        service: whoami
    spec:
      containers:
      - name: whoami
        image: subicura/whoami:1
        livenessProbe:
          httpGet:
            path: /
            port: 4567
```

## Exam 1. 다음 조건을 만족하는 replicaset을 만들어 보세요.

guide-03/task-03/exam-1.yml

- Name: nginx
- Labels: app => nginx
- Container Name: nginx
- Image: nginx
- Replicas: 3

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
  labels:
    type: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      type: nginx
      service: nginx
  template:
    metadata:
      labels:
        type: nginx
        service: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
```

## 정리

```
kubectl delete -f ./
```
