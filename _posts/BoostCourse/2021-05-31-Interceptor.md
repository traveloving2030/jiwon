---
layout: post
title: "인터셉터"
date: 2021-05-31
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---


# 인터셉터

- Interceptor는 Dispatcher servlet에서 Handler(Controller)로 요청을 보낼 때, Handler에서 Dispathcer servlet으로 응답을 보낼 때 동작

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/42.jpg" height="280" width="300" />

- Spring이 동작될 때 클라이언트로부터 요청이 들어오면 Filter가 존재한다면 Filter를 수행
    - 우리는 web.xml에 Characher Set을 지정하는 필터를 정의했음
    
    ```xml
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter
		</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
    ```

- Dispatcher Servlet은 선처리 할 작업이 존재한다면 선처리함
    - Handler Mapping을 통해서 실제로 어떤 Handler가 동작을 해야하는지 정보를 얻음
    - 이때, Dispatcher Servlet이 Handler를 수행할 때 HandlerInterceptor가 존재
- 위 요청 작업이 끝나면 View 정보를 Dispatcher Servlet에게 넘겨줌
    - View Resolver를 통해서 View 정보를 얻어오고 해당 View를 찾아 응답함


## Servlet 필터와 인터셉터의 차이점

- 서블릿 필터는 dispatcherServlet이 실행되기 이전에 발생하고 인터셉터는 dispatcherServlet 이후 handler로 넘어가기 전에 발생


## 인터셉터 작성법

- org.springframework.web.servlet.HandlerInterceptor 인터페이스를 구현
- org.springframework.web.servlet.handler.HandlerInterceptorAdapter 클래스를 상속
- Java Config를 사용한다면, WebMvcConfigurerAdapter가 가지고 있는 addInterceptors 메소드를 오버라이딩하고 등록하는 과정을 거침
- xml 설정을 사용한다면, < mvc:interceptors> 요소에 인터셉터를 등록



# 인터셉터 적용 실습

- 웹 어플리케이션에 인터셉터를 적용해서 컨트롤러 실행 전과 실행 후에 로그를 남겨보도록 함
- kr.or.connect.guestbook.interceptor 패키지 생성 후 HandlerInterceptorAdapter 클래스를 상속하는 LogInterceptor 클래스 생성
- preHandle 메소드는 Controller 메소드가 실행되기전, postHandle 메소드는 Controller 메소드가 실행된 후 실행

### LogInterceptor.java

```java
package kr.or.connect.guestbook.interceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

public class LogInterceptor extends HandlerInterceptorAdapter{

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		System.out.println(handler.toString() + " 가 종료되었습니다.  " + modelAndView.getViewName() + "을 view로 사용합니다.");
	}

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		System.out.println(handler.toString() + " 를 호출했습니다.");
		return true;
	}
}
```


- 인터셉터가 만들어졌으면, 인터페이스를 등록해야함
    - WebMvcContextConfiguration.java 파일에 등록함

### WebMvcContextConfiguration.java 

```java
@Override
	public void addInterceptors(InterceptorRegistry registry) {
    		registry.addInterceptor(new LogInterceptor());
	}
```

## server run 했을 때 결과

- preHandle의 System.out.println(handler.toString() + " 를 호출했습니다."); 가 출력
- postHandle의 System.out.println(handler.toString() + " 가 종료되었습니다.  " + modelAndView.getViewName() + "을 view로 사용합니다."); 가 출력
    -  이때, index를 View로 사용합니다. 라고 되어있는데, list.jsp는 index.jsp에서 리다이렉트로 열어짐


<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/60.PNG" height="280" width="300" />

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/61.PNG" height="280" width="300" />



## 방명록을 추가했을 때의 결과

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/62.PNG" height="280" width="300" />

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/63.PNG" height="280" width="300" />

- 등록할 때 guestbookController의 write 메소드가 호출됨
    - write 메소드가 다 호출되면 인터셉터의 postHandle() 메소드 때문에 콘솔 출력
        - 이때, view 이름이 redircet:list라고 되어있는데, redierct: 이 들어가면 실제로 view를 찾아내는게 아니라 그냥 redirect 시켜주는 것!
        - redirect 되었으니 다시 list 요청을 처리하는 controller를 찾아옴


## 정리

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/42.jpg" height="280" width="300" />

- 위 그림에서 Handler 부분에서 guestbook 컨트롤러의 list 헨들러가 호출되어 수행하고 나옴
- View를 list 라는 View를 이용는구나
    - ViewResolver에서 list에 해당되는 View 정보를 얻어내서 실제 list.jsp를 찾고 해당 list.jsp가 응답함

- 어떠한 동작을 거쳐서 인터셉터가 수행되는지 잘 생각해보야함!

- `해당 시점에 특별한 일을 수행해야한다면` postHandle, preHandle 메소드에 적절한 기능을 집어넣을 수 있음!

 