---
layout: post
title: "What is Dockerizing"
---

Dockerizing(도커라이징)은 Docker 컨테이너를 사용하여 애플리케이션을 패키징, 배포 및 실행 하는 과정입니다.

여기서 Docker란 애플리케이션에 필요한 모든 기능을 하나의 패키지로 제공하는 오픈 소스 도구입니다.

도커 컨테이너에서 내가 만든 애플리케이션이 실행되도록 하려면 어떻게 해야 할까요? 바로 Docker Image를 만들면 됩니다!

Docker Image를 만들기 위해 우선 Dockerfile을 작성해야합니다.
```
$ touch Dockerfile
```
생성된 Dockerfile에 가장 먼저 어떤 Node 버전을 사용할지 작성하여야 합니다. 이 글에서는 16.15.0 버전을 사용할 예정입니다. ([Docker Hub node image](https://hub.docker.com/_/node)) ([Docker Image Variants](https://dev.to/hyeonjun/docker-node-image-variants-46e6))
```
#FROM node:<version>
FROM node:16.15.0
```
이미지 안에 애플리케이션 코드를 넣기 위한 폴더 생성 및 Working Directory 설정
```
# dir 생성 및 working dir 설정
RUN mkdir -p /app
WORKDIR /app
```
생성한 app directory에 source code copy (!주의 Dockerfile의 path를 확인해주세요.)
```
COPY ./ /app
```
app directory에 dependecy install 및 source code build
```
RUN yarn install
RUN yarn run tsc
```
Docker demon에 바인딩할 포트 선택 및 서버 실행 CMD 입력 (!주의 CMD는 package.json에 실행 script에 따라 달라질 수 있습니다.)
```
# Docker demon에 바인딩할 포트 선택
EXPOSE 3000

# 서버 실행
CMD ["yarn", "start"]
```
node_modules, yarn-error.log 모듈 및 파일은 복사되지 않도록 .dockerignore 파일 생성(Dockerfile과 같은 경로에 생성!)

Docker Image 빌드
```
docker build -t <username>/<원하는 docker image명><:tag명> .
```
위 명령어를 사용해 빌드된 이미지들 리스트를 아래 명령어로 확인 가능!
```
docker image ls
```

Docker Image 실행하기
```
docker run -p 3000:3000 -d <username>/<docker image명>
```
위 명령어를 통해 build한 Docker Image를 실행할 수 있고 [Docker Desktop](https://www.docker.com/products/docker-desktop/)을 통해 logging 및 관리를 할 수 있다! (CMD로도 가능하다.)
