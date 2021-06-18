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

- healthObj는 `객체리터럴`(비슷한 기능을 묶음)
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

## this 좀 더 알아보기

- get() 함수가 실행되면 window. 함수가 실행될때의 컨텍스트는 window를 참조한다.

```js
function get() {
    return this;
}

get(); //window. 함수가 실행될때의 컨텍스트는 window를 참조한다.
new get(); //object. new키워드를 쓰면 새로운 object context가 생성된다.
```


# bind 메소드로 this제어하기

- this 키워드는 this를 사용하고 있는 함수가 어떻게 불리는가에 따라서 달라지는 경우가 있음
- bind를 사용하면 원하는 this가 가리키는 대상을 묶어놓을 수 있음

```js
var healthObj = {
  name : "달리기",
  lastTime : "PM10:12",
  showHealth : function() {
    setTimeout(function(){
      console.log(this.name + "님, 오늘은 " + this.lastTime + "에 운동을 하셨네요");
    }, 500);
  }
}

healthObj.showHealth(); // 님, 오늘은 undefined에 운동을 하셨네요
```
- 결과 : undefined가 나온다. 왜일까? => 확인을 위해 `debugger`를 작동시켜본다
  - 현업에서 코드가 복잡해 this가 가리키는것을 확인해야 할때가 많은데, 이때 debugger를 걸어주면 됨!

```js
var healthObj = {
  name : "달리기",
  lastTime : "PM10:12",
  showHealth : function() {
    setTimeout(function(){
      debugger;
      console.log(this.name + "님, 오늘은 " + this.lastTime + "에 운동을 하셨네요");
    }, 500);
  }
}
console.log(this); // [object Window] 

```

- 확인해봤더니, this가 [object Window] 로 나온다. 그랬더니 undefined가 나왔구나
- 그럼 this가 healthObj를 가리키려면?
  - `함수의 바깥부분을 가리키도록` 설정해줘야함 => bind

```js
var healthObj = {
  name : "달리기",
  lastTime : "PM10:12",
  showHealth : function() {
    setTimeout(function(){
      console.log(this.name + "님, 오늘은 " + this.lastTime + "에 운동을 하셨네요");
    }.bind(this), 500);
  }
}
healthObj.showHealth(); //"달리기님, 오늘은 PM10:12에 운동을 하셨네요"
```

- function(){}.bind(this) 로서, `함수 뒤에 .으로 bind`를 시킴
  - 이때, 함수도 .으로 변하는 순간 `객체로 변함`


## ES6 Arrow로 함수를 표현하면 어떻게 될까?

- 이때는 bind가 필요가 없다.

```js
var healthObj = {
  name : "달리기",
  lastTime : "PM10:12",
  showHealth : function() {
    setTimeout(() => {
        console.log(this.name + "님, 오늘은 " + this.lastTime + "에 운동을 하셨네요");      
    }, 1000)
  }
}
healthObj.showHealth(); // "달리기님, 오늘은 PM10:12에 운동을 하셨네요"
```