---
layout: post
title: "쿠키와 세션"
date: 2021-05-27
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---

# 웹에서의 상태 유지 기술

- HTTP프로토콜은 상태 유지가 안되는 프로토콜
    - 이전에 무엇을 했고, 지금 무엇을 했는지에 대한 정보를 갖고 있지 않음
    - 웹 브라우저(클라이언트)의 요청에 대한 응답을 하고 나면 해당 클라이언트와의 연결을 지속하지 않음
- `상태 유지`를 위해 Cookie와 Session기술이 등장


# 쿠키와 세션

## 쿠키

- 클라이언트 단에 저장되는 작은 정보의 단위
- 사용자 컴퓨터에 저장
- `클라이언트에서 생성하고 저장`될 수 있고, `서버단에서 전송한 쿠키가 클라이언트에 저장`될 수 있음
- 저장된 정보를 다른 사람 또는 시스템이 볼 수 있는 단점
- 유효시간이 지나면 사라짐
- 쿠키는 그 수와 크기에 제한
    - 하나의 쿠키는 4K Byte 크기로 제한
    - 브라우저는 각각의 웹사이트 당 20개의 쿠키를 허용
    - 모든 웹 사이트를 합쳐 최대 300개 허용
    - 따라서 클라이언트 당 쿠키의 최대 용량은 1.2M Byte

## 세션

- 서버에 저장
- 서버가 종료되거나 유효시간이 지나면 사라짐


# 쿠키의 동작 이해

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/52.png" height="280" width="300" />

- 클라이언트가 요청한 값에 유지해야할 정보가 있다면 서버는 유지할 정보를 가지고 쿠키 생성
    - 해당 쿠키는 key, value 쌍으로 구성되어 있으며 유지시간 등을 가짐
        - 주석, 경로, 유효기간, 버전, 도메인과 같은 추가적 정보도 저장 가능
- 만들어진 쿠키는 반드시 다시 클라이언트에게 보내져야함

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/53.png" height="280" width="300" />

- 클라이언트가 항상 쿠키를 가지고 있고 서버에게 요청할 때 쿠키를 같이 보냄
    - 서버는 아~~ 브라우저가 이런 정보를 유지해야 하구나 인식


# 세션의 동작 이해

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/54.png" height="280" width="300" />

- 세션키를 생성
- 세션 저장소에 저장해야 할 정보들을 저장
    - 저장소만 만들어놓고 끝내버리면 클라이언트가 다시 요청을 했을 때 서버는 클라이언트의 저장소가 어디에 있는지 알아낼 길이 없음
    - 따라서 서버측에서는 Session Key를 가지고 쿠키를 만들어냄
- 쿠키를 클라이언트에게 보냄
- 클라이언트는 다시 요청할 때마다 서버가 만들어낸 쿠키를 가지고 매번 들어올 것임
    - 이 때, 세션에 정보를 담기 위해 생성되는 객체가 `HttpSession 객체`!



# Cookie를 이용한 상태유지

- 서버에서 쿠키 생성, Response의 addCookie 메소드를 이용해 클라이언트에게 전송
- 이름, 값 쌍 정보를 입력하여 생성

```java
Cookie cookie = new Cookie(이름, 값);
response.addCookie(cookie);
```
- 쿠키의 이름은 알파벳과 숫자로만 구성해야함
    - 공백, 괄호, 등호, 콤마, 콜론, 세미콜론 등은 포함 불가!
    - 이름-값 쌍 외에도 도메인(Domain), 경로(Path), 유효기간(Max-Age, Expires), 보안(Secure), HttpOnly 속성을 저장 가능
- 웹 클라이언트는 쿠키를 응답으로 받으면 자동으로 서버별로 쿠키를 클라이언트에게 저장함

- 클라이언트가 보낸 쿠키 정보 읽기
    - httpServletRequest 객체 내 getCookies 함수를 이용하면 쿠키정보 읽기 가능
    - 쿠키의 데이터 타입은 배열로서, 쿠키값을 여러개를 받아올 수 있음 

```java
Cookie[] cookies = request.getCookies();
```

- 클라이언트가 가진 쿠키를 `서버측에서 삭제할 수는 없음`
    - 쿠키의 관리 주체는 웹 클라이언트임!
- 대신, `maxAge가 0인 같은 이름의 쿠키를 전송하면 됨`
    - 즉, 똑같은 이름을 가진 쿠키가 동시에 2개는 존재할 수 없음

```java
Cookie cookie = new Cookie("이름", null);
cookie.setMaxAge(0);
response.addCookie(cookie);
```

## setMaxAge()가 뭔가?

- 인자는 유효기간을 나타내는 초 단위의 정수형
- 만일 유효기간을 0으로 지정하면 쿠키의 삭제
- 음수를 지정하면 브라우저가 종료될 때 쿠키가 삭제
- 유효기간을 10분으로 지정하려면
- cookie.setMaxAge(10 * 60); //초 단위 : 10분
- 1주일로 지정하려면 (7*24*60*60)로 설정


# Session을 이용한 상태 유지

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/56.png" height="280" width="300" />

- 웹 클라이언트가 서버측에 요청을 보내게 되면 서버는 클라이언트를 식별하는 session id를 생성
- 서버는 session id를 이용해서 key와 value를 이용한 저장소인 HttpSession을 생성
- 서버는 session id를 저장하고 있는 쿠키를 생성하여 클라이언트에 전송
- 클라이언트는 서버측에 요청을 보낼때 session id를 가지고 있는 쿠키를 전송
- 서버는 쿠키에 있는 session id를 이용해서 그 전 요청에서 생성한 HttpSession을 찾고 사용


## 세션 유지

- 세션은 클라이언트가 서버에 접속하는 순간 생성
- 특별히 지정하지 않으면 세션의 유지 시간은 기본 값으로 `30분` 설정
    - 세션의 유지 시간이란 서버에 접속한 후 서버에 요청을 하지 않는 최대 시간
- 30분 이상 서버에 전혀 반응을 보이지 않으면 세션이 자동으로 끊어짐
- 이 세션 유지 시간은 web.xml파일에서 설정 가능

```xml
<session-config>
  <session-timeout>30</session-timeout>
</session-config>
```


## 세션 생성 및 얻기

- new HttpSession 하지 않는다. 서버가 알아서 만들어준다
    - request.getSession()을 수행하게 되면 클라이언트로부터 받아온 응답에서 SessionId로 되어있는 쿠키가 있는지 없는지 확인
    - 만약 세션이 있다면 세션을 반환하고 `없다면 새롭게 세션을 생성하여 반환`
        - 이때, 새롭게 생성된 세션인지는 HttpSession이 가지고 잇는 isNew() 메소드를 통해 알 수 있음
- 아래 두개의 코드는 같은 의미를 지니는 코드

```java
HttpSession session = request.getSession();
HttpSession session = request.getSession(true);
```

- 아래의 코드에서 request의 getSession()메소드에 파라미터로 false를 전달하면, 이미 생성된 세션이 있다면 반환하고 `없으면 null을 반환`

```java
HttpSession session = request.getSession(false);
```


## 세션에 값 저장

- name과 value의 쌍으로 객체 `Object를 저장`하는 메소드
    - 따라서, getAttribute 할때 Object를 알맞은 형태로 형변환을 꼭 해주어야함

```java
setAttribute(String name, Object value)
String value = (String) session.getAttribute("id");
```


## 세션 값 삭제

- removeAttribute(String name) 메소드
    - name값에 해당하는 세션 정보 삭제

- invalidate() 메소드
    - 모든 세션 정보를 삭제



## javax.servlet.http.HttpSession

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/57.png" height="280" width="300" />

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/58.png" height="280" width="300" />