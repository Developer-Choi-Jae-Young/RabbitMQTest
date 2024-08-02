# 1. [RabbitMQ 용어 정리]
![RabbitMQ 관련 이미지](https://github.com/Developer-Choi-Jae-Young/RabbitMQTest/blob/54235d387076db0eb24c2ee5a9ad7f07a683f520/%EA%B7%B8%EB%A6%BC1.png)
+ Producer : 메세지(요청)을 보내는 주체, 메세지를 Exchange 에 Publish 함

+ Consumer : Producer 로 부터 메세지를 받아 처리하는 주체

+ Exchange : Producer 로 부터 전달받은 메세지를 어떤 Queue 로 보낼지 결정하는 장소

  + 하나의 Exchange 에 1개 이상의 Queue 가 등록 됨

  + 둘 사이의 Routing Key(Binding Key) 존재

+ Queue : Consumer 가 메세지를 소비하기 전까지 메세지를 보관하는 장소

+ Binding : Exchange 와 Queue 관계를 정의 즉, Binding 에 따라서 Exchange 에 발행된 메세지가 어떤 Queue 로 갈지 결정된다.

  + 사용자가 특정 Exchange 가 특정 Queue 을 Binding 하도록 정의함

  + Binding 은 Routing Key(Binding Key) 을 수단으로 이루어짐

  + 특정 Exchange 와 Binding 된 Queue 는 해당 Exchange 에 등록되었다고 이해하면 됨

# 2. [Binding 전략]

앞서 살펴본바와 같이 Binding 이란 Producer 로 부터 메세지를 받은 Exchange 가 어떤 Queue 로 메세지를 전달할지에 대한 방식이였습니다.
-> Exchange 에 Queue 을 등록하고 Queue 에 보관되어 있는 메세지를 해당 Queue 을 바라보는 Consumer 가 소비하는 방식

여기에는 4가지 전략이 있습니다❗️

+ 🌱 Direct Exchange : 메세지의 Routing Key 와 정확히 일치하는 Binding 된 Queue 로 Routing

![RabbitMQ 관련 이미지](https://github.com/Developer-Choi-Jae-Young/RabbitMQTest/blob/54235d387076db0eb24c2ee5a9ad7f07a683f520/%EA%B7%B8%EB%A6%BC2.png)

Direct Exchange 에 따르면, 메세지에 부여된 Routing Key 와 동일한 Key 로 Binding 된 Queue 에만 전송합니다.
해당 이미지는 Direct Exchange 방식을 도식화한 이미지인데, rabbit key 을 가진 메세지는 Exchange 을 거쳐 rabbit 으로 binding 된 Queue 로 전송됩니다 👨‍💻

---
+ 🌱 Fanout Exchange : Binding 된 모든 Queue 에 메세지를 Routing

![RabbitMQ 관련 이미지](https://github.com/Developer-Choi-Jae-Young/RabbitMQTest/blob/54235d387076db0eb24c2ee5a9ad7f07a683f520/%EA%B7%B8%EB%A6%BC3.png)

Fanout Exchange 전략은, Routing Key 에 상관 없이 Exchange 에 등록된 모든 Queue 에 메세지를 전송합니다.

---
+ 🌱 Topic Exchange : 특정 Routing Patten 이 일치하는 Queue 로 Routing

![RabbitMQ 관련 이미지](https://github.com/Developer-Choi-Jae-Young/RabbitMQTest/blob/54235d387076db0eb24c2ee5a9ad7f07a683f520/%EA%B7%B8%EB%A6%BC4.png)

Topic Routing 은 Routing Key 의 패턴을 이용해 메세지를 Routing 합니다.
여러 Consumer 에서 메세지 형식에 따라 선택적으로 수신해야 하는 경우 등에 사용됩니다.

위의 이미지는 Topic Routing 을 도식화한 것인데, animal.rabbit 이라는 Routing key 로 Exchange 에 메세지가 발행되었습니다.
그러면 이와 일치하는 패턴을 가지는 Routing Key 로 Binding 된 Queue 로 메세지가 전달됩니다.
animal.rabbit 에 animal 과 # 은 일치하는 패턴이기에 정상적으로 전달됩니다❗️

---
+ 🌱 Headers Exchange : Key-Value 로 정의된 Header 속성을 통한 Routing

![RabbitMQ 관련 이미지](https://github.com/Developer-Choi-Jae-Young/RabbitMQTest/blob/54235d387076db0eb24c2ee5a9ad7f07a683f520/%EA%B7%B8%EB%A6%BC5.png)

Headers Exchange 는 앞서 살펴보았던 Topic Exchange 와 비슷하지만 Header 을 이용하는 차이점이 있습니다.
Producer 에서 정의된 Header 에 Key-Value 쌍과 Consumer 에서 정의된 Argument 의 Key-Value 쌍이 일치하면 Binding 됩니다.

|Key|Value|설명|
|:---:|:---:|:---:|
|x-match|all|header 의 모든 key-value 쌍 값과 argument의 모든 key-value 쌍이 일치할때만 Binding|
|x-match|any|header 의 key-value 쌍 값과 argument의 하나의 key-value 쌍과 일치하더라도 Binding|

# 3. [RabbitMQ 구성 요소의 세부 속성]

- Exchange 속성
+ Name : Exchange 이름
+ Type : Binding 전략(앞서 살펴본 4가지 전략)
+ Durability : 메세지 브로커가 시작될 때 남아있는지 여부
  + Durable : 메세지 브로커 서버가 재시작되어도 기존 Exchange 메세지가 저장되어 남아있음
  + Transient : 메세지 브로커 서버가 재시작되면 기존 Exchange 메세지가 모두 사라짐
+ Auto-delete : 마지막 Queue 연결이 해제되면 Exchange 삭제됨
이번 실습에서는 4가지 Binding 전략 중 일반적인 전략인 Direct Exchange 을 사용
