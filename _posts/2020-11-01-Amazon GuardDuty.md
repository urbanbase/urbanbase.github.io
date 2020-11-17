---
layout: post
title: "아직도 Amazon GuardDuty 도입을 안 하셨다면?"
date: 2020-11-01 10:00:00
categories: dev
tags: AWS server API

---

<img src="/assets/20_11_AmazonGuardDuty/20_11_thumbnail.png" width="" alt="AmazonGuardDuty 1">
<br>

안녕하세요. API 개발팀장 최진욱입니다. 어반베이스에서는 Serverless 기반 RESTful API Architecture 설계 및 개발을 담당하고 있습니다. 여느 스타트업처럼 저희도 기술 사이드에 방점을 두고 서비스 퀄리티를 높이는 데 집중했었는데요, 서비스가 차츰 안정화되다 보니 보안 이슈가 중요해지더라고요. AWS에서 다양한 보안 위협 탐지 서비스를 제공하고 있는데, 저희는 그중에서 모든 리전에서 지원 가능한 GuardDuty 도입을 고려하게 됐습니다. 

# Amazon GuardDuty?
Amazon GuardDuty는 AWS 계정 및 워크로드를 보호하는 지능형 위협 탐지 보안 서비스로, 잠재적 무단 활동과 악의적 활동을 지속적으로 모니터링하여 감지합니다. GuardDuty는 AWS Region 내에서 AWS CloudTrail 로그, Amazon VPC (Virtual Private Cloud) 플로우 로그 및 DNS (Domain Name System) 로그의 데이터를 처리합니다. 아래 이미지의 탐지 영역에 Amazon GuardDuty가 위치하는 것을 볼 수 있습니다.


<img src="/assets/20_11_AmazonGuardDuty/image 01.png" width="" alt="AmazonGuardDuty 1">



# 자, 그럼 셋팅해봅시다!
저희는 Amazon CloudWatch Events 트리거를 이용해 알림을 2가지 방식으로 받고 싶었습니다. 
1) 이메일 알림을 받기 위한 아마존 SNS 설정
2) ~~이메일은 놓치기 쉬우니~~ 사내에서 사용 중인 텔레그램 알림을 받기 위한 Lambda 함수 설정 

먼저, Amazon GuardDuty를 활성화 할 동일 리전에 SNS 주제를 생성하고 구독을 설정합니다.

## Amazon SNS (Console)

<img src="/assets/20_11_AmazonGuardDuty/image 02.png" width="" alt="AmazonGuardDuty 2">


## Amazon SNS (CLI)

```bash
// 주제 생성
$ aws sns create-topic --region ap-northeast-2 --name urbanbase-api-team

// 구독
$ aws sns subscribe \
      --topic-arn arn:aws:sns:ap-northeast-2:{AWS_ACCOUNT}:urbanbase-api-team \
      --protocol email \
      --notification-endpoint my-email@example.com
```

## AWS Lambda (Node.js)

```jsx
/**
 * AWS SNS 통해 오는 알림을 텔레그램으로 전달 한다.
 * @param event
 * @param context
 * @returns {Promise<{statusCode: number}|boolean>}
 */
exports.handler = async (event, context) => {
  try {
    // info: health-check
    if (event.hasOwnProperty('healthCheck') && event.healthCheck === true) {
      return true;
    }

    const severityEmoji = (Number(event.detail.severity)) >= 7 ? '🔴' : '🟠';
    const sampleDataEmoji = ('sample' in event.detail.service.additionalInfo && event.detail.service.additionalInfo.sample === true) ? '⭕' : '❌';

    const guardDutyURL = `https://console.aws.amazon.com/guardduty/home?region=${event.region}#/findings?search=id%3D${event.id}`;
    const options = {
      parse_mode: 'Markdown',
      reply_markup: {
        inline_keyboard: [
          [
            {
              text: 'For more details',
              url: guardDutyURL,
            },
          ],
        ],
      },
    };

    const text = `
*[GuardDuty Alarm]*

*타이틀*: ${event.detail.title}
*샘플유무*: ${sampleDataEmoji}
*스테이지*: ${stage[process.env.STAGE]}
*심각도*: ${event.detail.severity}(${severityEmoji})
*타입*: ${event.detail.type}
*리전*: ${event.region}
*설명*: ${event.detail.description}
`;

    await bot.sendMessage(process.env.TELEGRAM_ID, text, options);
  } catch (e) {
    console.error(`Final Catch in ${__filename}:`, e);
  }
  return {
    statusCode: 200,
  };
};
```

## Amazon CloudWatch Events (Console)

### 규칙 생성

<img src="/assets/20_11_AmazonGuardDuty/image 03.png" width="" alt="AmazonGuardDuty 3">


1단계 규칙 생성 화면은 '이벤트 소스'와 '대상'으로 구분됩니다. 이벤트 소스는 이벤트 패턴을 작성하거나 사용자를 지정, 대상을 호출할 일정을 설정할 수 있습니다. 저는 사용자 지정 이벤트 패턴을 선택해서 아래와 같이 작성했습니다. 

#### 이벤스 소스 설정하기

이벤트 소스는 guardduty, 세부 유형은 Finding, 세부 조건은 심각도 보통 이상으로 지정했습니다.

```json
{"source":["aws.guardduty"],"detail-type":["GuardDuty Finding"],"detail":{"severity":[4,4.0,4.1,4.2,4.3,4.4,4.5,4.6,4.7,4.8,4.9,5,5.0,5.1,5.2,5.3,5.4,5.5,5.6,5.7,5.8,5.9,6,6.0,6.1,6.2,6.3,6.4,6.5,6.6,6.7,6.8,6.9,7,7.0,7.1,7.2,7.3,7.4,7.5,7.6,7.7,7.8,7.9,8,8.0,8.1,8.2,8.3,8.4,8.5,8.6,8.7,8.8,8.9]}}
```

#### 대상 설정하기

먼저 SNS는 해당 리전에서 주제를 생성하셔야합니다. (생성 방법은 검색 고고) 생성하셨다면, 이벤트가 이벤트 패턴과 일치하거나 일정이 트리거될 때 호출할 대상을 지정해보겠습니다.

<img src="/assets/20_11_AmazonGuardDuty/image 04.png" width="" alt="AmazonGuardDuty 4">



1. 대상 추가 버튼 클릭
2. 셀렉트박스에서 SNS 주제 선택
3. 주제란에서 Amazon SNS 에서 생성한 주제 선택
4. 입력 구성에서 입력 변환기 선택
5. 입력 경로(첫번째 텍스트 상자)에 아래 코드 (5. 입력 경로) 붙여넣기
6. 입력 템플릿(두번째 텍스트 상자)에 아래 코드 (6. 입력 템플릿) 붙여넣기
7. 끝~

```json
// 5. 입력 경로
{"severity":"$.detail.severity","Finding_ID":"$.detail.id","Finding_Type":"$.detail.type","region":"$.region","Finding_description":"$.detail.description"}

// 6. 입력 템플릿
"You have a severity <severity> GuardDuty finding type <Finding_Type> in the <region> region."
```

#### Lamda 함수 추가

이제 Lambda 함수를 추가해보겠습니다. 

1. 대상 추가 버튼 클릭
2. 셀렉트박스에서 Lambda 함수 선택
3. 함수란에서 텔레그램으로 메시지를 전송해 줄 함수 선택
4. 입력 구성에서 일치하는 이벤트 선택
5. 끝~

이제 하단의 세부 정보 구성 버튼을 클릭합니다.

### 규칙 세부 정보

<img src="/assets/20_11_AmazonGuardDuty/image 05.png" width="" alt="AmazonGuardDuty 5">


2단계 규칙 세부 정보 화면에서는 이름과 설명, 상태를 설정할 수 있습니다. 이름과 설명을 기재하시고 규칙 설정 버튼을 클릭하시면 이벤트 규칙 설정이 완료됩니다.

<br>
<br>

## Amazon CloudWatch Events (CLI)

### 규칙 생성

```json
$ aws events put-rule --region ap-northeast-2 --name "guardDuty-trigger-production" --event-pattern "{\"source\":[\"aws.guardduty\"],\"detail-type\":[\"GuardDuty Finding\"],\"detail\":{\"severity\":[4,4,4.1,4.2,4.3,4.4,4.5,4.6,4.7,4.8,4.9,5,5,5.1,5.2,5.3,5.4,5.5,5.6,5.7,5.8,5.9,6,6,6.1,6.2,6.3,6.4,6.5,6.6,6.7,6.8,6.9,7,7,7.1,7.2,7.3,7.4,7.5,7.6,7.7,7.8,7.9,8,8,8.1,8.2,8.3,8.4,8.5,8.6,8.7,8.8,8.9]}}"
```

### 대상 설정하기

```json
$ aws events put-targets --region ap-northeast-2 --rule "guardDuty-trigger-production" --targets "[{\"Id\": \"Id100000000000\",\"Arn\": \"arn:aws:sns:ap-northeast-2:{AWS_ACCOUNT_ID}:urbanbase-api-team\",\"InputTransformer\": {\"InputPathsMap\": {\"Finding_ID\": \"$.detail.id\",\"Finding_Type\": \"$.detail.type\",\"Finding_description\": \"$.detail.description\",\"region\": \"$.region\",\"severity\": \"$.detail.severity\"}, \"InputTemplate\": \"\\\"You have a severity <severity> GuardDuty finding type <Finding_Type> in the <region> region.\\\"\"}}]"
```
InputTemplate 는 String 값만 입력을 받는데 위 이미지처럼 InputTemplate 란에 "You have a severity <severity> GuardDuty finding type <Finding_Type> in the <region> region." 을 등록하고 싶으면  \"\\\"You have a severity <severity> GuardDuty finding type <Finding_Type> in the <region> region.\\\"\" 처럼 사용하면 됩니다. 

<br>
<br>

## Amazon GuardDuty 활성화

### Console

GuardDuty 활성화 버튼을 클릭합니다.

<img src="/assets/20_11_AmazonGuardDuty/image 06.png" width="" alt="AmazonGuardDuty 6">


### CLI

```bash
$ aws guardduty create-detector --region ap-northeast-2 --enable \
      --finding-publishing-frequency "FIFTEEN_MINUTES" \
      --data-sources "{\"S3Logs\": {\"Enable\": \"true\"}}"
```

### Node.js

```jsx
let AWS = {};

const getAWS = (region) => {
  const localAWS = require('aws-sdk');
  localAWS.config.region = region;

  // multiple profile 사용 시
  const credentials = new localAWS.SharedIniFileCredentials({ profile: 'profileId' });
  localAWS.config.credentials = credentials;
  return localAWS;
};

(async () => {
  try {
    let params;
    const region = 'ap-northeast-2';

    AWS = getAWS(region);
		const guardduty = new AWS.GuardDuty();

    console.log(`Current Region: ${region}`);
    // info: guardDuty 설정
    params = {
      Enable: true,
      FindingPublishingFrequency: 'FIFTEEN_MINUTES',
      DataSources: {
        S3Logs: {
          Enable: true,
        }
      },
      Tags: {
        region: region,
      },
    };

    const result = await guardduty.createDetector(params).promise();
    console.log(result);
	} catch (e) {
    console.error(e)
  }
})();
```
<br>
<br>

# S3 Protection (S3 보호)
기존 Amazon Macie 에서 사용할 수 있었던 Amazon S3 활동에 대한 이상 및 위협 탐지가 Amazon GuardDuty의 일부가 되면서 비용도 80% 이상 감소했다고 합니다. 이 기능을 통해 S3 데이터 액세스 이벤트 및 S3 구성을 지속적으로 모니터링하고 프로파일링하여 비정상적인 지리적 위치에서의 요청과 같은 의심스러운 활동을 감지할 수 있습니다. 또한 아래와 같은 예방 제어를 비활성화 할 수도 있습니다.

- S3 블록 퍼블릭 액세스
- 잘못 구성된 버킷 권한을 검색하려는 시도와 일치하는 API 호출 패턴

<img src="/assets/20_11_AmazonGuardDuty/image 07.png" width="" alt="AmazonGuardDuty 7">


# 샘플 결과 작성

<img src="/assets/20_11_AmazonGuardDuty/image 08.png" width="" alt="AmazonGuardDuty 8">

<img src="/assets/20_11_AmazonGuardDuty/image 09.png" width="" alt="AmazonGuardDuty 9">



# 탐지 예제
S3 버킷 하나를 생성한 뒤 버킷 정책에 익명 사용자에게 읽기 전용 권한을 부여해보았습니다.

```json
// bucket policy
{
    "Version": "2008-10-17",
    "Id": "allow for anonymouse",
    "Statement": [
        {
          "Sid":"PublicRead",
          "Effect":"Allow",
          "Principal": "*",
          "Action":["s3:GetObject"],
          "Resource":["arn:aws:s3:::sample/*"]
        }
    ]
}
```

일정 시간이 지나면 Amazon GuardDuty에서 탐지하게 되고,

<img src="/assets/20_11_AmazonGuardDuty/image 10.png" width="" alt="AmazonGuardDuty 10">


위에서 설정한 Amazon CloudWatch Events 로 트리거가 동작하여 아래와 같이 알림이 옵니다.

Amazon SNS를 통한 이메일 수신

<img src="/assets/20_11_AmazonGuardDuty/image 11.png" width="" alt="AmazonGuardDuty 11">


AWS Lambda 를 통한 텔레그램 메시지 수신

<img src="/assets/20_11_AmazonGuardDuty/image 12.png" width="" alt="AmazonGuardDuty 12">


여기까지 Amazon GuardDuty 활성화 및 Amazon CloudWatch Events 연동을 통해 알림을 수신하는 방법에 대해 알아보았습니다. 읽어주셔서 감사합니다.

# 참고 문헌
[https://aws.amazon.com/ko/blogs/aws/new-using-amazon-guardduty-to-protect-your-s3-buckets/](https://aws.amazon.com/ko/blogs/aws/new-using-amazon-guardduty-to-protect-your-s3-buckets/)

[https://docs.aws.amazon.com/ko_kr/guardduty/latest/ug/guardduty_findings_cloudwatch.html#guardduty_findings_cloudwatch_format](https://docs.aws.amazon.com/ko_kr/guardduty/latest/ug/guardduty_findings_cloudwatch.html#guardduty_findings_cloudwatch_format)