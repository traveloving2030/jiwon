---
layout: post
title: "RestController"
date: 2021-05-15
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---

# RestController
- Spring 4 에서 Rest API 또는 Web API를 개발하기 위해 등장한 애노테이션
- 이전 버전의 @Controller와 @ResponseBody를 포함

# MessageConvertor
- 자바 객체와 HTTP 요청 / 응답 바디를 변환하는 역할
     - ex) 외부에서 전달받은 JSON 메소드를 내부에서 사용할 수 있는 객체로 변환
     - 컨트롤러를 리턴한 객체가 클라이언트에게 JSON으로 변환해서 전달할 수 있도록 하는 역할
- @ResponseBody, @RequestBody
- @EnableWebMvc 로 인한 기본 설정
- WebMvcConfigurationSupport 를 사용하여 Spring MVC 구현
- Default MessageConvertor 를 제공
- 링크 바로가기 의 addDefaultHttpMessageConverters메소드 항목 참조


# JSON 응답하기
- 컨트롤러의 메소드에서는 JSON으로 변환될 객체를 반환
- jackson라이브러리를 추가할 경우 객체를 JSON으로 변환하는 메시지 컨버터가 사용되도록 @EnableWebMvc에서 기본으로 설정되어 있음
- jackson라이브러리를 추가하지 않으면 JSON메시지로 변환할 수 없어 500오류가 발생
- 사용자가 임의의 메시지 컨버터(MessageConverter)를 사용하도록 하려면 WebMvcConfigurerAdapter의 configureMessageConverters메소드를 오버라이딩 하도록 함


# RestCotroller 실습
- guestbook 프로젝트에 계속함
- kr.or.connect.guestbook.controller 패키지 내 GuestbookApiController.java 파일 생성
    - @RestController 어노테이션 
    - 클래스 자체에 @RequestMapping(path="/guestbooks") 선언하면 메소드 바디에 있는 내용들을 공통으로 사용가능
    - contentsType으로 application/json 그리고 GET방식으로 요청이 들어오면 @GetMapping 이하의 메소드가 실행
        - list메소드는 결과값으로 Map 객체를 반환할 것
            - application/json 요청이기 때문에 Dispatcher Servlet은 json MessageConvertor를 내부적으로 사용하여 해당 Map 객체를 JSON으로 변환하여 전송하게 됨
        - write 메소드 역시 Guestbook 객체를 반환하는데 이때도 클라이언트에게 JSON 객체로 변환되어 갈것

```java
package kr.or.connect.guestbook.controller;

import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import kr.or.connect.guestbook.dto.Guestbook;
import kr.or.connect.guestbook.service.GuestbookService;

@RestController
@RequestMapping(path="/guestbooks")
public class GuestbookApiController {
	@Autowired
	GuestbookService guestbookService;
	
	@GetMapping
	public Map<String, Object> list(@RequestParam(name="start", required=false, defaultValue="0") int start) {
		
		List<Guestbook> list = guestbookService.getGuestbooks(start);
		
		int count = guestbookService.getCount();
		int pageCount = count / GuestbookService.LIMIT;
		if(count % GuestbookService.LIMIT > 0)
			pageCount++;
		
		List<Integer> pageStartList = new ArrayList<>();
		for(int i = 0; i < pageCount; i++) {
			pageStartList.add(i * GuestbookService.LIMIT);
		}
		
		Map<String, Object> map = new HashMap<>();
		map.put("list", list);
		map.put("count", count);
		map.put("pageStartList", pageStartList);
		
		return map;
	}
	
	@PostMapping
	public Guestbook write(@RequestBody Guestbook guestbook,
						HttpServletRequest request) {
		String clientIp = request.getRemoteAddr();
		// id가 입력된 guestbook이 반환된다.
		Guestbook resultGuestbook = guestbookService.addGuestbook(guestbook, clientIp);
		return resultGuestbook;
	}
	
	@DeleteMapping("/{id}")
	public Map<String, String> delete(@PathVariable(name="id") Long id,
			HttpServletRequest request) {
		String clientIp = request.getRemoteAddr();
		
		int deleteCount = guestbookService.deleteGuestbook(id, clientIp);
		return Collections.singletonMap("success", deleteCount > 0 ? "true" : "false");
	}
}
```

- 잘 동작하는지 확인하기 위해 PostMAN으로 실습
    - GET방식 : http://localhost:8080/guestbook/guestbooks
        - Headers에 Key 값으로 Content-Type Value값으로 application/json 명시
        - 하나만 조회 : http://localhost:8080/guestbook/guestbooks?start=5
    - Post방식: http://localhost:8080/guestbook/guestbooks
        - Headers에 Key 값으로 Content-Type Value값으로 application/json 명시
        - body내용 첨가
        
        ```json
        {
            "name":"hello!",
            "content":"test중"
        }
        ```
    - Delete방식: http://localhost:8080/guestbook/guestbooks
        - http://localhost:8080/guestbook/guestbooks/3