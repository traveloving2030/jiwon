---
layout: post
title: "Spring Framework"
date: 2021-05-06
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Framework란?

- 반제품이다
- 이미 만들어진 플랫폼을 가지고 우리가 살을 덧붙이면된다!

# Spring Framework

- 엔터프라이즈급 어플리케이션을 구축할 수 있는 가벼운 솔루션
- 원스-스탑-숍(One-Stop-Shop) ==> 모든 과정을 한꺼번에 해결함
- 원하는 부분만 가져다 사용할 수 있도록 모듈화
- `spring-jdb`c : 자바 JDBC프로그래밍을 쉽게 할 수 있도록 기능을 제공
- `spring-tx` : 선언적 트랜잭션 관리를 할 수 있는 기능을 제공

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/38.png" height="280" width="300" />



# 기타 Spring 개념

- IoC 컨테이너입니다.
- 선언적으로 트랜잭션을 관리할 수 있습니다.
- 완전한 기능을 갖춘 MVC Framework를 제공합니다.
- AOP 지원합니다.
- 스프링은 도메인 논리 코드와 쉽게 분리될 수 있는 구조로 되어 있습니다.


## 프레임워크 모듈
- 스프링 프레임워크는 약 20개의 모듈로 구성되어 있습니다.
- 필요한 모듈만 가져다 사용할 수 있습니다.


## AOP와 인스트루멘테이션

- spring-AOP : AOP 얼라이언스(Alliance)와 호환되는 방법으로 AOP를 지원합니다.
- spring-aspects : AspectJ와의 통합을 제공합니다.
- spring-instrument : 인스트루멘테이션을 지원하는 클래스와 특정 WAS에서 사용하는 클래스로 더 구현체를 제공합니다. 참고로 BCI(Byte Code Instrumentations)은 런타임이나 로드(Load) 때 클래스의 바이트 코드에 변경을 가하는 방법을 말합니다.


## 메시징(Messaging)

- spring-messaging : 스프링 프레임워크 4는 메시지 기반 어플리케이션을 작성할 수 있는 Message, MessageChannel, MessageHandler 등을 제공합니다. 
- 또한, 해당 모듈에는 메소드에 메시지를 맵핑하기 위한 어노테이션도 포함되어 있으며, Spring MVC 어노테이션과 유사합니다.


## 데이터 엑서스(Data Access) / 통합(Integration)

- 데이터 엑세스/통합 계층은 JDBC, ORM, OXM, JMS 및 트랜잭션 모듈로 구성되어 있다.
- spring-jdbc : 자바 JDBC프로그래밍을 쉽게 할 수 있도록 기능을 제공합니다.
- spring-tx : 선언적 트랜잭션 관리를 할 수 있는 기능을 제공합니다.
- spring-orm : JPA, JDO및 Hibernate를 포함한 ORM API를 위한 통합 레이어를 제공합니다.
- spring-oxm : JAXB, Castor, XMLBeans, JiBX 및 XStream과 같은 Object/XML 맵핑을 지원합니다.
- spring-jms : 메시지 생성(producing) 및 사용(consuming)을 위한 기능을 제공, Spring Framework 4.1부터 - spring-messaging모듈과의 통합을 제공합니다.


## 웹(Web)

- 웹 계층은 spring-web, spring-webmvc, spring-websocket, spring-webmvc-portlet 모듈로 구성됩니다.
- spring-web : 멀티 파트 파일 업로드, 서블릿 리스너 등 웹 지향 통합 기능을 제공한다. HTTP클라이언트와 Spring의 원격 지원을 위한 웹 관련 부분을 제공합니다.
- spring-webmvc : Web-Servlet 모듈이라고도 불리며, Spring MVC 및 REST 웹 서비스 구현을 포함합니다.
- spring-websocket : 웹 소켓을 지원합니다.
- spring-webmvc-portlet : 포틀릿 환경에서 사용할 MVC 구현을 제공합니다.