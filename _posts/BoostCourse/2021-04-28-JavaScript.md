---
layout: post
title: "WebAPI"
date: 2021-04-27
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# 자바스크립트 배열 & 객체

## Concat 

- 원소 합치기
```js
var origin = [1,2,3,4];
var changed = origin.concat(5,6);
console.log(changed) //[1,2,3,4,5,6]
```

- 주의할 점은 어떤 메서드는 새로운 배열을 반환하고, 어떤 메서드는 원래 배열의 값을 변경시킨다는 것입니다. 예를 들어서 concat은 원래 배열은 그대로 있고 합쳐진 새로운 배열을 반환함
- 아래 origin과 changed는 서로 다른 배열!!

```js
var origin = [1,2,3,4];
var changed = origin.concat();  //[1,2,3,4]
console.log(origin === changed);  //[1, 2, 3, 4] [1, 2, 3, 4] false
```


## ForEach

- 아래 두 식은 같음(자바스크립트는 1급함수 임)
```js
var origin=[1,2,3,4];

origin.forEach(function(v,i,o){ 
    console.log(v);
})

```

```js
var origin=[1,2,3,4];

var fun = function(v,i,o){
    console.log(v)
}
origin.forEach(fun);

```


## Map

- 값을 반환한다는 것에 주목!
- 마찬가지로 origin은 [1,2,3,4]로 값을 유지함!
```js
var mapped=origin.map(function(v){
    return v*2; 
})

console.log(mapped) // [2,4,6,8]
```

```js
var newArr = ["apple","tomato"].map(function(value, index) {
   return index + "번째 과일은 " + value + "입니다";
});
console.log(newArr) // ["0번째 과일은 apple입니다", "1번째 과일은 tomato입니다"]
```