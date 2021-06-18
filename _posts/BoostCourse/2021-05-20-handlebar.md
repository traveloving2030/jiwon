---
layout: post
title: "handlerbar를 활용한 템플릿 작업"
date: 2021-05-20
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true
---


<h1 style="color:red;">{블라블라} 를 {{블라블라}} 로 바꿔줘야 인식함!!</h1>

# handlebar를 활용한 템플릿 작업-1

- html에 다음과 같이 template코드를 만듦

```html
<html>
    <script type="myTemplate" id="listTemplate">
        <li>
        <div>게시자 : {name}</div>
        <div class="content">{content}</div>
        <div>좋아요 갯수 <span> {like} </span></div>
        <div class="comment">
        <div>{comment}</div>
        </div>
    </li>
    </script>	
</html>

```

- javascript에서는 replace로 치환하는 방법 말고, 라이브러리를 사용해서 결과를 얻을 수 있음

```js
var template = document.querySelector("#listTemplate").innerText;
var bindTemplate = Handlebars.compile(template);  //bindTemplate은 메서드입니다.
```

- 데이터 추가

```js
var data = {
  	"id" : 88,
    "name" : "crong",
    "content" : "새로운글을 올렸어요",
    "like" : 5, 
    "comment" : "댓글이다"
};

bindTemplate(data);
```

- 전체 완성된 코드

```html
<!DOCTYPE html>
<html>

<head>
    <style>
        li {
            list-style: none;
        }
    </style>
</head>

<body>
    <section class="show">

    </section>
    <script type="myTemplate" id="listTemplate">
        <li>
            <div>게시자 : {name}</div>
            <div class="content">{content}</div>
            <div>좋아요 갯수 <span>{like}</span></div>
            <div class="comment">
                <div>{comment}</div>
            </div>
        </li>
    </script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.7/handlebars.min.js"
        integrity="sha512-RNLkV3d+aLtfcpEyFG8jRbnWHxUqVZozacROI4J2F1sTaDqo1dPQYs01OMi1t1w9Y2FdbSCDSQ2ZVdAC8bzgAg=="
        crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script>
        var template = document.querySelector("#listTemplate").innerText;
        var bindTemplate = Handlebars.compile(template);
        var data = {
            "id": 88,
            "name": "crong",
            "content": "새로운글을 올렸어요",
            "like": 5,
            "comment": "댓글이다"
        };

        var resultHTML = bindTemplate(data);
        console.log(resultHTML);
        var show = document.querySelector(".show");
        show.innerHTML=resultHTML;
    </script>
</body>

</html>
```


# handlebar를 활용한 템플릿 작업-2

- 넣어주는 data가 아래와 같이 comment 영역이 여러개 있을 때를 가정

 ```js
var data = {
  	"id" : 88,
    "name" : "crong",
    "content" : "새로운글을 올렸어요",
    "like" : 5, 
    "comment" : ["댓글이다", "멋진글이네요", "잘봤습니다"]
};
 ```

 - 위의 comment들을 하나씩 반복하면서 쪼개어주고싶다
    - #each 구문을 쓴다

 ```html
 <html>
 <script type="myTemplate" id="listTemplate">
    <li>
        <div>게시자 : {name}</div>
        <div class="content">{content}</div>
        <div>좋아요 갯수 <span> {like} </span></div>
        <div class="comment">
        <h3>댓글목록</h3>
        {#each comment}
            <div>{@index}번째 댓글 : {this}</div>
        {/each}
        </div>
    </li>
</script>	
 </html>

 ```

- 전체 완성된 코드

```html
<!DOCTYPE html>
<html>

<head>
    <style>
        li {
            list-style: none;
        }
    </style>
</head>

<body>
    <section class="show">

    </section>
    <script type="myTemplate" id="listTemplate">
        <li>
            <div>게시자 : {name}</div>
            <div class="content">{content}</div>
            <div>좋아요 갯수 <span> {like} </span></div>
            <div class="comment">
            <h3>댓글목록</h3>
            {#each comment}
                <div>{@index}번째 댓글 : {this}</div>
            {/each}
            </div>
        </li>
    </script>	
    <script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.7/handlebars.min.js"
        integrity="sha512-RNLkV3d+aLtfcpEyFG8jRbnWHxUqVZozacROI4J2F1sTaDqo1dPQYs01OMi1t1w9Y2FdbSCDSQ2ZVdAC8bzgAg=="
        crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script>
        var template = document.querySelector("#listTemplate").innerText;
        var bindTemplate = Handlebars.compile(template);
        var data = {
            "id": 88,
            "name": "crong",
            "content": "새로운글을 올렸어요",
            "like": 5,
            "comment": ["댓글이다", "멋진글이네요", "잘봤습니다"]
        };

        var resultHTML = bindTemplate(data);
        console.log(resultHTML);
        var show = document.querySelector(".show");
        show.innerHTML = resultHTML;
    </script>
</body>

</html>
```

## data자체가 많아진 경우의 처리

```js
var data = [
	{"id" : 88, "name" : "crong", "content" : "새로운글을 올렸어요", "like" : 5, "comment" : ["댓글이다", "잘했어요"]},
	{"id" : 28, "name" : "hary", "content" : "전 오늘도 노래를 불렀어요", "like" : 0, "comment" : ["제발고만..","듣고싶네요 그노래"]},
	{"id" : 23, "name" : "pororo", "content" : "크롱이 항상 말썽을 피워서 행복해~", "like" : 4, "comment" : []},
	{"id" : 5, "name" : "pobi", "content" : "물고기를 한마리도 잡지 못하다니..", "like" : 5, "comment" : ["댓글이다", "멋진글이네요", "잘봤습니다"]}
];
```