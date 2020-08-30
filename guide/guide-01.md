# Docker

도커에 대해 기본적인 사용법을 실습합니다.

## Task 1. Docker 기본 실습

### 기본 명령어

| 명령어  |  설명  |
|---|---|
| run | 컨테이너 실행하기 |
| ps | 컨테이너 목록 확인하기 |
| stop | 컨테이너 중지하기 |
| rm | 컨테이너 제거하기 |
| logs | 컨테이너 로그보기 |
| images | 이미지 목록 확인하기 |
| pull | 이미지 다운로드하기 |
| rmi | 이미지 삭제하기 |

### 컨테이너 생성 실습

**ubuntu 컨테이너 생성**

```
docker run --rm -it ubuntu:18.04 /bin/sh
# --rm : 실행이 끝나면 자동으로 삭제
# -it : 텍스트를 입력하겠다는 옵션
```

**간단한 웹 애플리케이션 생성**

```
docker run -d -p 4567:4567 subicura/docker-workshop-app:1
# 외부에 port(4567)를 노출하고, container에 연결이 됨
```

http://xxxx:4567 접속

**MySQL 생성**

```
docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --name mysql \
  mysql:5.7
# 3306 port를 열고 있음
```

Database 생성

```
# mysql 접속
docker exec -it mysql mysql
# db 생성
create database wp CHARACTER SET utf8;
# 계정생성
grant all privileges on wp.* to wp@'%' identified by 'wp';
#
flush privileges;
quit
```

**Wordpress 생성**
( codeserver와 port충돌이 나기때문에, codeserver는 종료 후 수행 : "sudo systemctl stop codeserver" )
```
docker run -d -p 8000:80 \
  -e WORDPRESS_DB_HOST=172.17.0.1 \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

http://xxxx:8000 접속

**컨테이너 목록 확인**

```
docker ps -a
```

**컨테이너 로그**

```
docker logs xxx 
# xxx : container id
```

**컨테이너 종료**

```
docker stop xxx
```

**컨테이너 삭제**

```
docker rm xxx
```

**이미지 목록 확인**

```
docker images
```

**네트워크 생성**

```
docker network create app-network
# 가상의 n/w 이름(app-network) 을 생성.
```

**네트워크에 연결된 컨테이너 생성**

```
docker run -d \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  --name mysql \
  --network=app-network \
  mysql:5.7
 # 가상의 n/w 안에서는 port설정을 하지 않아도 접속이 가능함
```

```
docker exec -it mysql mysql
create database wp CHARACTER SET utf8;
grant all privileges on wp.* to wp@'%' identified by 'wp';
flush privileges;
quit
```

```
docker run -d -p 8000:80 \
  --network=app-network \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

## Exam 1. 방명록 만들기

**frontend**

이미지
- subicura/guestbook-frontend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_API_ADDR # ex) backend:8000

**backend**

이미지
- subicura/guestbook-backend:latest

환경변수
- PORT # ex) 8000
- GUESTBOOK_DB_ADDR # ex) mongodb:27017

**mongodb**

이미지
- mongo:4

기본포트
- 27017


## Exam 1. 방명록 만들기(실습내용)
```
# container id : mongodb, image : "mongo:4"
docker run -d --name=mongodb --network=app-network mongo:4
# "app-network" n/w 안의 container들은 "mongodb"이름으로 ip를 찾을 수 있게 됨.

docker run -d --name=backend --network=app-network -e PORT=8000 -e GUESTBOOK_DB_ADDR=mongodb:27017 subicura/guestbook-backend:latest
docker logs backend

docker run -d -p 3000:8000 -e PORT=8000 -e GUESTBOOK_API_ADDR=backend:8000 --network=app-network subicura/guestbook-frontend:latest
# front라 name은 의미가 없음. 3000번으로 접속하면 내부의 8000번 port로 연결.

# 접속 테스트 : http://13.125.211.167:3000/
```

## 정리

```
docker stop xxx
docker rm xxx
docker system prune -a
```
