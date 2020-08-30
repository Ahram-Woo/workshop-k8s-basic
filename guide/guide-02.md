# Docker Compose

도커 컴포즈에 대해 기본적인 사용법을 실습합니다.

## Task 1. Docker Compose 기본 실습

### YAML

[YAML Syntax](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)

### 기본 명령어

| 명령어  |  설명  |
|---|---|
| up | 시작 |
| stop | 중지 |
| down | 중지 후 삭제 |
| ps | 목록 |
| logs | 로그보기 |

### 도커 컴포즈 실습

`guide-02`라는 폴더를 만듭니다. ( Visual Studio Code 에서 PROJECT 밑에 폴더 생성 )

**간단한 웹 애플리케이션 생성**

guide-02/docker-workshop-app/docker-compose.yml

```yml
version: '3'
services:
  web:
    image: subicura/docker-workshop-app:1
    ports:
      - "4567:4567"
```

```
## vscode terminal : ~/project/guide-02/docker-workshop-app 에서 실행
docker-compose up -d
```

http://xxxx:4567 접속  ( container id 가 확인 됨 )
'기존에는 명령적으로 실행("docker run -d -p 4567:4567 subicura/docker-workshop-app:1") 하였다면, 이제는 선언적으로 실행하였다. '

**wordpress 생성**
(wordpress와 mysql을 합쳐서 작성)
guide-02/wordpress/docker-compose.yml

```yml
version: '3'
services:
  wordpress:
    image: wordpress
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wp
      WORDPRESS_DB_USER: wp
      WORDPRESS_DB_PASSWORD: wp
    ports:
      - "8000:80"
    restart: always
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: wp
      MYSQL_DATABASE: wp
      MYSQL_USER: wp
      MYSQL_PASSWORD: wp
 
 # mysql에 별도 접속을 하지 않아도 자동으로 db, user를 생성해 줌
 # wordpress는 외부접속이 필요하기 때문에 port설정을 해줌.
 # mysql보다 wordpress가 먼저 기동되는 경우 mysql이 없어서 오류가 날수 있기때문에 재시작옵션(restart)을 설정해 둠.
```

```
docker-compose up -d
```

**목록**

```
docker-compose ps
```

**로그**

```
docker-compose logs -f
```

**종료 후 제거**

```
docker-compose down
```

## Exam 1. 방명록 만들기

guide-02/guestbook/docker-compose.yml

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


## Exam 1. 방명록 만들기(실습)

guide-02/guestbook/docker-compose.yml

```
version: '3'
services:
  frontend:
    image: subicura/guestbook-frontend:latest
    environment:
      PORT: 8000
      GUESTBOOK_API_ADDR: backend:8000
    ports:
      - "3000:8000"
    restart: always
  backend:
    image: subicura/guestbook-backend:latest
    environment:
      PORT: 8000
      GUESTBOOK_DB_ADDR: mongodb:27017
    ports:
      - "8000:80"
    restart: always
  mongodb:
    image: mongo:4
```

## 정리

```
docker-compose down
docker system prune -a
```



