---
layout: post
title: "Docker Node Image Variants"
---

## `node:<version>`
사실상 default 이미지.
요구 사항이 무엇인지 확실하지 않을 때(필요한 이미지가 불확실할때) 사용하는 것이 좋습니다. 이 이미지는 버리는 용도의 컨테이너(소스코드를 마운트하고 앱을 실행하기 위한 컨테이너)로 사용되기도 하고 다른 이미지 빌드용으로 사용될 수도 있습니다.

몇몇 태그들은 `bullseye`, `buster` 또는 `stretch`와 같은 이름을 포함하고 있는 태그들이 있는데 Debian 릴리즈 코드의 이름이고 이 이미지들은 해당 릴리즈 버전 기반의 이미지들 입니다.

이 태그는 `buildpack-deps`이미지 기반입니다. `buildpack-deps`는 시스템에 많은 도커 이미지를 가진 도커 사용자를 위해 설계되었습니다. 매우 많은 Debian 패키지들이 있으나, 패키지에서 파생된 이미지가 설치해야 하는 패키지 수가 줄어들어 시스템에 있는 모든 이미지의 전체 크기가 줄어듭니다.

## `node:<version>-alpine`
`Alpine Linux` 프로젝트 기반. `Alpine Linux`는 대부분의 배포 기반 이미지 보다 작아서 작은 이미지를 만들 수 있음.

이 Variants는 최종 이미지 크기가 가능한 한 작은 것이 주요 목표일 때 유용함.

## `node:<version>-slim`
기본 태그에 포함된 공통 패키지를 포함하지 않으며 node를 실행하기 위한 최소한의 패키지만 포함.

[Node official Image - dockerhub](https://hub.docker.com/_/node)
