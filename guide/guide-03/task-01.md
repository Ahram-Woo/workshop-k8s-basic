# kubectl
( k8s의 api server와 통신하기위한 client 도구 )
자주 사용하는 kubectl 명령어를 알아봅니다.

> **TIP** `alias k='kubectl'`을 입력하면 편하게 명령어를 입력할 수 있습니다.

## 기본 명령어

- apply
  - Apply a configuration to a resource by filename or stdin ( 내가 원하는 desired status를 적용하겠다 )
- get 
  - Display one or many resources ( 현재 리소스의 전체적인 목록 )
- describe
  - Show details of a specific resource or group of resources ( 상세내용 )
- delete
  - Delete resources by filenames, stdin, resources and names, or by resources and label selector
- logs
  - Print the logs for a container in a pod
- exec
  - Execute a command in a container ( docker의 exec와 동일 )


> 전체 명령어는 `kubectl`을 입력합니다.

## 명령 vs 선언

```
# 명령
kubectl scale --replicas=3 rs/whoami    # replica set를 3개로 늘려라고 명령하는 것 
```

vs

```yml
# 선언
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: whoami
spec:
  replicas: 3
```

## 기본 오브젝트

다음 오브젝트들을 생성하고 조회하고 삭제하는 작업을 합니다.

- node
- pod
- replicaset
- deployment
- service
- loadbalancer
- ingress
- volume
- configmap
- secret
- namespace

> 전체 오브젝트는 `kubectl api-resources`를 입력합니다.

## 다양한 사용범

특정 오브젝트를 조회하는 방법이 여러개 있습니다.

**get**
( object를 list로 조회 )
```
# pod, replicaset, deployment, service 조회
kubectl get all ( 주로 사용하는 resource들에 대해 조회 )

# node 조회
kubectl get no
kubectl get node
kubectl get nodes

# 결과 포멧 변경
kubectl get nodes -o wide
kubectl get nodes -o yaml
kubectl get nodes -o json
kubectl get nodes -o json |
      jq ".items[] | {name:.metadata.name} + .status.capacity"
```

**describe**

```
# kubectl describe type/name
# kubectl describe type name
kubectl describe node <node name>
kubectl describe node/<node name>
```

**그외 자주 사용하는 명령어**

```
kubectl exec -it <POD_NAME>
kubectl logs -f <POD_NAME|TYPE/NAME>

kubectl apply -f <FILENAME>
kubectl delete -f <FILENAME>
```
