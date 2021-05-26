---
layout: post
title: "Layered Architecture"
date: 2021-05-13
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Controller에서 중복되는 부분을 처리하려면?
- 쇼핑몰에서 게시판에서도 회원 정보를 보여주고, 상품 목록 보기에서도 회원 정보를 보여줘야 한다면 회원 정보를 읽어오는 코드는 어떻게 해야 할까?
    - 별도의 객체로 분리
    - 별도의 메소드로 분리

# 컨트롤러와 서비스
- 비지니스 메소드를 별도의 `Service객체`에서 구현하도록 하고 컨트롤러는 Service객체를 사용하도록 구현
- 그림의 상품 서비스는 Controller1, Controller3 모두 사용
    - 이때는 Controller1, Controller3에 각각 구현하는 것이 아닌 `상품과 관련된 비지니스만 처리하는 서비스 객체`를 따로 만듦
    - 이러면 Controller1, Controller3 모두 사용할 수 있음

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/48.png" height="280" width="300" />


# 서비스 객체
- 비지니스 로직(Business logic)을 수행하는 메소드를 가지고 있는 객체
- 보통 하나의 비지니스 로직은 하나의 트랜잭션으로 동작


# 트랜잭션
- 트랜잭션의 특징은 크게 4가지로 구분
    - 원자성 (Atomicity)
    - 일관성 (Consistency)
    - 독립성 (Isolation)
    - 지속성 (Durability)

- 원자성 : `전체가 성공하거나 전체가 실패`
    - e.g) 출금 기능의 흐름이 다음과 같다고 생각
        - 잔액이 얼마인지 조회한다.
        - 출금하려는 금액이 잔액보다 작은지 검사한다.
        - 출금하려는 금액이 잔액보다 작다면 (잔액 - 출금액)으로 수정한다.
        - 언제, 어디서 출금했는지 정보를 기록한다.
        - 사용자에게 출금한다.
    - 4번에서 오류가 발생했다면, 앞의 작업을 `모두 원래대로 복원 (Roll Back)`
    - 5번까지 모두 성공했을 때만 정보를 `모두 반영 (Commit)`
    - 이렇게 rollback 하거나 commit을 하게 되면 하나의 트랜잭션 처리가 완료

- 일관성 : `트랜잭션의 작업 처리 결과가 항상 일관성이 있어야 한다는 것`
    - 트랜잭션이 진행되는 동안에 데이터가 변경되더라도 업데이트된 데이터로 트랜잭션이 진행되는 것이 아니라, 처음에 트랜잭션을 진행하기 위해 참조한 데이터로 진행
    - 이렇게 함으로써 각 사용자는 일관성 있는 데이터를 볼 수 있는 것

- 독립성 :  둘 이상의 트랜잭션이 동시에 병행 실행되고 있을 경우에 `어느 하나의 트랜잭션이라도 다른 트랜잭션의 연산을 끼어들 수 없음`
    - A사용자가 입력, 삭제, 수정하는 일들을 수행하고 있었고 B사용자도 이러한 일들을 수행할 때, A사용자가 하고있는 작업들이 Commit, Rollback 하는 트랜잭션을 완료하기 전에는 B는 해당 부분에 대해 알 수 없음

- 지속성 :  `트랜잭션이 성공적으로 완료되었을 경우, 결과는 영구적으로 반영되어야 한다는 점`


# JDBC 프로그래밍에서 트랜잭션 처리 방법
- 여러개의 작업이 성공하여 `끝났을 때만` Commit을 반영하겠다
    - Connection객체의 setAutoCommit메소드에 false를 파라미터로 지정
    - Default로는 true로 설정되어있음
- 따라서 입력, 수정, 삭제 SQL이 실행을 한 후 모두 성공했을 경우 Connection이 가지고 있는 commit()메소드를 호출됨

## @EnableTransactionManagement
- Spring Java Config파일에서 트랜잭션을 활성화 할 때 사용하는 애노테이션
- Java Config를 사용하게 되면 PlatformTransactionManager 구현체를 모두 찾아서 그 중에 하나를 매핑해 사용
- 특정 트랜잭션 메니저를 사용하고자 한다면 TransactionManagementConfigurer를 Java Config파일에서 구현하고 원하는 트랜잭션 메니저를 리턴
- 아니면, 특정 트랜잭션 메니저 객체를 생성시 @Primary 애노테이션을 지정


# 서비스 객체에서 중복으로 호출되는 코드의 처리
- 데이터 엑세스 메소드를 별도의 Repository(Dao) 객체에서 구현하도록 하고 Service는 Repository객체를 사용
    - 서비스 객체들(Repository)마다 비지니스 메소드를 가지고 있음
    - 하나의 비지니스 메소드는 트랜잭션 단위로 작업이 처리됨
    - 하나의 트랜잭션에는 여러개의 DB 작업이 수행될 수 있음

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/49.PNG" height="280" width="300" />


# 전체 아키텍처
- `Presentation Layer` : `Controller 객체가 동작`하게함 (지금 과정은 Web에 보이게 만듦)
    - 만약, Presentation Layer를 Window Programming으로 바꾸고 싶으면 ServiceLayer와 Repository Layer은 그대로 두고 Presentation Layer만 바꿔주면 됨
    - 즉, `유지보수`가 매우 용이!
- `Service Layer` : `비지니스 메소드를 가지고 있는 Service 객체가 동작`하게 함
- `Repository Layer` : `실제 데이터베이스에 접근해서 데이터를 가져오는 일`들 수행
    - Service Layer는 Repository Layer에 있는 DAO 객체를 사용하게 됨
- 우리는 개발하면서 `이 로직을 어떤 Layer에 놓을까`를 고민해야함!

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/50.png" height="280" width="300" />


# 설정의 분리
- Spring 설정 파일을 프리젠테이션 레이어쪽과 나머지를 분리
- web.xml 파일에서 프리젠테이션 레이어에 대한 스프링 설정은 DispathcerServlet이 읽도록함
- 그 외의 설정은 ContextLoaderListener를 통해서 읽도록함
- DispatcherServlet을 경우에 따라서 2개 이상 설정할 수 있는데 이 경우에는 각각의 DispathcerServlet의 ApplicationContext가 각각 독립적이기 때문에 각각의 설정 파일에서 생성한 빈을 `서로 사용할 수 없음`
    - 동시에 필요한 빈은 ContextLoaderListener를 사용함으로써 공통으로 사용하게 할 수 있음
- ContextLoaderListener와 DispatcherServlet은 각각 ApplicationContext를 생성
    - ContextLoaderListener가 생성하는 ApplicationContext가 root컨텍스트가 되고
    - DispatcherServlet이 생성한 인스턴스는 root컨텍스트를 부모로 하는 자식 컨텍스트가 됨
    - 자식 Context들은 Root Context의 설정을 사용할 수 있음! (자식 컨텍스트들은 root컨텍스트의 설정 빈을 사용할 수 있음)

