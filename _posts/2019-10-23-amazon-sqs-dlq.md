---
layout: post
title: "Amazon SQS 에서 배달 못한 편지 처리"
#description: ""
date: 2019-10-23
tags: [aws]
comments: true
share: true
---

# Amazon SQS 에서 배달 못한 편지 처리

현재 웹 애플리케이션을 개발하는 환경에서는 비동기 메세지 기반 통신을 위해 AWS의 완전 관리형 서비스를 사용하고 있다.

각 서비스는 무언가 발생했을 때 이벤트를 게시하고 다른 서비스는 이 이벤트를 인식하고 어떠한 처리를 해야 한다. 이때, 이벤트에 관심 있는 서비스가 N개 이기 때문에 팬아웃 메시징을 구현해야 하는데 AWS의 SNS와 SQS 조합을 통해 어렵지 않게 적용할 수 있다.

이제 이벤트에 관심 있는 서비스는 SQS를 폴링하며 새로운 메시지가 도착하면  소비할 수 있는데 여러 가지 이유로 메시지를 제때 소비 못 할 수 있다.

이 글에서는 제때 소비하지 못한 메시지를 다시 소비 시도 할 수 있는 방법을 다룬다.

## AWS SQS 의 배달 못한 편지 대기열 설정
![](https://docs.aws.amazon.com/ko_kr/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview.png)
> 그림 출처 - AWS 개발자 안내서: 기본 Amazon SQS 아키텍처

이미 AWS 개발자 안내서에 잘 설명되어 있지만 요약하면 SQS는 중복성과 고가용성을 위해 여러 대의 서버에 메시지 사본을 저장하고 메시지의 최소 1회 전송을 보장한다. 이말인 즉, 1회 이상 전송 될 수도 있다는 말이다. 따라서 개발자 안내서에서는 소비자 측에서 동일한 **메시지를 두 번 이상 소비해도 멱등 하도록 설계**할 것을 권한다.

반면, 메시지를 생산자나 소비자 측의 문제로 메시지를 제때 소비 못 할 수도 있다. SQS는 메시지를 자동으로 삭제하지 않고 소비자가 SQS에 메시지를 삭제하라고 알려야 한다. 생산자가 보낸 메시지가 약속한 형식과 다르거나 소비자가 메시지를 제대로 소비하지 못하면 메시지는 SQS와 소비자 사이에서 영원히 오갈 것이다.
이를 해결하기 위해 SQS는 특정 횟수 이상 소비하지 못한 메시지를 배달 못 한 편지 대기열(이하 dlq)로  전달되도록 할 수 있고, 이를 통해 문제가 있는 메시지를 디버깅 할 수 있는데 이는 또 다른 문제를 유발한다.

위에서 예로 들었던 생산자와 소비자가 문제가 아니지만 다른 여러가지 이유로 일시적으로 메시지를 소비 못 했는데 재시도 횟수가 초과하여 메시지가 dlq로 전달되어 버릴 수 있기 때문이다. 따라서 dlq로 전달된 메시지를 다시 소비 시도해볼 방법이 필요했다.


## 배달 못한 편지 소비하기
### 방법1. Dead letter Queue(이하 dlq) Consume
처음 생각했던 방법은 큐를 세 벌 구성하는 것이다. dlq 역시 SQS대기열이기에 소비자 측에서는 큐에서 메시지를 소비하듯 dlq에서 메시지 소비를 시도하고 이 역시 실패하면 dlq 의 dlq(이하 파킹큐) 에 최종적으로 메시지가 도착하는 형태다. 이런 처리를 생각한 이유는 (1) 개발자가 직접 확인/처리하는 일을 최소화 (2) SQS 는 최소 요금 없이 매달 1백만 건까지 무료라는 점 때문이다. 하지만 이게 정말 대안이 될까?
애초에 dlq에서 다시 메시지를 소비하려 한 이유는 일시적으로 소비 못 했을 때 재시도 하기 위함이다. 이 상황의 해결 여부도 모르는데 dlq의 메시지를 또 꺼내와 소비하려 한다면? 다음 시나리오를 생각해 보자.

- 소비자는 메시지를 수신하면 외부 서비스 A와 한 번 통신한다.
- 그런데 외부 서비스 A가 일시적으로 응답을 제대로 주지 못한다.
- 이로 인해 메시지를 소비하지 못한다.
- 큐와 dlq 의 재시도 횟수가 각 5번일 때, 한 메시지 당 10번의 API 호출이 일어난다.

그럼 메시지 N개에 N*10번의 A 서비스 호출이 일어나는데 이는 현재 문제를 해결하기 위한 좋은 방법이 아닌 것 같다.

### 방법2. Re-queue from Dead letter Queue
두 번째 생각한 방법은 dlq에서 메시지를 꺼내 다시 큐에 넣는 방법이다. 그러면 소비자는 메시지를 다시 꺼내 시도할 것이다. 이는 모두 수동적인데 dlq에 메시지가 도착하면 개발자는 알람을 받고 수동으로 re-queue 하는 로직을 실행한 후에도 소비가 안 되면 디버깅을 해봐야 한다. 메시지가 문제인지 다른 무엇인가 문제가 있는지.
하지만 더 좋은 방법이 떠오르지 않고 발생 빈도가 잦지 않았기 때문에 이 방법으로 구현했다.


## AWS SDK 를 통한 Re-queue
이제 해야 할 일은
1. dlq 에서 메시지를 읽어온다.
2. 큐에 다시 전송한다.
3. dlq 에서 메시지를 삭제한다.

Java 를 기준으로 AWS에서 제공하는 SDK를 사용하면 이 역시 어렵지 않게 구현할 수 있다. AmazonSQS 가 제공하는 다음 API 들을 사용하면 된다.
- getQueueUrl
- receiveMessage
- sendMessage
- deleteMessage

### 문제점
`receiveMessage(ReceiveMessageRequest receiveMessageRequest)` 를 통해서 큐로부터 한번 요청에 메시지를 최대 10개까지 수신할 수 있다. 이 때 주의할 점이 있는데, 해당 API 의 주석에 잘 명시되어 있다.

> Short poll is the default behavior where a weighted random set of machines is sampled on a ReceiveMessage call. Thus, only the messages on the sampled machines are returned. If the number of messages in the queue is small (fewer than 1,000), you most likely get fewer messages than you requested per ReceiveMessage call. If the number of messages in the queue is extremely small, you might not receive any messages in a particular ReceiveMessage response. If this happens, repeat the request.

내용인즉슨, 짧은 폴링을 할 때 메시지 사본이 저장된 여러 서버 중 가중치에 따라 샘플링된 일부 머신 세트에서 메시지를 가져오는데 해당 세트에 요청한 수보다 적은 메시지가 있을 수 있다. 즉, 메시지를 10개를 요청했지만 샘플링된 머신 세트에 메시지가 3개 밖에 없다면 이것만 얻어올 수 있는 것이다.

긴 폴링을 통해 이 문제를 해결할 수 있는 것 같지만 현재 환경의 SQS 설정 정책상  긴 폴링을 활성화하지 않기 때문에 이는 검증해보지 못했다.

대안으로  `getQueueAttributes` 를 사용했다. 이 요청을 통해 큐의 여러 속성 예컨대, 지연설정, 재전송정책 등 여러 속성을 조회할 수 있는데 그 중 `ApproximateNumberOfMessages` 라는 속성을 통해 큐에 남아 있는 메시지 근사치를 얻어올 수 있다. 이름에서 드러나듯 이 근사치이기 때문에 이 역시 정확하지 않을 수 있다. 하지만 내가 테스트한 환경에서는 이 요청을 통해 dlq 에 있는 모든 메시지를 re-queue 할 수 있었다.

```java
do {
    // do something

    if (chunkSize > receiveMessages.size() && 0 >= approximateNumberOfMessages) {
        doNext = false;
    }
    
    //  do something
} while (doNext);
```




## 참고
- [비동기 메시지 기반 통신 - Microsoft Docs](https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/architect-microservice-container-applications/asynchronous-message-based-communication)
- [기본 Amazon SQS 아키텍처 - Amazon Simple Queue Service](https://docs.aws.amazon.com/ko_kr/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-architecture.html)
- [Amazon SQS 메시지 전송, 수신 및 삭제 - Java용 AWS SDK](https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/examples-sqs-messages.html)
- [Amazon SQS 짧은 폴링 및 긴 폴링 - Amazon Simple Queue Service](https://docs.aws.amazon.com/ko_kr/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)
