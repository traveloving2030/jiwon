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
<ul>
  <li>
    <img src="https://images-na.,,,,,/513hgbYgL._AC_SY400_.jpg" class="product-image" >    </li>
  <li>
    <img src="https://images-n,,,,,/41HoczB2L._AC_SY400_.jpg" class="product-image" >    </li>
  <li>
    <img src="https://images-na.,,,,51AEisFiL._AC_SY400_.jpg" class="product-image" >  </li>
 <li>
    <img src="https://images-na,,,,/51JVpV3ZL._AC_SY400_.jpg" class="product-image" >
 </li>
 <section class="log"></section>
</ul>
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

- 8분부터 다시 듣기! 