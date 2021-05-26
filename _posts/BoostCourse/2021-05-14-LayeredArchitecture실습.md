---
layout: post
title: "Layered Architecture 실습"
date: 2021-05-14
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Layered Architecture 실습1 - 방명록 만들기
- SpringJDBC를 이용한 DAO 작성
- Controller + Service + DAO
- 트랜잭션 처리
- SpringMVC에서 폼 값 입력받기
- SpringMVC에서 redirect하기
- Controller에서 jsp에게 전달한 값을 JSTL과 EL을 이용하여 출력하기

## 방명록 요구사항 1/6
- 방명록 정보는 guestbook 테이블에 저장
- id는 자동으로 입력됨
- id, 이름, 내용, 등록일을 저장

## 방명록 요구사항 2/6
- http://localhost:8080/guestbook 을 요청하면 자동으로 /guestbook/list로 리다이렉팅한다
- 방명록이 없으면 건수는 0이 나오고 아래에 방명록을 입력하는 폼이 보여짐

## 방명록 요구사항 3/6
- 이름과 내용을 입력하고 등록버튼을 누르면 /guestbook/write URL로 입력한 값을 전달하여 저장
- 값이 저장된 이후에는 /guestbook/list로 리다이렉트됨

## 방명록 요구사항 4/6
- 입력한 정보가 보여짐
- 방명록 내용과 폼 사이의 숫자는 방명록 페이지 링크. (방명록 5건당 1페이지로 설정)

## 방명록 요구사항 5/6
- 방명록 6건 입력되자 페이지 수가 2건 보여짐
    - /guestbook/list?start=0 을 요청하고, 2페이지를 누르면 /guestbook/list?start=5 를 요청하게 됨
    - /guestbook/list 는 /guestbook/list? start=0과 같음

## 방명록 요구사항 6/6
- 방명록에 글을 쓰거나, 방명록의 글을 삭제할 때는 Log테이블에 클라이언트의 ip주소, 등록(삭제) 시간, 등록/삭제(method칼럼) 정보를 데이터베이스에 저장
- 사용하는 테이블은 log임
- id는 자동으로 입력되도록 함

## 방령록 클래스 다이어그램(중요!!)
- 웹 레이어 설정 파일 : web.xml, WebMvcContextConfiguration.java
- 비지니스, 레파지토리 레이어 설정파일 : ApplicationConfig.java, DBConfig.java
    - web.xml 에는 2개의 Java Config파일 설정
        - DispatcherServlet이 읽어들이는 WebMvcContextConfiguration.java 설정파일
        - ApplicationContextListener가 읽어들일 ApplicationConfig.java 설정파일
            - ApplicatoinConfig.java 설정파일은 DBConfig.java 파일을 Import할 것


<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/51.PNG" height="280" width="300" />


## 방명록 웹 어플리케이션 전체 아키텍처

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/52.jpg" height="280" width="300" />

- (1) URL 요청을 처리하는 핸들러인 GuestbookController가 있음
- (2) GuestbookController는 비지니스 로직을 가지고 있는 Guestbook Service 객체를 사용함
    - (2-1)해당 서비스 객체는 Guestbook Service 라는 인터페이스와 GuestbookServiceImpl 라는 구현 클래스로 구성
- (3) GuestbookServiceImpl에서는 LogDao와 GuestbookDao를 이용하여 비지니스 로직을 수행함
    - 이때, 비지니스 로직은 하나의 트랜잭션 단위로 실행됨
    - LogDao는 저장만할 뿐 나머지 작업은 수행하지 않음. 따라서 별도의 SQL은 필요하지 않음
    - (3-1) GuestbookDao에서는 여러개의 SQL작업을 수행하기 때문에 별도의 SQL파일들이 필요함
        - 이런 sql들은 GuestbookDaoSqls 파일에 SQL을 관리함
- (4) 각각 사용할 DTO 필요
- (5) View의 역할을 수행하기 위해 list.jsp 작성
- (6) index.jsp는 Redirect하는 코드만 나오게 될 것