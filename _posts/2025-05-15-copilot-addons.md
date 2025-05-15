---
layout: post
title: "Copilot addons"
---

## addons yml 설정하기
기본 템플릿은 아래와 같습니다. 
경로는 `copilot/{serviceName}` 폴더 아래에 addons 폴더를 추가하고 `{name}.yml` 파일 추가하여서 작성하면됩니다. 
최종 경로는 `copilot/{serviceName}/addons/{name}.yml`가 됩니다.

```
Parameters:
  App:
    Type: String
  Env:
    Type: String
  Name:
    Type: String

Resources:
  SQSAccessPolicy:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Sid:
            Effect:
            Action:
            Resource:
Outputs:
  SQSAccessPolicyArn:
    Description:
    Value: !Ref SQSAccessPolicy
```

Parameters는 AWS Copilot에서 항상 요구하는 필수 항목입니다. (참고. [copilot-cli](https://aws.github.io/copilot-cli/docs/developing/additional-aws-resources/#customizing-the-parameters-section)) 
`AWS Copilot always requires the App, Env, and Name parameters to be defined in your template.`

Resources는 실제 사용할 AWS Resource PolicyDocument Statement에 사용할 Resource의 Sid, Effect, Action, Resource를 작성하면 됩니다.

```
Sid: SQS-A
Effect: Allow
Action:
  - sqs:SendMessage
Resource: arn:aws:sqs:ap-northeast-2:000000000000:sqs-a
```

예를 들어, sqs-a에 sqs message 전송 권한을 주고싶으면 위와 같이 Action에 권한 Resource에는 실제 arn값을 작성하면됩니다. Sid는 StatementId 입니다. (참고. [IAM sid](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_sid.html))

추가로 sqs-b에 전송 권한을 주고싶다면 아래와같이 작성하면 됩니다.

```
Sid: SQS-B
Effect: Allow
Action:
  - sqs:SendMessage
Resource: arn:aws:sqs:ap-northeast-2:000000000000:sqs-b
```

작성된 전체 yml은 아래와 같습니다. IAM 권한은 copilot이 deploy되면서 addons yml에 따라서 자동으로 IAM 권한을 만들어서 설정해줍니다.

```
Parameters:
  App:
    Type: String
  Env:
    Type: String
  Name:
    Type: String

Resources:
  SQSAccessPolicy:
    Type: AWS::IAM::ManagedPolicy
    Properties:
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Sid: SQS-A
            Effect: Allow
            Action:
              - sqs:SendMessage
            Resource: arn:aws:sqs:ap-northeast-2:000000000000:sqs-a
          - Sid: SQS-B
            Effect: Allow
            Action:
              - sqs:SendMessage
            Resource: arn:aws:sqs:ap-northeast-2:000000000000:sqs-b

Outputs:
  SQSAccessPolicyArn:
    Description: "Sqs 메시지 전송 권한"
    Value: !Ref SQSAccessPolicy
```

## 기대효과
- IAM 권한이 템플릿화 되어서 이상적인 서버 구조가 될 수 있음!!
