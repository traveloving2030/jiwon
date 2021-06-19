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


<h1 style="color:red;"> 실제로 적용시 {블라블라} 에서 중괄호를 각각 하나씩 더해주어야 인식함!!</h1>

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

- 방법1 : 앞에서 배웠던 바와 같이 새로운 배열을 하나 선언하고 반복문을 통해 구현

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
        var data = [
            { "id": 88, "name": "crong", "content": "새로운글을 올렸어요", "like": 5, "comment": ["댓글이다", "잘했어요"] },
            { "id": 28, "name": "hary", "content": "전 오늘도 노래를 불렀어요", "like": 0, "comment": ["제발고만..", "듣고싶네요 그노래"] },
            { "id": 23, "name": "pororo", "content": "크롱이 항상 말썽을 피워서 행복해~", "like": 4, "comment": [] },
            { "id": 5, "name": "pobi", "content": "물고기를 한마리도 잡지 못하다니..", "like": 5, "comment": ["댓글이다", "멋진글이네요", "잘봤습니다"] }
        ];
        var newArray = [];
        data.forEach(function (v) {
            var resultHTML = bindTemplate(v);
            console.log(resultHTML);
            newArray.push(resultHTML);

        })
        var show = document.querySelector(".show");
        show.innerHTML = newArray;

    </script>
</body>

</html>
```

- 방법 2 : reduce 함수를 통해 구현
    - reduce 함수 : `누적된 합`을 구할때 유용!

```html
<!DOCTYPE html>
<html>

<head>
    <style>
        li {
            list-style: none;
            padding: 3%;
            border-top: 1px solid gray;
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
        var data = [
            { "id": 88, "name": "crong", "content": "새로운글을 올렸어요", "like": 5, "comment": ["댓글이다", "잘했어요"] },
            { "id": 28, "name": "hary", "content": "전 오늘도 노래를 불렀어요", "like": 0, "comment": ["제발고만..", "듣고싶네요 그노래"] },
            { "id": 23, "name": "pororo", "content": "크롱이 항상 말썽을 피워서 행복해~", "like": 4, "comment": [] },
            { "id": 5, "name": "pobi", "content": "물고기를 한마리도 잡지 못하다니..", "like": 5, "comment": ["댓글이다", "멋진글이네요", "잘봤습니다"] }
        ];

        var resultHTML = data.reduce(function(prev,next){
            return prev + bindTemplate(next);
        }, "");

        var show = document.querySelector(".show");
        show.innerHTML = resultHTML;
    </script>
</body>

</html>
```


## 조건 상황에 따른 처리

- 만약 댓글이 없는 경우 다른 메세지를 처리해야한다면?
    - handlebar에서는 if, else 문까지 모두 지원
    - if 앞의 #은 handlebar의 문법

```html
<script type="myTemplate" id="listTemplate">
    <li>
        <div>게시자 : {name}</div>
        <div class="content">{content}</div>
        <div>좋아요 갯수 <span> {like} </span></div>
        <div class="comment">
        <h3>댓글목록</h3>
        {#if comment}
            {#each comment}
                <div>{@index}번째 댓글 : {this}</div>
            {/each}
        {else}
            <div>댓글이 아직 없군요</div>
        {/if}
        </div>
    </li>
</script>
```


# handlebar를 활용한 템플릿 작업-3

- 위 코드에서 좋아요 갯수가 5개 이상이면 "축하해요. 좋아요가 5개이상 입니다"라는 문자열을 추가하고 싶다
    - likes라는 커스텀 항목 추가 `(#likes 라는 helper로 등록함)`


```html
<script type="myTemplate" id="listTemplate">
    <li>
        <div>게시자 : {name}</div>
        <div class="content">{content}</div>

        {#likes like}
            {like}
        {/likes}

        <div class="comment">
        <h3>댓글목록</h3>
        {#if comment}
            {#each comment}
                <div>{@index}번째 댓글 : {this}</div>
            {/each}
        {else}
            <div>댓글이 아직 없군요</div>
        {/if}
        </div>
    </li>
</script>	
```

- #likes라 helper를 찾아가주기 위해 helper를 등록해줘야함

```js
 Handlebars.registerHelper("likes", function (like) {
    if (like > 4) {
        return "<span>축하해요 좋아요가 " + like + "개 이상입니다!</span>";
    } else if (like < 1) {
        return "아직 아무도 좋아하지 않아요..";
    } else {
        return like + "개의 좋아요가 있네요";
    }
});
```

- 반영된 전체 코드

```html
<!DOCTYPE html>
<html>

<head>
    <style>
        li {
            list-style: none;
            padding: 3%;
            border-top: 1px solid gray;
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
    
            {#likes like}
                {like}
            {/likes}
    
            <div class="comment">
            <h3>댓글목록</h3>
            {#if comment}
                {#each comment}
                    <div>{@index}번째 댓글 : {this}</div>
                {/each}
            {else}
                <div>댓글이 아직 없군요</div>
            {/if}
            </div>
        </li>
    </script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.7/handlebars.min.js"
        integrity="sha512-RNLkV3d+aLtfcpEyFG8jRbnWHxUqVZozacROI4J2F1sTaDqo1dPQYs01OMi1t1w9Y2FdbSCDSQ2ZVdAC8bzgAg=="
        crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script>


        Handlebars.registerHelper("likes", function (like) {
            if (like > 4) {
                return "<span>축하해요 좋아요가 " + like + "개 이상입니다!</span>";
            } else if (like < 1) {
                return "아직 아무도 좋아하지 않아요..";
            } else {
                return like + "개의 좋아요가 있네요";
            }
        });
        
        var template = document.querySelector("#listTemplate").innerText;
        var bindTemplate = Handlebars.compile(template);
        var data = [
            { "id": 88, "name": "crong", "content": "새로운글을 올렸어요", "like": 5, "comment": ["댓글이다", "잘했어요"] },
            { "id": 28, "name": "hary", "content": "전 오늘도 노래를 불렀어요", "like": 0, "comment": ["제발고만..", "듣고싶네요 그노래"] },
            { "id": 23, "name": "pororo", "content": "크롱이 항상 말썽을 피워서 행복해~", "like": 4, "comment": [] },
            { "id": 5, "name": "pobi", "content": "물고기를 한마리도 잡지 못하다니..", "like": 5, "comment": ["댓글이다", "멋진글이네요", "잘봤습니다"] }
        ];

        var resultHTML = data.reduce(function (prev, next) {
            return prev + bindTemplate(next);
        }, "");

        var show = document.querySelector(".show");
        show.innerHTML = resultHTML;

    </script>
</body>

</html>
```