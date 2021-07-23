---
layout: post
title: "Argument Resolver"
date: 2021-06-01
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---


# Argument Resolver

- 컨트롤러의 메소드의 인자로 사용자가 임의의 값을 전달하는 방법을 제공하고자 할 때 사용
- 예를 들어, 세션에 저장되어 있는 값 중 특정 이름의 값을 메소드 인자로 전달


# Argument Resolver 동작방식

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/53.jpg" height="280" width="300" />

- 1. Client Request 요청
- 2. Dispatcher Servlet에서 해당 요청을 처리
- 3. Client Request에 대한 Handler Mapping
    - RequestMapping에 대한 매칭 (RequestMappingHandlerAdapter)
    - Interceptor처리
    - Argument Resolver 처리 <-- 바로 여기서 우리가 지정한 Argument Resolver 실행
    - Message Converter 처리
- 4. Controller Method invoke


# Argument Resolver 작성방법 1/2

- org.springframework.web.method.support.HandlerMethodArgumentResolver를 구현한 클래스를 작성
- supportsParameter메소드를 오버라이딩 한 후, 원하는 타입의 인자가 있는지 검사한 후 있을 경우 true가 리턴되도록 함
- resolveArgument메소드를 오버라이딩 한 후, 메소드의 인자로 전달할 값을 리턴


# Argument Resolver 작성방법 2/2

- Java Config에 설정하는 방법
    - WebMvcConfigurerAdapter를 상속받은 Java Config 파일에서 addArgumentResolvers 메소드를 오버라이딩 한 후 원하는 Argument Resolver 클래스 객체를 등록

- xml 파일에 설정하는 방법

```xml
   <mvc:annotation-driven>
        <mvc:argument-resolvers>
            <bean class="아규먼트리졸버클래스"></bean>      
        </mvc:argument-resolvers>
    </mvc:annotation-driven>
```


# Spring MVC의 기본 ArgumentResolver들

- getDefaultArgumentResolvers()메소드를 보면 기본으로 설정되는 Argument Resolver에 어떤 것이 있는지 알 수 있음
<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/64.png" height="280" width="300" />

- 하지만 Map객체나 Map을 상속받은 객체는 Spring에서 이미 선언한 아규먼트 리졸버가 처리하기 때문에 전달 할 수 없음
    - Map객체를 전달하려면 Map을 필드로 가지고 있는 별도의 객체를 선언한 후 사용해야함



# Argument Resolver 실습

- 웹 어플리케이션에 아규먼트 리졸버를 적용해 HTTP요청 헤더 정보를 저장하고 있는 HeaderInfo 인자타입이 메소드에 있을 경우 자동으로 넘겨주는 예제 실습

- kr.or.connect.guestbook.argumentresolver 패키지 생성
- Map객체나 Map을 상속받은 객체는 Spring에서 이미 선언한 아규먼트 리졸버가 처리하기 때문에 전달 할 수 없기에,
    - Map을 Field로 가지고 있는 HeadInfo Class작성
        - map 지정 생성자함수 정의
        - map을 넣고 뺄수있는 put, get 함수 정의


### HeadInfo.java

```java
package kr.or.connect.guestbook.argumentresolver;

import java.util.HashMap;
import java.util.Map;

public class HeaderInfo {
	private Map<String, String> map;
	
	public HeaderInfo() {
		map = new HashMap<>();
	}

	public void put(String name, String value) {
		map.put(name,  value);
	}
	
	public String get(String name) {
		return map.get(name);
	}

}
```

- Handler 메소드 Argument Resolver 인터페이스를 구현하는 HeaderMapArgumentResolver Class 구현
    - 컨트롤러 메소드 인자가 4개일 경우, supportsParameter 메소드가 4번 호출
        - supportsParameter는 인자의 정보를 파라미터로 전달하는데, 해당 파라미터 정보에 원하는 정보가 있다면 true, 없다면 false 반환
            - 여기서 파라미터 타입이 위에서 생성한 HeaderInfo 클래스 타입일 경우 true를 return 하도록 함
        - supportsParameter가 true를 반환할 경우에만 resolveArgument 메소드가 호출됨
        - resolveArgument가 return한 값은 Controller 메소드의 인자로 전달됨
            - 요청으로부터 넘어온 모든 HeaderInfo 정보를 담는 headerInfo 객체 생성
            - 헤더정보를 Iterator로 받아옴



### HeaderMapArgumentResolver.java

```java
package kr.or.connect.guestbook.argumentresolver;

import java.util.Iterator;

import org.springframework.core.MethodParameter;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

public class HeaderMapArgumentResolver implements HandlerMethodArgumentResolver {

	@Override
	public boolean supportsParameter(MethodParameter parameter) {
		return parameter.getParameterType() == HeaderInfo.class;
	}

	@Override
	public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
			NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {

		HeaderInfo headerInfo = new HeaderInfo();
		
		Iterator<String> headerNames = webRequest.getHeaderNames();
		while(headerNames.hasNext()) {
			String headerName = headerNames.next();
			String headerValue = webRequest.getHeader(headerName);
			System.out.println(headerName + " , " + headerValue);
			headerInfo.put(headerName, headerValue);
		}
		
		return headerInfo;

	}

}
```

- 이렇게 생성한 Argument Resolver를 적용하기 위해서 WebMvcContextConfiguration 클래스에 
    - addArgumentResolver 메소드를 오버라이딩 해야함
    - 인자로 넘어온 argumentResolvers에 앞에서 생성한 아규먼트 리졸버를 넘겨줘야함

### WebMvcContextConfiguration.java 추가

```java
@Override
	public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
    		System.out.println("아규먼트 리졸버 등록..");
		argumentResolvers.add(new HeaderMapArgumentResolver());
	}
```


- Argument Resolver를 HeaderInfo 클래스로 생성했고, HeaderMapArgumentResolver 클래스에 해당 아규먼트 리졸버를 등록했다면
    - GuetbookController.java 에서 아규먼트 리졸버가 해당 인자가 넘어왔을 때 사용이 된다고 했으니 인자를 넘겨줘야 함

### GuetbookController.java 에서 list 메소드 수정

```java
@GetMapping(path="/list")
	public String list(@RequestParam(name="start", required=false, defaultValue="0") int start,
					   ModelMap model, @CookieValue(value="count", defaultValue="1", required=true) String value,
					   HttpServletResponse response,
					   HeaderInfo headerInfo) {
		System.out.println("-----------------------------------------------------");
		System.out.println(headerInfo.get("user-agent"));
		System.out.println("-----------------------------------------------------");
```



# 서버 실행 결과

- HeaderMapArgumentResolver.java 내의 System.out.println(headerName + " , " + headerValue); 결과

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/65.PNG" height="280" width="300" />


- GuetbookController.java 내의 System.out.println(headerInfo.get("user-agent")); 결과

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/66.PNG" height="280" width="300" />

