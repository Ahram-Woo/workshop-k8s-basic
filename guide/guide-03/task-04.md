# Deployment

Deployment using replicaset

## 예제

### 기본 예제

guide-03/task-04/whoami-deploy.yml

```yml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: whoami-deploy
spec:
  replicas: 3
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

```
kubectl set image deploy/whoami-deploy whoami=subicura/whoami:2
kubectl apply -f whoami-deploy.yml
kubectl get rs -w
kubectl describe deploy/whoami-deploy  # deployment-controller 가 replica set에서 명령을 요청한 내용을 확인할 수 있다.
kubectl rollout history -f whoami-deploy.yml
kubectl set image deploy/whoami-deploy whoami=subicura/whoami:1 --record=true
kubectl rollout history -f whoami-deploy.yml  # 3번을 변경했더라도, 다른 이미지가 2개뿐이면 최대 2개만 조회가 된다.
kubectl rollout history -f whoami-deploy.yml --revision=2
kubectl rollout status deploy/whoami-deploy
kubectl rollout undo deploy/whoami-deploy
kubectl rollout undo deploy/whoami-deploy --to-revision=3
```

### 추가 예제

guide-03/task-04/whoami-deploy-strategy.yml

```yml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: whoami-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      type: app
      service: whoami
  minReadySeconds: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
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

```
kubectl describe deploy/whoami-deploy
kubectl set image deploy/whoami-deploy whoami=subicura/whoami:2
kubectl get rs -w
```

## Exam 1. 다음 조건을 만족하는 deployment를 만들어 보세요.

guide-03/task-04/exam-1.yml

- Name: nginx
- Labels: app => nginx
- Container Name: nginx
- Image: nginx
- Replicas: 3

## 정리

```
kubectl delete -f ./
```
