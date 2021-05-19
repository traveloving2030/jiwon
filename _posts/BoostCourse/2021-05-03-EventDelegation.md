---
layout: post
title: "Event Delegation"
date: 2021-05-03
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---



- 아래 화면은 가로로 배치된 책 리스트
- 각각 리스트에 클릭을 할 때 어떤 이벤트가 발생해야 한다고 가정
- addEventListener를 사용해서 이벤트 등록을 할 수 있을것

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/36.png" height="280" width="300" />


```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
</head>
<body>

  <ul>
    <li><img src="https://images-na.ssl-images-amazon.com/images/I/513hgSybYgL._AC_SY400_.jpg" class="product-image" ></li>
    <li><img src="https://images-na.ssl-images-amazon.com/images/I/41HoczBHr2L._AC_SY400_.jpg" class="product-image" ></li>
    <li><img src="https://images-na.ssl-images-amazon.com/images/I/51AEI3isFiL._AC_SY400_.jpg" class="product-image" ></li>
    <li><img src="https://images-na.ssl-images-amazon.com/images/I/51JVp8YV3ZL._AC_SY400_.jpg" class="product-image" ></li>
  </ul>
  
  <section class="log"></section>
</body>
</html>
```

- li 각각에 addEventListener를 통해 이벤트를 등록

```js
var log = document.querySelector(".log");
var lists = document.querySelectorAll("ul > li");

for(var i=0; len=lists.length; i < len; i++) {
  lists[i].addEventListener("click", function(evt) {
     log.innerHTML = "clicked" + evt.currentTarget.firstElementChild.src;
  });
}
```

- 이때, 만약 list가 훨씬 더 많다면 브라우저는 기억해야 할 이벤트리스너도 많아짐 (비효율적)
- 또한, list가 한개 더 동적으로 추가된다면 추가된 엘리먼트에 역시 addEventListener를 해줘야 함
- `target 정보`가 해결해줄 수 있음!


```js
var log = document.querySelector(".log");
var ul = document.querySelectorAll("ul");

ul.addEventListener("click", function(evt){
  console.log(evt.target.tagName, evet.currentTarget.tagName); //IMG UL
})

```

# 이벤트 버블링

- 사진을 클릭해보자. (패딩사이 말고 딱 사진만 포함된 범위)
- 이벤트는 ul에 걸었는데, target은 IMG, currentTarget은 ul이 나왔다
- 아! `target`은 `Click한 지점의 정보를 알려주네!` (클릭한게 무엇인지 알려줌)
- `li나 img태그`는 `ul 태그에 속하기도함`. 따라서 `ul에 등록한 이벤트 리스너도 실행`이 됨 => `이벤트 버블링`

- list가 한개 더 동적으로 추가되더라도 문제없이 작동하는 법

```js
var ul = document.querySelector("ul");
ul.addEventListener("click",function(evt) {
    if(evt.target.tagName === "IMG") {
      log.innerHTML = "clicked" + evt.target.src;
    }
});
```

- 여기서 또 하나의 문제는, 이미지 태그는 padding 값이 있어서, img태그와 li 태그 사이에 공백이 존재
- 이 부분(공백)을 클릭하면 tagName이 LI라서 위에서 구현한 조건문으로 들어가지 않았기 때문
- 이 부분(공백)을 클릭해도 이미지 url을 출력할 수 있으려면 어떻게 해야할까

```js
var ul = document.querySelector("ul");
ul.addEventListener("click",function(evt) {
  debugger;
    if(evt.target.tagName === "IMG") {
      log.innerHTML = "clicked" + evt.target.src;
    } else if (evt.target.tagName === "LI") {
      log.innerHTML = "clicked" + evt.target.firstChild.src;
    }
});
```

- 이벤트 버블링 참고사이트 : http://jsbin.com/qepufol/1/edit?html,js,output
- 실습 url : http://jsbin.com/qepufol/1/edit?html,js,output