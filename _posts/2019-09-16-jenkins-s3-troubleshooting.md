---
title: "Jenkins S3 정적호스팅 업로드 오류 (s3 인증 오류)"
last_modified_at: 2019-09-16T00:00:00+09:00
categories:
  - trouble-shooting
tags:
  - jenkins
  - aws
  - s3
toc: true
toc_sticky: true
toc_label: "shortcut"
---
Jenkins 로 git에서 소스를 내려 받아 서버에서 빌드 후 AWS S3 에 업로드하여 정적호스팅을 하려고 설정을 했다.

과정중에 다음과 같은 오류가 발생

`The authorization mechanism you have provided is not supported. Please use AWS4-HMAC-SHA256`

원인은 2버전의 인증을 허용하지 않는 리전에서 4버전의 인증을 이용하기 때문에 발생하는 오류라고 한다. (V4 인증 오류)

[https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/API/sig-v4-authenticating-requests.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/API/sig-v4-authenticating-requests.html)

에러 메시지 :

    Exception caught during task execution: A client error (InvalidRequest) occurred when calling the PutObjects operation: The authorization mechanism you have provided is not supported. Please use AWS4-HMAC-SHA256


이게 검색해보면 다양한 방법이 나오는데...  
아래의 명령어를 사용해서 v4 정책을 default 로 설정하라는 내용이 많았다.

    $ aws configure set default.s3.signature_version s3v4

그러나 불행히도 나의 환경에서는 위의 명령에 대해서도 오류가 발생했다.  
그러던 중... 나와 같은 문제로 고통받는 외국인의 글이 있었다...ㅠ  


[https://github.com/aws/aws-cli/issues/2432](https://github.com/aws/aws-cli/issues/2432)


문제가 발생한 이유가 aws cli를 apt 패키지 관리자로 설치하였기 때문이라고 한다.  
awscli를 apt 패키지 관리자로 설치하게 되면 최신 버전으로 설치되지 않아서 인증관련 문제가 발생하는 것

**즉, aws cli를 pip3 로 설치해야 문제가 발생하지 않는다.**

## ubuntu pip3 설치하는 방법

이 역시 많은 방법이 나오는데 아래의 방법이 가장 간편했다.

`$ sudo apt install python3-pip`

설치 후 `$pip3 --version` 명령으로 확인한다.

pip가 설치되면 amazon에서 안내하는 대로 awscli 를 설치하면 된다.

`pip3 install awscli --upgrade --user`

[https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-install.html](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-install.html)
[https://github.com/aws/aws-cli/blob/master/README.rst](https://github.com/aws/aws-cli/blob/master/README.rst)

이후 다시 s3 버킷에 파일을 업로드하면 문제없이 업로드가 될 것이다.. 아마도

    $ aws s3 cp /home/jenkins/.jenkins/jobs/jenkins-s3-test/workspace/dist/ s3://jenkins-s3-test --recursive --grant read=uri=http://acs.amazonaws.com/groups/global/AllUsers


혹시 다른 권한 오류가 발생한다면

S3 버킷 정책에 `GetObject`, `PutObject`, `DeleteObject` 등의 내용이 잘 있는지 확인!

    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": "*",
                "Action": [
                    "s3:GetObject",
                    "s3:PutObject",
                    "s3:DeleteObject"
                ],
                "Resource": "arn:aws:s3:::jenkins-s3-test/*"
            }
        ]
    }