---
layout: post
title: "Spring MVC 실습"
date: 2021-05-12
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Spring MVC 웹 어플리케이션 실습

- DispatcherServlet을 FrontController로 설정
- 설정하는 방법은 2가지 존재
    - web.xml 파일에 설정(Servlet이기 때문)
    - org.springframework.web.WebApplicationInitializer 인터페이스를 구현
        - 이 방법은 처음 WebApplication 구동시간이 오래걸릴 수 있음

- web.xml 파일에 설정하는 방법
    - DispatcherServlet이 xml 파일이 아닌 자바 Config 파일 (kr.or.connect.mvcexam.config.WebMvcContextConfiguration 클래스)을 이용하여 사용할 수 있게끔 설정할것임
    - (복습) servlet-mapping 부분에 우리가 원하는 주소 url을 넣고 해당 부분의 요청이 들어오면 servlet-mapping의 servlet-name과 같은 name의 servlet-class를 실행
    - 그런데 Dispatcher Servlet은 url-pattern이 / 이므로 어떤 하나의 특성 요청을 받는게 아니라 `모든 요청을` 받게됨

```xml
<!-- web.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<web-app>

  <display-name>Spring JavaConfig Sample</display-name>

  <servlet>
    <servlet-name>mvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextClass</param-name>
      <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
    </init-param>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>kr.or.connect.mvcexam.config.WebMvcContextConfiguration</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>mvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>


</web-app>
```

- `Displtcher Servlet에 대한 설정`은 web.xml에서하고 Dispatcher Servlet이 `읽어들어야 할 설정`은 JavaConfig로 함
    - Dispatcher Servlet은 해당 설정 파일을 읽어들여서 내부적으로 Srping Container인 Application Context를 생성하게 됨

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = {"kr.or.connect.webmvc.controller"})

public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter{
    ...
}
```

- Annotation  살펴보기
    - @Configuration : 아 이 클래스는 Java Config 파일이구나~!
    - @EnableWebMvc : Web에 필요한 Bean들을 자동으로 설정해줌
        - 해당 Bean들은 RequestMappingHandlerMapping, RequestMappingHandlerAdapter, ExceptionHandlerExceptionResolver, MessageConverter 등이 있음
    - @ComponentScan : Spring MVC에서 Controller(핸들러)를 찾기 위해 사용