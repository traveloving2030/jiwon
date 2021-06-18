---
layout: post
title: "자바스크립트 - 객체 리터럴과 this"
date: 2021-05-18
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---

# 자바스크립트 객체의 활용

- '.' 이 들어간 것들은 객체라고 생각하면됨 e.g) console.log => console은 객체!
- function(){}.bind() 라는 표현식이 있음. 그럼 function은 그럼 객체인가?
    - 객체로서 동작하는것은 맞지만, 함수는 함수타입이라고 함.
- 자바스크립트의 객체는 SingleTon이다. 즉, 하나의 객체를 반환하게 되어있음

## this

- 이 함수의 실행 Context, 즉 해당 함수가 참조하고 있는 객체의 참조지점을 알려줌
- 아래의 showHeath()의 함수는 healthObj `안에서 불림`. 따라서 this가 가리키는 것은 healthObj가 됨
- this는 실행타임에 결정됨

```js
var healthObj = {
  name : "달리기",
  lastTime : "PM10:12",
  showHealth : function() {
    console.log(this.name + "님, 오늘은 " + this.lastTime + "에 운동을 하셨네요");
  }
}

healthObj.showHealth();
```

```js
const obj = {
   getName() {
     return this.name;
     },
  setName(name) {
      this.name = name;
    }
}
obj.setName("crong");
const result = obj.getName();
```





