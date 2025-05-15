---
layout: post
title: "AWS - API Gateway to SQS"
---

**1. IAM 정책 생성**
- SQS 권한 추가 (ID에는 AWS 계정ID)
![iam-s3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ilrz03xg7bsplou1xoje.png)

**2-1. IAM 역할 생성**
- API Gateway 서비스 선택 후 역할 생성
![iam-role](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vjrivj9somfif7ge9p58.png)

**2-2. IAM 역할 권한 추가**
- 생성한 역할에 정책연결을 통해 생성한 권한 추가
![iam-role-access](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m4h8f7zmspzex3a1fc8w.png)

**3. API Gateway 생성**
- REST API 구축
- REST API 이름 설정 및 API 생성 (API Gateway 내부에서 dv/prod 등 스테이지 구분 가능)

**4-1. 리소스 생성**
- 리소스 이름 지정 + 경로 지정
![create-resource](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/88yqiwa9oqx0i3w5w8bw.png)

**4-2. 메소드 생성**
- AWS 서비스 SQS 선택
- 경로 재정의를 사용하여 SQS로 전송 (경로는 현재 예시 기준 `ID/api-gateway-to-sqs` 경로 패턴: `${AWS 계정ID}/${SQS-이름}`)
- 실행 역할은 위에서 생성한 IAM 역할의 arn 값
![create-method](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q0fj84bopbg2c7z52ohd.png)

**5. 메소드 상세 설정**
- 통합 요청 탭에서 HTTP 헤더 설정 및 매핑 템플릿 추가
- 템플릿
```
Action=SendMessage&MessageBody=$input.body
```
![mapping-template](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/17uqcskmpq4knt90blin.png)

**6. 테스트**
- 테스트에서 테스트 요청이 가능함.
- 응답 본문에 messageId 확인 + 경로로 지정한 SQS의 사용가능한 메시지 개수 확인.

**FYI.**
- stage변수 사용방법
    - stage 배포 및 stage 변수 추가 후 `${stageVariables.변수명}`으로 사용가능.


cf.
- https://www.youtube.com/watch?v=_U_PiSpbWug
- https://www.youtube.com/watch?v=v8cqDXyHZ4Y
