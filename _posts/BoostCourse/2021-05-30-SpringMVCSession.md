---
layout: post
title: "Spring MVC에서 Session사용하기"
date: 2021-05-30
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---

# Spring MVC에서 Session사용하기

## @SessionAttributes & @ModelAttribute

- @SessionAttributes 파라미터로 지정된 이름과 같은 이름이 @ModelAttribute 파라미터에 지정되어 있을 경우 메소드가 반환되는 값은 세션에 저장
    - 아래 예제에서는 setUpUserForm() 메소드가 리턴하는 User라는 객체가 @ModelAttribute()의 파라미터인 user 이름으로  세션에 저장됨
    
```java
@SessionAttributes("user")
public class LoginController {
  @ModelAttribute("user")
  public User setUpUserForm() {
  return new User();
  }
}
```

- Controller 클래스 위에 @SessionAttribute 어노테이션 사용
- doLogin 메소드의 인자로 @ModelAttribute가 사용되었는데, 이때 SessionAttribute와 ModelAttribute로 전달되는 파라미터가 같을 경우, 
    - @ModelAttribute("user") : 먼저 Session에서 파라미터로 전달된 이름으로 저장된 객체 user를 찾음
    - User user : 그리고나서 해당객체의 요청으로부터 넘어온 값을 설정하여 전달하게 됨

```java
@Controller
@SessionAttributes("user")
public class LoginController {
......
  @PostMapping("/dologin")
  public String doLogin(@ModelAttribute("user") User user, Model model) {
......
  }
}
```


## SessionStatus

- @ModelAttribute("user")를 통해 session 객체 user를 찾아서 User user로 전달한 상태에서
    - 전달받은 값을 `삭제`할 때는 sessionStatus.setComplete() 메소드 호출

```java
@Controller
@SessionAttributes("user")
public class UserController {
...... 
    @RequestMapping(value = "/user/add", method = RequestMethod.POST)
    public String submit(@ModelAttribute("user") User user, SessionStatus sessionStatus) {
  ......
  sessionStatus.setComplete();
                                   ......

   }
 }
```


## form tag 라이브러리

- modelAttribute속성으로 지정된 이름의 객체를 세션에서 읽어와서 form태그로 설정된 태그에 값을 설정 가능

```jsp
<form:form action="login" method="post" modelAttribute="user">
Email : <form:input path="email" /><br>
Password : <form:password path="password" /><br>
<button type="submit">Login</button>
</form:form>
```


# 실습

- 관리자는 /loginform에서 암호를 입력해 로그인을 한다.
- 관리자가 암호를 맞게 입력할 경우 세션에 로그인 정보가 저장된다.
- 세션에 로그인 정보가 있을 경우 방명록에는 "삭제" 링크가 보여진다.
- 삭제 링크를 누르면 삭제가 된다. 삭제 작업에서도 로그인 정보가 있는지를 검사해야 한다.


### loginform.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>loginform</title>
</head>
<body>
<h1>관리자 로그인</h1>
<br><br>
${errorMessage}<br>

<form method="post" action="login">
	암호 : <input type="password" name="passwd"><br>
	<input type="submit">
</form>

</body>
</html>
```


### GuestbookAdminController.java

- 패스워드가 1234와 같으면 session Attribute에 isAdmin 값을 넣음
    - 암호가 맞지않으면 redirectAttributes에 errorMessage를 넣음
        - redirectAttributes는 dispatcherServlet이 관리하는 FlashMap에 값을 저장함
            - redirect할때 딱 한번만 값을 유지할 목적으로 사용됨(Redirect는 요청을 2번하는 것을 기억)
            - Session을 사용할수도 있지만 좀더 효율적인 목적으로 redirectAttr를 사용함
- 로그아웃 될 시 session.removeAttribute로 세션값 삭제

```java
package kr.or.connect.guestbook.controller;

import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.support.SessionStatus;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

@Controller
public class GuestbookAdminController {

       @GetMapping(path="/loginform")
		public String loginform() {
			return "loginform";
		}
		
        @PostMapping(path="/login")
		public String login(@RequestParam(name="passwd", required=true) String passwd, 
				HttpSession session,
				RedirectAttributes redirectAttr) {
			
			if("1234".equals(passwd)) {
				session.setAttribute("isAdmin", "true");
			}else {
				redirectAttr.addFlashAttribute("errorMessage","암호가 틀렸습니다.");
				return "redirect:/loginform";
			}
			return "redirect:/list";
		}
		
       @GetMapping(path="/logout")
		public String login(HttpSession session) {
			session.removeAttribute("isAdmin");
			return "redirect:/list";
		}

}
```


### list.jsp

- 진짜 로그인 된건지, 안된건지 확인하기 위해 기존의 list.jsp 에서 삭제 링크 걸어줌
    - loginform.jsp 페이지에서 1234로 로그인했을때만 해당 페이지 보여짐
    - GuestbookAdminController에서 session.setAttribute("isAdmin", "true");로 Session 지정했음
    - 따라서 아래 방명록에 삭제 링크를 걸어줌으로서 잘 들어갔는지 확인할 수 있음


```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>    
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>방명록 목록</title>
</head>
<body>

<h1>방명록</h1>
<br>
방명록 전체 수 : ${count }, 방문한 수 : ${cookieCount }<br><br>


<c:forEach items="${list}" var="guestbook">

${guestbook.id }<br>
${guestbook.name }<br>
${guestbook.content }<br>
${guestbook.regdate }<br>
<c:if test="${sessionScope.isAdmin == 'true'}"><a href="delete?id=${guestbook.id}">삭제</a><br><br></c:if>
</c:forEach>
<br>

<c:forEach items="${pageStartList}" var="pageIndex" varStatus="status">
<a href="list?start=${pageIndex}">${status.index +1 }</a>&nbsp; &nbsp;
</c:forEach>

<br><br>
<form method="post" action="write">
name : <input type="text" name="name"><br>
<textarea name="content" cols="60" rows="6"></textarea><br>
<input type="submit" value="등록">
</form>
</body>
</html>
```


### GuestbookController.java

- list.jsp에서 삭제 버튼을 누르면 delete 요청 처리부 구현
    - GuestbookController 에서 구현하는게 맞음. 왜냐하면 컨텐츠를 삭제하는 것이기 때문
- guestbookService에서 addGuestbook 메소드 구현시 사용자 ip를 log로 저장함
    - 컨텐츠가 삭제될때 해당 방명록의 id와 ip값도 같이 삭제됨


```java
	@GetMapping(path="/delete")
	public String delete(@RequestParam(name="id", required=true) Long id, 
			             @SessionAttribute("isAdmin") String isAdmin,
			             HttpServletRequest request,
			             RedirectAttributes redirectAttr) {
		if(isAdmin == null || !"true".equals(isAdmin)) { // 세션값이 true가 아닐 경우
			redirectAttr.addFlashAttribute("errorMessage", "로그인을 하지 않았습니다.");
			return "redirect:loginform";
		}
		String clientIp = request.getRemoteAddr();
		guestbookService.deleteGuestbook(id, clientIp);
		return "redirect:list";		
	}
```