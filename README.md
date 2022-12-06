![image](https://user-images.githubusercontent.com/487999/79708354-29074a80-82fa-11ea-80df-0db3962fb453.png)

# 예제 - 음식배달

## 기능적 요구사항

1.고객이 메뉴를 선택하여 주문한다.

2.고객이 선택한 메뉴에 대해 결제한다.

3.주문이 되면 주문 내역이 입점상점주인에게 주문정보가 전달된다.

4.상점주는 주문을 수락하거나 거절할 수 있다.

5.상점주는 요리시작때와 완료 시점에 시스템에 상태를 입력한다.

6.고객은 아직 요리가 시작되지 않은 주문은 취소할 수 있다.

7.요리가 완료되면 고객의 지역 인근의 라이더들에 의해 배송건 조회가 가능하다.

8.라이터가 해당 요리를 pick한 후, 앱을 통해 통보한다.

9.고객이 주문상태를 중간중간 조회한다.

10.주문상태가 바뀔때 마다 카톡으로 알림을 보낸다.

11.고객이 요리를 배달 받으면 배송확인 버튼을 탭하여, 모든 거래가 완료된다.


추가사항
1. 고객은 결재 내역과 상태를 확인할 수 있다.

2. 라이더는 pick이 있는지 여부와 배달완료가 되었는지 확인한다.

## DDD 적용
![image](https://user-images.githubusercontent.com/118959734/205806811-d569d167-caee-4d7b-8c8d-4c07ad5e08de.png)

추가 1 : 고객은 결재내역과 상태를 확인한다.
![image](https://user-images.githubusercontent.com/118959734/205819379-6b3bf139-9286-4b43-a56b-ce2d51330591.png)

추가2 : 라이더는 pick이 있는지 여부와 배달완료가 되었는지 확인한다.
![image](https://user-images.githubusercontent.com/118959734/205819823-9e2a0997-aeb4-4f6d-838c-f6047cd050c4.png)


# 구현
## 체크포인트

### 1.Saga(Pub/Sub)
고객이 주문하고 결재가 처리되면 상점에 주문내역이 전달된다.
![image](https://user-images.githubusercontent.com/118959734/205808546-41d561a4-2ea1-41d5-96fc-b93516060759.png)

![image](https://user-images.githubusercontent.com/118959734/205808638-ee4d0c3f-0240-4e90-86dc-7529b76a757b.png)

상점에서 주문내역을 확인한다.

![image](https://user-images.githubusercontent.com/118959734/205808796-f37da38d-4215-407d-b938-66174b950678.png)
![image](https://user-images.githubusercontent.com/118959734/205808844-a320ee59-cd0b-47be-afcb-738d1fa9de7c.png)



### 2.CQRS
OrderPlace, Paid, OrderAccept, OrderReject, OrderStart, OrderFinish, Pick 와 같이 주문 상태가 변하는지 확인할 수 있다.
DDD 에서 CQRS 설정

![image](https://user-images.githubusercontent.com/118959734/205809479-57e837da-d3c4-49b0-b995-2a6aa3fb07e1.png)

CQRS 가 설정된 소스 내용
![image](https://user-images.githubusercontent.com/118959734/205810092-e92c61de-ba25-4a91-a923-7b2233d34a97.png)

상태 조회를 한 결과 내용

![image](https://user-images.githubusercontent.com/118959734/205810148-018288a4-da67-47e1-8bba-e16dacf63df0.png)


### 3.Compensation/Correlation 
주문하면 아래와 같이 payment에 저장된다.
![image](https://user-images.githubusercontent.com/118959734/205810461-818f34fb-c644-48a2-bedd-5189608de4c3.png)

![image](https://user-images.githubusercontent.com/118959734/205810817-ef75b05d-43a8-4ef0-beef-e9b31d08ac8d.png)

![image](https://user-images.githubusercontent.com/118959734/205811751-f2f9ea66-199a-4b4f-85a2-113125f08cfb.png)


### 4.Request/Response  
주문 취소시의 결제가 되어있는지 확인을 한뒤 결제가 되어있고 아직 요리를 시작하지 않았다면 결제 취소를 요청한다.

![image](https://user-images.githubusercontent.com/118959734/205816088-e164f457-3866-4952-9a1a-7f09cfef3345.png)

Request를 받아 처리하는 로직이다.

![image](https://user-images.githubusercontent.com/118959734/205816909-bfa95e90-f12b-41b2-b9ef-49dbb4ef744b.png)

### 5.Circuit Breaker  

timeout 시간 설정

![image](https://user-images.githubusercontent.com/118959734/205817607-07570e57-a781-420b-97ce-0bf8622cd74b.png)

Circuit Breaker 가 발동되면 fallback 에서 throw를 내놓도록 설정

![image](https://user-images.githubusercontent.com/118959734/205818155-53930fee-3b88-425d-9d46-3564fd639e99.png)
![image](https://user-images.githubusercontent.com/118959734/205820120-84133582-b580-4735-ac6e-18504cd470e3.png)

### 6.Gateway/Ingress  
![image](https://user-images.githubusercontent.com/118959734/205820385-b2bb32ba-f89f-44bb-ae16-0e7836f4ddfe.png)
![image](https://user-images.githubusercontent.com/118959734/205820450-4d7554e7-021e-45de-ba13-bcb300a1ccf6.png)






