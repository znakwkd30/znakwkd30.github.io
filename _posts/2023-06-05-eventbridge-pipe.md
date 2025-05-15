---
layout: post
title: "AWS EventBridge - pipe"
---

## AWS EventBridge Pipe 란?

특정 소스 (DynamoDB, Kinesis, SQS 등) 다양한 소스에서 이벤트가 발생했을때, 이벤트를 핸들링해서 AWS 서비스(Lambda), 이벤트 버스 또는 API로 전송하는 역할을 합니다.

![AWS EventBridge Pipe 구조](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3264yimfp6qd475y79j3.png)

### 소스

어떤 AWS 서비스에 pipe가 트리거되게 할지 (이 글은 SQS를 기준으로 작성되었습니다.)
- 서비스 목록
    - Kinesis
    - SQS
    - DynamoDB
    - Amazon MQ
    - Apache Kafka
    - Amazon MSK

![AWS EventBridge Pipe 소스](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3awie563dyns7j1ohtgy.png)

### 필터링(선택)
- 트리거된 이벤트에 이벤트 패턴을 비교하여 필터링 처리함.
- 이벤트 패턴 필터링 [문서](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html)
- 예시) SQS body로 넘어오는 값 필터링 하는 법.
```
{
  "body": {
    "pattern": "AWS",
    "data": {
      "id": "test",
      "name": "test"
    }
  }
}
```
SQS message body에 pattern 값으로 필터링 하고싶을 경우 이벤트 패턴에 다음과 같이 작성하면 필터링 할 수 있습니다!
```
{
  "body": {
    "pattern": ["AWS"]
  }
}
```

![AWS EventBridge Pipe 필터링](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c2li5md6kb8kkbnbnxul.png)

### 보강(선택)
- 최종 대상에 요청을 전송하기 전에 Lambda 또는 특정 API에 request를 날려서 데이터를 추가로 받아와서 대상에게 요청할때 필요한 값을 보정하는 기능으로 추정
- 실제 사용해볼 기회는 없었음..

![AWS EventBridge Pipe 보강](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/eyr1gdp8dcq2dcp310ll.png)

### 대상
- 원하는 서비스에 요청을 보내는 곳 (이 글에 작성된 예시는 API 대상 입니다.)

![AWS EventBridge Pipe API 대상](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cdnce4las7yosp7x7gg4.png)

원하는 대상을 선택해서 요청을 전송합니다.

cf. 헤더 파라미터에 Key/Value 를 추가하면 Authorization 값 등 헤더 값을 처리 할 수 있습니다. (소스에서 받은 값도 활용이 가능합니다!)

### 대상 - 대상 입력 변환기
- 소스에서 받은 값을 대상에 요청을 보낼때 `req.body`로 전달함.

![AWS EventBridge Pipe 대상 입력 변환기](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/t3vkr1t14j8az8astplt.png)

