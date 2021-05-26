---
layout: post
title: "Spring MVC"
date: 2021-05-11
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# MVC란?

- Model : 모델은 뷰가 렌더링하는데 필요한 데이터. 예를 들어 사용자가 요청한 상품 목록이나, 주문 내역이 이에 해당
- View : 웹 애플리케이션에서 뷰(View)는 실제로 보이는 부분이며, 모델을 사용해 렌더링함. 뷰는 JSP, JSF, PDF, XML등으로 결과를 표현
- Controller : 컨트롤러는 사용자의 액션에 응답하는 컴포넌트. 컨트롤러는 모델을 업데이트하고, 다른 액션을 수행함

# MVC Model1 아키텍처

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/41.png" height="280" width="300" />

- 브라우저가 요청을 하면 해당 요청을 JSP가 받게됨(요청만큼 jsp 페이지가 존재)
- JSP는 RoleDAO와 같은 JavaBean을 이용하여 DB를 사용하게 됨
- 그 결과를 화면에 출력함
- 이 방식의 문제점
    - JSP 코드에 HTML, Java 코드가 다 섞여 있음(유지보수가 어려움)

# MVC Model2 아키텍처

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/42.png" height="280" width="300" />

- Model1 아키텍처의 보완
- 브라우저의 요청을 Servlet이 받게 함
- Servlet이 JavaBean을 이용하여 DB에서 데이터를 꺼내오고 그 결과들을 JSP를 통해 결과를 화면에 보여줌
    - `Servlet : Controller`
    - `JSP : View (Model의 결과를 화면에 보여줌)`
    - `JavaBean : Model (주문내역, 상품목록)`


# MVC Model2 발전 형태

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/43.png" height="280" width="300" />

- 클라이언트가 보내는 모든 요청을 프론트 컨트롤러라 하는 Serlvet Class가 다 받음(이때, 이 Servlet은 딱 하나만 존재)
- 프론트 컨트롤러는 요청만받고 실제 일은 하지 않는데, 실제일은 컨트롤러 클래스(핸들러 클래스)에게 위임
- 컨트롤러는 JavaBean들을 이용하여 결과를 만들어내고 만들어진 결과를 Model에 담고 프론트 컨트롤러에게 보냄
- 프론트컨트롤러는 알맞은 View에게 모델을 전달하고 그 결과를 출력하게 됨

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/44.png" height="280" width="300" />



# Spring MVC 기본 동작 흐름 (중요!!!)

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/45.png" height="280" width="300" />

- 색깔별 분류
    - 파란색 : Database를 제외한 나머지 것들은 SpringMVC가 제공해주는것들
    - 보라색 : 개발자가 만들어야하는 부분
    - 녹색 : Spring이 제공하는 부분도 있고 개발자가 만들어야하는 부분도 존재

- 동작순서
    - ①,② 클라이언트가 요청을 보내면 이 보낸 모든 요청을 Dispatcher Servlet Class가 받음
      - Dispatcher Servlet은 요청을 처리해줄 Controller와 메소드가 무엇인지 Handler Mapping에게 물어봄. 
      - 이때, Handler Mapping이 혼자서 알아낼수는 없기 때문에 SpringMVC로 개발할때 어떤 요청에 어느 컨트롤러가 동작할지를 XML파일 또는 클래스파일에 Annotation으로 설정을 해주어야함
      - 이런 정보들을 Spring으로 만들어진 웹 어플리케이션이 실행할 때 Handler Mapping 객체들이 생성되면서 관리를 함
    - ③,④ Handler Adapter에게 실행 요청. 그때 결정된 Controller와 해당 메소드가 실행됨
    - ⑤ 그 결과를 Model에 받아서 Dispatcher Servlet에 전달
        - 이때 Dispatcher Servlet은 Controller가 Return한 View Name을 알아오게 됨
    - ⑥,⑦ Controller가 Return한 View Name을 가지고 적절한 Resolver를 통해 View 출력(응답)

- Spring MVC는 Dispatcher Servlet이 어떻게 동작하는지 이해하는 것이 중요!
    - Dispatcher Servlet이 요청을 받아서 결과를 출력하기까지 아래의 컴포넌트를 사용하게 될 것


# Dispatcher Servlet

- 프론트 컨트롤러이다
    - 회사로 전화걸면 내선 대표(총무팀)에서 받아 연결해주는 것과 같은 원리
    - 즉, 요청만 받고 일은 핸들러에게 넘겨버림
- 클라이언트의 모든 요청을 받은 후 이를 처리할 핸들러에게 넘기고 핸들러가 처리한 결과를 받아 사용자에게 응답 결과를 보여줌
- DispathcerServlet은 여러 컴포넌트를 이용해 작업을 처리
- https://www.boostcourse.org/web316/lecture/254347?isDesc=false 참고



# Spring MVC 웹 어플리케이션 환경설정


1. Maven Project생성
    - maven-archetype-webapp
    - Group Id : kr.or.connect
    - ArtifactId : mvcexam

2. Navigator에 src/main 폴더 내 java 폴더 생성

3. pom.xml 수정

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>kr.or.connect</groupId>
<artifactId>mvcexam</artifactId>
<packaging>war</packaging>
<version>0.0.1-SNAPSHOT</version>
<name>mvcexam Maven Webapp</name>
<url>http://maven.apache.org</url>
<properties>
<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
<spring.version>4.3.5.RELEASE</spring.version>
</properties>

<dependencies>
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>3.8.1</version>
<scope>test</scope>
</dependency>

<!-- Spring -->
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-context</artifactId>
<version>${spring.version}</version>
</dependency>
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-webmvc</artifactId>
<version>${spring.version}</version>
</dependency>

<!-- Servlet JSP JSTL -->
<dependency>
<groupId>javax.servlet</groupId>
<artifactId>javax.servlet-api</artifactId>
<version>3.1.0</version>
<scope>provided</scope>
</dependency>
<dependency>
<groupId>javax.servlet.jsp</groupId>
<artifactId>javax.servlet.jsp-api</artifactId>
<version>2.3.1</version>
<scope>provided</scope>
</dependency>
<dependency>
<groupId>jstl</groupId>
<artifactId>jstl</artifactId>
<version>1.2</version>
</dependency>

</dependencies>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-compiler-plugin</artifactId>
<version>3.6.1</version>
<configuration>
<source>1.8</source>
<target>1.8</target>
</configuration>
</plugin>
</plugins>
</build>
</project>

```

4. Maven Update

5. Navigator 탭의 .settings에서 org.eclipse.wst.common.project.facet.core.xml에서 jst.web version 2.3 => `3.1로 수정`

6. 이클립스 재시작

7. 프로젝트 Properties 에서 Project Facets에서 Dynamic Web Module이 3.1로 잘 바뀌어져 있는지 확인


# 404 에러 뜨는 원인

 ## web.xml 오류

  - web.xml의 web-app태그에 xml 스키마 정의가 필요함
  - 태그시작부분을 아래와 같이 삽입해줘야 함

    ```xml
    <web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
    ```
  - `web.xml을 수정했을 때`는 실행중이던 Servers 탭에서 해당 프로젝트를 삭제 후 Project 탭에서 Clean 하고 다시 서버 실행해야함

  
  ## pom.xml 오류

  - Maven 프로젝트 우클릭 후 Maven 탭에서 Update Project 해줘야함!