---
layout: post
title: "AWS CodePipeline을 활용한 빌드/배포 자동화"
date: 2019-07-02 16:00:00
categories: dev
tags: AWS server
---


안녕하세요. 백엔드 개발자 이정준입니다.<br>
API를 개발하고 AWS를 활용한 서버 설계 및 관리를 담당하고 있습니다.

버그 없이 개발을 잘 했다면 다음 단계는 빌드와 배포입니다. 그동안 젠킨스(Jenkins)을 사용했었는데 여러 불편함을 느끼고 AWS CodePipeline을 이용한 배포 환경으로 변경하였습니다.

젠킨스에서 불편함을 느꼈던 가장 큰 이유는 구동하기 위한 '서버'가 필요했기 때문입니다. 반면 AWS 서비스는 **서버리스(Serverless) 구조**로 사용할 수 있으며, 실제 작업할 때 외에는 고정적으로 서버 사용 비용이 발생하지 않습니다. **서버 관리 이슈도 사라집니다**. 플로그인 관리, 계정 관리 등 젠킨스 서버를 관리하면서 발생하는 시간을 절약할 수 있습니다.

저희 같은 스타트업에서, 젠킨스 서버를 관리하면서 발생하는 이슈를 해결하기 위해 리소스를 별도로 사용하는 것은 매우 비효율적입니다. 그래서 AWS CodePipeline을 사용하여 빌드/배포 자동화 환경을 서버리스 구조로 세팅하였습니다.
<br>
<br>
<br>
# AWS CodePipeline 구조
AWS CodePipeline의 구조는 아래와 같습니다.


<img src="/assets/7_serverless/image1.png">

<br>

*Source: <https://aws.amazon.com/ko/blogs/startups/accelerating-software-delivery-on-aws/>

개발자가 어떤 소스(git, 파일 등)를 변경하면 해당 내용을 check해서 빌드하고, 테스트한 뒤에 배포를 실행합니다. 일련의 과정은 데이터로 저장되며 정상적으로 빌드/배포가 되었는지, 문제가 있었다면 어느 부분에서 발생했는지 나타내 줍니다.
<br>
<br>
<br>
# AWS CodePipeline 단계별 설명
<img src="/assets/7_serverless/image2.png">

<br>

*Source: <https://docs.aws.amazon.com/ko_kr/codepipeline/latest/userguide/reference-pipeline-structure.html#actions-valid-providers>

위의 내용은 Pipeline의 단계별로 사용할 수 있는 서비스를 정리한 내용입니다. CodePipeline을 이용하면 무려 젠킨스도 서버리스 구조로 사용할 수 있습니다. (OMG!) 보시면 그 외에도 다양한 서비스를 활용해 AWS CodePipeline 구조를 구성할 수 있습니다.

이 중에서 저희가 택한 방식을 도식화해서 보여주면 다음과 같습니다.

<img src="/assets/7_serverless/image3.png">

아직까지는 CodePipeline에서 BitBucket을 지원하지 않습니다. 그래서 Bitbucket에서 원하는 브랜치에서 Push 또는 Pull request가 발생하면 CodeBuild를 통해서 Amazon S3에 업로드하고, 해당 내용을 CodePipeline을 통해서 캐치합니다. 캐치된 데이터는 CodeBuild를 통해 build되고 테스트 과정을 거칩니다. 테스트를 성공적으로 수행하면 해당 내용을 Amazon S3 또는 Amazon ECS로 배포합니다.
<br>
<br>
<br>
# AWS CodeBuild 및 CodePipeline 셋업
## 1. Bitbucket Webhook CodeBuild 생성

앞단에서 Bitbucket의 내용을 S3로 CodeBuild를 추가로 하나 더 만들어주면 됩니다.

<img src="/assets/7_serverless/image4.png">

**프로젝트 이름**을 작성해주고 **설명**을 간단히 적어줍니다. 그리고 **빌드 배지 활성화**를 선택해 줍니다.

**리포지토리**는 '내 Bitbucket 계정의 리포지토리'를 선택하고, **연결상태**에서 BitBucket 계정으로 연결 시켜주면 됩니다. 연결이 잘 되었다면 Bitbucket 리포지토리에 리포지토리 정보가 뜰텐데, 이때 원하는 리포지토리 정보를 선택하면 됩니다.

그런 다음, 기본 소스 Webhook 이벤트를 설정해 줍니다. Webhook을 선택하고, **이벤트 유형**은 PULL_REQUEST 또는 PUSH를 선택해 줍니다. PULL_REQUEST의 경우 CREATED/UPDATED/MERGED 중에서 원하는 이벤트를 선택할 수 있습니다. 이 때 **잊지말고 HEAD_REF를 설정**해줘야 합니다. 해당 내용을 설정하지 않으면 모든 PUSH에서 이벤트가 발생하게 됩니다.

예를 들어, refs/heads/develop로 하면 develop 브랜치에서 발생하는 PUSH만 Webhook을 걸어줄 수 있습니다.

<img src="/assets/7_serverless/image5.png">


**환경 이미지**는 어떤 운영체제 상에서 빌드를 할지 정하는 부분입니다. 관리형 이미지를 선택하면 우분투(Ubuntu)를 사용합니다. 만약 다른 운영체제를 사용하고 싶다면 사용자 지정 이미지를 선택하고, 원하는 운영체제를 선택해주면 됩니다.

도커 이미지를 빌드하거나 권한 승격이 필요한 경우에는 권한이 있음을 선택해 줍니다.

CodeBuild에서도 관리할 서비스 역할을 만들거나 기존 역할을 사용합니다. 일반적으로는 CodeBuild 마다 역할을 분리해서 관리해주는 것이 좋습니다. 조금 더 디테일한 설정을 하고 싶다면 추가 구성을 통해서 설정해 줍니다. 동작하는 제한 시간, 사용하고 싶은 인증서나 VPC 등을 설정할 수 있습니다.

**Buildspec**은 빌드를 어떻게 할지에 대한 내용을 작성해주는 곳입니다. 빌드 명령을 직접 적어줘도 되지만 git을 통해서 관리하는 것이 더 좋기 때문에 일반적으로 Buildspec.yml을 사용합니다.

<img src="/assets/7_serverless/image6.png">


**아티팩트**는 해당 없음을 사용하면 됩니다. 물론 여기서도 Amazon S3를 선택하면 되긴 하지만 buildspec.yml로 관리해 주는 것이 더 좋습니다. 해당 파일에서 빌드된 데이터를 어디로 저장할지 정의해 줍니다.

**로그**는 해당 CodeBuild에 대한 진행사항을 AWS CloudWatch에 기록합니다. 이후 빌드가 될 때, 모든 내용을 기록하고 관리할 수 있습니다.

<br>

## 2. 파이프라인 설정 선택

<img src="/assets/7_serverless/image7.png">


**파이프라인 이름**을 정하고, 서비스 역할을 새로 만들지 아니면 기존 서비스를 활용할지 확인하면 됩니다. 해당 내용은 IAM(AWS Identity and Access Management)를 통해서 관리할 수 있습니다. 빌드/배포하고자 하는 환경에 따라 권한을 다르게 줘야 합니다. 1번에서 말씀 드린바와 같이, 빌드/배포 환경은 동일한 서비스 역할을 사용하지 않는 것이 좋습니다.

좀 더 디테일하게 설정을 하고 싶다면 고급 설정을 이용할 수 있습니다.

<br>

## 3. 소스 스테이지 추가

<img src="/assets/7_serverless/image8.png">

**소스 공급자**를 Amazon S3 선택하고, 버킷과 S3 객체 키를 작성합니다. S3 객체 키의 경우 해당 버킷에서 캐치할 파일의 경로를 작성해주면 됩니다.

<br>

## 4. 빌드 스테이지 추가

<img src="/assets/7_serverless/image9.png">

빌드 공급자를 AWS CodeBuild로 선택하고, 사용할 리전을 선택해줍니다. 기존에 만들어 놓은 CodeBuild 프로젝트가 있다면 선택하고, 그렇지 않다면 '프로젝트 생성'을 통해 새로운 프로젝트를 만들어 줍니다.

위에서 설명한 것과 같이 CodeBuild를 만들면 됩니다. 새로운 프로젝트를 만들었다면 아래의 버튼을 통해 CodePipeline으로 돌아갑니다.

<br>

## 5. 배포 스테이지 추가

<img src="/assets/7_serverless/image10.png">

배포 스테이지에서는 어떤 환경에 배포할지 결정합니다. Amazon ECS에 배포 한다고 가정하고 배포 공급자로 선택해 줍니다. 배포하고자 하는 리전, 클러스터, 서비스 이름을 선택한 후, 검토로 넘어가서 지금까지 설정한 부분에 문제가 없는지 확인해 줍니다.

확인이 끝났다면 파이프라인을 생성해서 사용하면 됩니다.

<img src="/assets/7_serverless/image11.gif">

<br>

AWS CodeBuild와 CodePipeline을 사용해서 빌드 및 배포 자동화 시스템을 구축해 보았습니다. 스텝만 잘 따르면 그렇게 어렵지 않습니다. 빌드/배포 자동화 시스템은 MSA(Microservice Architecture) 구조에서 필수입니다. MSA 구조로 개발을 하게 되면 소스가 여러 레파지토리에 나눠지게 되고, 레파지토리별로 빌드/배포를 구성해야 하기 때문입니다. 이 때 서버리스 구조로 빌드/배포 자동화 시스템을 구축한다면 좀 더 효과적으로 프로젝트를 관리할 수 있으리라 생각합니다.