---
layout: post
title: "자바스크립트 비동기&이벤트 큐"
date: 2021-04-30
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# 자바스크립트

- 자바스크립트는 싱글 스레드 프로그래밍 언어
- 싱글 스레드 런타임 = 한번에 하나의 싱글 콜 스택을 가짐 = 하나의 프로그램은 동시에 하나의 코드만 실행할 수 있음 = 자바스크립트는 기본적으로 `동기 방식`으로 처리됨

## 그럼 비동기 처리는 왜 필요할까?
- 비동기처리 : `특정 로직의 실행이 끝날때까지 기다려주지 않고 나머지 코드를 먼저 실행하는것`
- 비동기를 처리하지않고 동기적으로 구성한다고 가정하자.
- 만일 WebAPI를 이용하여 데이터를 받아온다면, 다음함수가 실행되기 위해서 데이터를 모두 가져올 때까지 기다려야 한다.
- 이러한 문제를 해결하기 위해 비동기처리 함수를 구성해주어야 한다(setTimeout과 같은)
- 비동기 처리의 사례 : `Ajax`, `SetTimeOut`
- 비동기 처리 방식의 문제점은 밑에서 계속!!


<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/32.PNG" height="280" width="300" />

- 함수를 실행하려면 스택에 해당하는 함수를 집어넣어야함. `함수에서 리턴이 일어나면` 스택의 가장 위쪽에서 해당함수를 꺼냄
- 블로킹 : 느린 동작이 스택에 남아있는 것 e.g) 네트워크 요청, 이미지 프로세싱
- 브라우저는 Web API와 같은것을 제공함
- `setTimeout(자바스크립트 런타임 함수)과 같은 비동기함수, 또는 XHR WebAPI(브라우저 WebAPI)가 실행되면 WebAPI는 바로 종료하고 큐에 콜백을 집어넣음`
- `이벤트 루프는 스택이 비워질때까지 기다린 후에 스택에 쌓을 수 있음`

```js
console.log("hi");

$.get('url', function cb(data){
    console.log(data)
})

console.log('bye')
```

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/note.jpg" height="280" width="300" />

- Stack 쌓이는 순서
1. main()함수
2. console.log("hi") 쌓이고 실행 후 사라짐
3. $.get('url', cb) 쌓였다가 webapi 처리를 위해 사라짐 ( XHR Web API가 실행되는 동안 다른 코드가 정상적으로 실행됨)
4. console.log("bye") 쌓이고 실행 후 사라짐
5. XHR 실행이 완료 되면 WebAPI 콜백함수 cb는 Queue에 쌓이게 됨
6. 스택이 비워지고 나면 콜백함수 cb가 비로소 쌓이게 되고 `이벤트 루프에 의해 실행`
7. console.log(data) 쌓이고 실행 후 사라짐


## Promise 구문 (Async & Await)

```js
function delay(sec, callback){
    setTimeout(()=>{
        callback(new Date().toISOString());
    }, sec * 1000);
}

delay(1, (result)=> {
    console.log(1, result)
})

delay(1, (result)=> {
    console.log(2, result)
})

delay(1, (result)=> {
    console.log(3, result)
})
```

- 위 3개의 delay 함수는 비동기로 인해 콘솔로그가 모두 동시에 찍힘
- `우리가 원하는건 1초, 2초, 3초 이렇게 콘솔로그가 찍히도록` 하고싶어. 그럼, 어케해야할까?

```js
delay(1, (result)=>{
    console.log(1, result);

    delay(1, (result)=>{
        console.log(2, result);

        delay(1, (result)=>{
            console.log(3, result)
        })
    })
})
```

- 이렇게 하면 해결할 수 있는데.. `콜백지옥`에 부딪혔군!!
- 이런 `콜백지옥 문제를 해결하기 위해 나온것이 Promise`!!

```js
async function myAsync(){
    return 'async'
}

myAsync().then((result)=>{
    console.log(result);
})
```
- async는 함수를 Promise를 Return하는 함수로 만들어 줌
- Promise에서는 비동기 연산을 통해 resolve함수를 실행하고 
- .then 구문 뒤에 나오는 콜백 파라미터 result는 aysnc 함수의 return값 ('async' 스트링)을 받는다
- 그럼 await은 무엇일까?

```js
async function myAsync(){
    delay(3).then((time)=>{
        console.log(time);
    })
    return 'async'
}
```

- 위 코드에서, delay구문과 밑에 return 구문은 비동기 연산이 됨. 즉, return async가 먼저된다
- 우리가 원하는 것은 `delay되고 async를 return 하는것`!

```js
async function myAsync(){
    await delay(3)
    return 'async'
}
```

- delay(3)은 Promise였다! => Promise가 Resolve될때까지 다음줄로 넘어가지 않는다!, 즉 `delay(3)이 수행되기전까지 return 'async' 수행하지 않음`
- 그럼 await을 쓸 수 있는 함수는 꼭 Promise 이어야하는가?

```js
function normalFunc(){
    return 'wow';
}
async function myAsync(){
    const result1 = await normalFunc();
    console.log(result1);
    return 'async'
}

myAsync().then((result2)=>{
    console.log(result2)
})

/*결과
wow
async 
*/
```
- <h2 style="color:red;">여기서 중요한것은 await을 붙일 수 있는 것은 Promise 함수 뿐만 아니라 일반함수여도 됨!</h2>
 
