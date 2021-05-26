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

- 웹 브라우저에서 http://localhost:8080/mvcexam/plusform 이라고 요청을 보내면 서버는 웹 브라우저에게 2개의 값을 입력받을 수 있는 입력 창과 버튼이 있는 화면 출력
- 웹 브라우저에 2개의 값을 입력하고 버튼을 클릭하면 http://localhost:8080/mvcexam/plus URL로 2개의 입력값이 POST 방식으로 서버에게 전달한다. 서버는 2개의 값을 더한 후, 그 결과값을 JSP에게 request scope으로 전달하여 출력


  ## DispatcherServlet을 FrontController로 설정
- 설정하는 방법은 2가지 존재
    - web.xml 파일에 설정(Servlet이기 때문)
    - org.springframework.web.WebApplicationInitializer 인터페이스를 구현
        - 이 방법은 처음 WebApplication 구동시간이 오래걸릴 수 있음

- web.xml 파일에 설정하는 방법
    - DispatcherServlet이 xml 파일이 아닌 자바 Config 파일 (kr.or.connect.mvcexam.config.WebMvcContextConfiguration 클래스)을 이용하여 사용할 수 있게끔 설정할것임
    - (복습) servlet-mapping 부분에 우리가 원하는 주소 url을 넣고 해당 부분의 요청이 들어오면 servlet-mapping의 servlet-name과 같은 name의 servlet-class를 실행
    - 그런데 Dispatcher Servlet은 url-pattern이 / 이므로 어떤 하나의 특성 요청을 받는게 아니라 `모든 요청을` 받게됨
    - 아래 코드는 web.xml의 일부(전체 코드는 밑에 있음)

```xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app>
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


# Controller(Handler) 클래스 작성하기

- @Controller 어노테이이션을 클래스 위에 붙임
  - 이게 Controller 구나 하는것을 ComponentScan이 읽어들여 Spring Container가 관리할 수 있도록 함
- Controller 클래스 작성시 클래스나 매소드 위에 @RequestMapping 사용
  - 요청이 들어왔을 때 어떤 URL로 들어온 요청인지 알아내서 실제로 처리해야 하는 Controller가 무엇인지 알아내야함
  - Servlet Mapping URL 패턴 지정하는 부분과 같은것!

  ## @RequestMapping

  - Http 요청과 이를 다루기 위한 Controller 의 메소드를 연결하는 어노테이션
  - Http Method 와 연결하는 방법
    - @RequestMapping(value="/users", method=RequestMethod.POST)
    - From Spring 4.3 version 
      - @GetMapping
      - @PostMapping
      - @PutMapping
      - @DeleteMapping
      - @PatchMapping
  - Http 특정 해더와 연결하는 방법
    - @RequestMapping(method = RequestMethod.GET, headers = "content-type=application/json")
  - Http Parameter 와 연결하는 방법
    - @RequestMapping(method = RequestMethod.GET, params = "type=raw")
  - Content-Type Header 와 연결하는 방법
    - @RequestMapping(method = RequestMethod.GET, consumes = "application/json")
  - Accept Header 와 연결하는 방법
    - @RequestMapping(method = RequestMethod.GET, produces = "application/json")



# 실습

- mvcexam 프로젝트에서 패키지 생성 (kr.or.connect.mvcexam.config 패키지)
- WebMvcContextConfiguration.java 클래스 생성
  - 이 클래스 파일은 `disPatcher Servlet이 실행될 때 읽어들이는 설정파일`임!

```java
package kr.or.connect.mvcexam.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration
@EnableWebMvc
@ComponentScan(basePackages = { "kr.or.connect.mvcexam.controller" })
public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {
	@Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/assets/**").addResourceLocations("classpath:/META-INF/resources/webjars/").setCachePeriod(31556926);
        registry.addResourceHandler("/css/**").addResourceLocations("/css/").setCachePeriod(31556926);
        registry.addResourceHandler("/img/**").addResourceLocations("/img/").setCachePeriod(31556926);
        registry.addResourceHandler("/js/**").addResourceLocations("/js/").setCachePeriod(31556926);
    }
 
    // default servlet handler를 사용하게 합니다.
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
   
    @Override
    public void addViewControllers(final ViewControllerRegistry registry) {
    		System.out.println("addViewControllers가 호출됩니다. ");
        registry.addViewController("/").setViewName("main");
    }
    
    @Bean
    public InternalResourceViewResolver getInternalResourceViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
}
```

  - @EnableWebMvc를 이용하면 기본적인 설정이 모두 자동으로 되지만, 기본 설정 이외의 설정이 필요하다면 WebMvcConfigurerAdapter 를 상속받도록 Java config class를 작성한 후, 필요한 메소드를 오버라이딩 하도록 한다.
  - ComponentScan에서 basePackages는 여러개 있으면 중괄호 안에 넣어서 찾아낼수 있도록 할 수 있음
  - addResourceHandlers 부분이 필요한 이유
    - web.xml에서 모든 요청이 들어왔을 때 DispatcherServlet이 실행하게 해줘요 라고 설정해주었음
    - 그런데 이때 들어오는 모든 요청중에는 url에 매핑되어 있는것 뿐만 안리ㅏ CSS, Image, HTML 등등도 포함됨
      - 이런 요청들은 root 디렉토리를 명시해줌으로서 여기서 찾아요~라고 명시해줌
    
    ```java
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/assets/**").addResourceLocations("classpath:/META-INF/resources/webjars/").setCachePeriod(31556926);
        registry.addResourceHandler("/css/**").addResourceLocations("/css/").setCachePeriod(31556926);
        registry.addResourceHandler("/img/**").addResourceLocations("/img/").setCachePeriod(31556926);
        registry.addResourceHandler("/js/**").addResourceLocations("/js/").setCachePeriod(31556926);
    }
    ```

  - DefaultServletHandlers 메소드 : 매핑 정보가 없는 URL 요청은 Spring의 DefaultServletHttpRequestHandler가 처리하도록 해줌
    - Spring의 DefaultServletHttpRequestHandler는 WAS의 DefaultServelt에게 해당 일을 넘기게 됨
    - WAS는 DefaultServlet이 Static한 자원을 읽어서 보여주게 해줌
  
  ```java
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
  ```

  - addViewControllers 메소드 : 특정 URL에 대한 처리를 Controller 클래스를 작성하지 않고 매핑할 수 있도록 해줌
    - 요청 자체가 / 로 들어오면 main이라는 View로 보여주도록 함
    - 그럼 main View는 어디에서 찾아내는가?
      - ViewResolver() 객체에서 찾음 => main이라는 정보만 가지고는 View 정보를 찾을 수 없음
      - View 정보는 getInternalResourceViewResolver() 메소드에서 설정된 형태로 View를 사용하게 됨
      - 결국 `이 View는 WEB-INF/views 밑에 main.jsp 파일`임!
  
  ```java
    @Override
    public void addViewControllers(final ViewControllerRegistry registry) {
    		System.out.println("addViewControllers가 호출됩니다. ");
        registry.addViewController("/").setViewName("main");
    }
    
    @Bean
    public InternalResourceViewResolver getInternalResourceViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
  ```

  - 결론 : 이 `WebMvcContextConfiguration 클래스 내 설정들을 읽어들여서 Dispatcher Servlet이 동작을 하게 될 것!`


- 다음으로, web.xml에 `DispatcherServlet이 실제로 동작을 하게하기 위해` DispatcherServlet을 `Front Servlet으로 등록`해줘야 함

```xml
 <web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">

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
  - 모든 요청 /이 들어오면 servlet-name (mvc)와 같은 이름을 가진 org.springframework.web.servlet.DispatcherServlet 클래스를 `프론트 컨트롤러`로 할거에요
  - `DispatcherServlet이 실행될 때` WebMvcContextConfiguration `자바 Config 파일을 읽어내는 부분`이 다음과 같음

  ```xml
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>kr.or.connect.mvcexam.config.WebMvcContextConfiguration</param-value>
    </init-param>
  ```

  - 잘 동작하는지 확인
    - WEB-INF/views 폴더 생성후 밑에 main.jsp 파일 생성
    - `mvcexam 마우스 우클릭 후 run on server (톰켓실행)`

```jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<h1>Hello~!!!</h1>
</body>
</html>
```



# 실습1

- 웹 브라우저에서 http://localhost:8080/mvcexam/plusform 이라고 요청을 보내면 서버는 웹 브라우저에게 2개의 값을 입력받을 수 있는 입력 창과 버튼이 있는 화면 출력

- views 폴더 내에 plusForm.jsp, plusResult.jsp 파일 생성

```jsp
<!-- plusForm.jsp -->
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
<form method="post" action="plus">  
value1 : <input type="text" name="value1"><br>
value2 : <input type="text" name="value2"><br>
<input type="submit" value="확인">  
</form>  
</body>
</html>
```

```jsp
<!-- plusResult.jsp -->
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
${value1} 더하기 ${value2} (은/는) ${result} 입니다.
</body>
</html>
```

- Controller 생성 (핵심)
  - mvcexam 프로젝트에서 패키지 생성 (kr.or.connect.mvcexam.controller)
  - PlusController 클래스 생성

```java
package kr.or.connect.mvcexam.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class PlusController {
	@GetMapping(path = "/plusform")
	public String plusform() {
		return "plusForm";
	}

	@PostMapping(path = "/plus")
	public String plus(@RequestParam(name = "value1", required = true) int value1,
			@RequestParam(name = "value2", required = true) int value2, ModelMap modelMap) {
		int result = value1 + value2;

		modelMap.addAttribute("value1", value1);
		modelMap.addAttribute("value2", value2);
		modelMap.addAttribute("result", result);
		return "plusResult";
	}
}

```

  - plusForm.jsp 렌더링은 Get방식으로 처리할 것이기 때문이 @GetMapping 적용
    - 이때, 이 일을 처리하게 될 메소드를 지정하는데, View Name을 넘겨줘야 함
    - 실제 Get 요청이 들어왔을 때 이 View 보여줘요~ 알려주면 됨
    - 그냥 String으로 View Name을 처리하기 위해 plusForm 메소드 선언
    - plusForm()가 리턴하는 문자열 "plusForm"은 WebMvcContextConfiguration.java 에서 `WEB-INF/views 에서 plusForm.jsp를 찾아서 View로 보여줌`

```java
	@GetMapping(path = "/plusform")
	public String plusform() {
		return "plusForm";
	}

```
  - mvcexam 프로젝트 run on server 수행하여 http://localhost:8080/mvcexam/plusform 입력시 화면 나타나면 됨


  - plufsForj.jsp 값 입력 Post 처리
    - @RequestParam 어노테이션으로 입력 파라미터 받음
    - request 객체를 생성하기 위해 HttpServletRequest를 Spring에서 직접 사용할 수도 있지만 우리는 ModelMap 객체를 사용
      - `ModelMap 객체`는 Spring 프레임워크가 알아서 request 객체로 만들어줌!
    - 반환하는 String 값 "plusResult"는 WebMvcContextConfiguration.java 에서 WEB-INF/views 에서 plusResult.jsp를 찾아서 View로 보여줌

```java
	@PostMapping(path = "/plus")
	public String plus(@RequestParam(name = "value1", required = true) int value1,
			@RequestParam(name = "value2", required = true) int value2, ModelMap modelMap) {
		int result = value1 + value2;

		modelMap.addAttribute("value1", value1);
		modelMap.addAttribute("value2", value2);
		modelMap.addAttribute("result", result);
		return "plusResult";
	}
}
```


# 실습2

- http://localhost:8080/mvcexam/userform 으로 요청을 보내면 이름, email, 나이를 물어보는 폼이 보여진다.
- 폼에서 값을 입력하고 확인을 누르면 post방식으로 http://localhost:8080/mvcexam/regist 에 정보를 전달하게 된다.
- regist에서는 입력받은 결과를 콘솔 화면에 출력한다.

- views 폴더 내 userform.jsp 만들기

```jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
<form method="post" action="regist">  
name : <input type="text" name="name"><br>
email : <input type="text" name="email"><br>
age : <input type="text" name="age"><br>
<input type="submit" value="확인"> 
</body>
</html>
```

- 요청을 처리할 Controller 생성
  - kr.or.connect.mvcexam.controller 패키지 내 UserController.java 클래스 파일 생성



```java
package kr.or.connect.mvcexam.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import kr.or.connect.mvcexam.dto.User;

@Controller
public class UserController {
	@RequestMapping(path="/userform", method=RequestMethod.GET)
	public String userform() {
		return "userform";
	}
	
	@RequestMapping(path="/regist", method=RequestMethod.POST)
	public String regist(@ModelAttribute User user) {

		System.out.println("사용자가 입력한 user 정보입니다. 해당 정보를 이용하는 코드가 와야합니다.");
		System.out.println(user);
		return "regist";
	}
}
```
  - 지난번 실습과의 차이
    - @GetMapping을 사용하였으나, 이번엔 @RequestMapping 사용
    - @ModelAttribute에 param을 하나씩 다 일일이 가져오는것이 아니라 `DTO를 이용하여 가방안에 한꺼번에` 가져온다!
      - @ModelAttribute에 User DTO 객체만 가져오면 Spring 컨테이너가 알아서 파라미터를 넘겨준다!
      - DTO와 req.param 내 name 속성을 반드시 일치시켜준다!
  
- DTO를 담을 패키지 생성 (kr.or.connect.mvcexam.dto 패키지 생성 후  User.java 클래스 파일 생성

```java
package kr.or.connect.mvcexam.dto;

public class User {
	private String name;
	private String email;
	private int age;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "User [name=" + name + ", email=" + email + ", age=" + age + "]";
	}	
}
```

- regist() 결과 View로 뿌려주는 regist.jsp 생성(views 폴더내에 위치)

```jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
<h2>잘 등록되었습니다</h2>
</body>
</html>
```


# 실습3

- http://localhost:8080/mvcexam/goods/{id} 으로 요청을 보낸다.
- 서버는 id를 콘솔에 출력하고, 사용자의 브라우저 정보를 콘솔에 출력한다.
- 서버는 HttpServletRequest를 이용해서 사용자가 요청한 PATH정보를 콘솔에 출력한다.


- views 폴더 내 goodsById.jsp 만들기

```jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
id : ${id } <br>
user_agent : ${userAgent }<br>
path : ${path }<br>
</body>
</html>
```

- 요청을 처리할 Controller 생성
  - kr.or.connect.mvcexam.controller 패키지 내 GoodsController.java 클래스 파일 생성

```java
package kr.or.connect.mvcexam.controller;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestHeader;

@Controller
public class GoodsController {
	@GetMapping("/goods/{id}")
	public String getGoodsById(@PathVariable(name="id") int id,
							   @RequestHeader(value="User-Agent", defaultValue="myBrowser") String userAgent,
							  HttpServletRequest request,
							  ModelMap model
							  ) {
		
		String path = request.getServletPath();
		
		System.out.println("id : " + id);
		System.out.println("user_agent : " + userAgent);
		System.out.println("path : " + path);
		
		model.addAttribute("id", id);
		model.addAttribute("userAgent", userAgent);
		model.addAttribute("path", path);
		return "goodsById";
	}
}
```
  - @GetMapping을 이용하여 Url 요청이 들어왔을 때 뒤에 들어오는 값 id를 Path Variable로 받겠다
  - Request Header에서 넘어오는 정보는 @RequestHeader를 이용하여 값들을 String변수 userAgent에 대입

- 서버 실행시켜보기
  - http://localhost:8080/mvcexam/goods/2 를 입력한 결과

```json
id : 2
user_agent : Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36
path : /goods/2
```
