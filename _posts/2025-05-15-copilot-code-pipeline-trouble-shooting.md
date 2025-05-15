---
layout: post
title: "Copilot Code Pipeline Build Stage Trouble Shooting"
---

Pipeline Build Stage Failed (Using Copilot Platform linux/arm64)

해당 에러는 Copilot Platform이 linux/arm64이며, manifest.yml + buildspec.yml 파일을 수정하지 않았을 경우 발생합니다.

Copilot pipelien init를 하여 생성된 manifest.yml + buildspeec.yml 파일의 빌드 이미지는 arm64기반의 이미지가 아니라 일반 linux 기반의 이미지이기 때문에 설정을 통해 arm64 이미지로 맞춰주어야 합니다.

맞춰주지 않게 되면 아래와 같이 resources upload 에러가 발생합니다.
```
✘ upload resources required for deployment for copilot: build and push image: build Dockerfile at /codebuild/output/path/Dockerfile: building image: exit status 1
Cloudformation stack and config files were not generated. Please check build logs to see if there was a manifest validation error.
```

해결 방법은 위에서 말했던거처럼 manifest.yml과 buildspec.yml의 빌드 이미지를 arm64로 맞춰주면 됩니다.

먼저 manifest.yml 파일에 (source와 stages 사이에)
```
build:
  image: aws/codebuild/amazonlinux2-aarch64-standard:2.0
```
코드를 추가해 빌드 이미지를 설정해줍니다.

buildspec.yml 파일에 기존 아래와 같이 작성되어있는 부분을 변경해줍니다. 이렇게 작성 후 새로 빌드를 실행하게되면 빌드가 정상 작동하는것을 확인할 수 있습니다.
```
- wget -q https://ecs-cli-v2-release.s3.amazonaws.com/copilot-linux-v1.18.0
- mv ./copilot-linux-v1.18.0 ./copilot-linux
```
```
- wget -q https://ecs-cli-v2-release.s3.amazonaws.com/copilot-linux-arm64-v1.18.0
- mv ./copilot-linux-arm64-v1.18.0 ./copilot-linux
```

![Copilot Code Pipeline Build Success Image](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ytz0ku6oy0276xtino9m.png)

참고. [github issue](https://github.com/aws/copilot-cli/issues/3158), [github pr](https://github.com/aws/copilot-cli/pull/3190/files)

번외
```
[Container] 2022/05/16 02:38:00 Phase context status code: COMMAND_EXECUTION_ERROR Message: Error while executing command: for env in $pl_envs; do
  tag=$(sed 's/:/-/g' <<<"${CODEBUILD_BUILD_ID##*:}-${env}" | rev | cut -c 1-128 | rev)
  for svc in $svcs; do
  ./copilot-linux svc package -n $svc -e $env --output-dir './infrastructure' --tag $tag --upload-assets;
  if [ $? -ne 0 ]; then
    echo "Cloudformation stack and config files were not generated. Please check build logs to see if there was a manifest validation error." 1>&2;
    exit 1;
```

로그를 보다보면 이런 에러가 붉은 글씨로 떠있어서 해당 에러 메세지를 보면서 어떤 에러인지 유추 하였는데 이부분을 볼 필요가 없었습니다... 살짝만 위로 올려보면 위에 작성한것 처럼 명확한 에러를 보여주는....T^T
