---
layout: post
title: "HTML Templating"
date: 2021-05-04
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# HTML Templating 작업

- 반복적인 HTML부분을 template로 만들어두고, 서버에서 온 데이터(주로JSON)을 결합해서, 화면에 추가하는 작업

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/37.png" height="280" width="300" />


## HTML Template과 json 결합

```js
var data = {  title : "hello",
              content : "lorem dkfief",
              price : 2000
           };
var html = "<li><h4>{title}</h4><p>{content}</p><div>{price}</div></li>";

html.replace("{title}", data.title)
    .replace("{content}", data.content)
    .replace("{price}", data.price)
```

- 만약 data가 배열형태로 여러개가 있다면 어떻게 처리할까?

```js
var data =[
    {
        title : "hello",
        content : "lorem oka",
        price : 2000
    },
    {
        title : "bye",
        content : "lorem oka",
        price : 2000
    },
    {
        title : "goodnight",
        content : "lorem oka",
        price : 2000
    }
];
var html="<li><h4>{title}</h4><p>{content}</p><div>{price}</div></li>";
var resultHtml =[];
data.forEach(function(c) {
    var result = html.replace("{title}", c.title)
                     .replace("{content}", c.content)
                     .replace("{price}", c.price);
    resultHtml.push(result);
    
});

console.log(resultHtml);
```



## HTML Template 보관

- HTML코드 안에 숨겨둠(간단한 로직)
- 서버에서 file로 보관하고 Ajax로 요청해서 받아옴(숨겨야 할 데이터가 많다면 별도 파일로 분리해서 Ajax로 가져오는 방법)

- 아래 코드 결과 console확인해보기

```html
<!DOCTYPE html>
<html>

<head>
</head>

<body>
    <h2>Template Test</h2>
    <div class="content"></div>
</body>

<script id="template-List-item" type="text/template">
    <li>
        <h4>{title}</h4>
        <p>{content}</p>
        <div>{price}</div>
    </li>
</script>

<script>
    var html=document.querySelector("#template-List-item").innerHTML;
    console.log(html); 
</script>

</html>
```


- 데이터 가져오기 실습

```html
<!DOCTYPE html>
<html>

<head>
</head>

<body>
    <h2>Template Test</h2>
    <div class="content"></div>
</body>

<script id="template-List-item" type="text/template">
    <li>
        <h4>{title}</h4>
        <p>{content}</p>
        <div>{price}</div>
    </li>
</script>

<script>
    //mock data
    var data = [
        {title : "hello",content : "lorem dkfief",price : 2000},
        {title : "hello",content : "lorem dkfief",price : 2000}
    ];
    var html=document.querySelector("#template-List-item").innerHTML;
    var resultHTML=html.replace("{title}", data[0].title)
        .replace("{content}", data[0].content)
        .replace("{price}", data[0].price)
    
    document.querySelector(".content").innerHTML = resultHTML;
</script>

</html>
```


- 여러개의 데이터에 대해 HTML Template 보관 방법

```html
<!DOCTYPE html>
<html>

<head>
</head>

<body>
    <h2>Template Test</h2>
    <div class="content"></div>
</body>

<script id="template-List-item" type="text/template">
    <li>
        <h4>{title}</h4>
        <p>{content}</p>
        <div>{price}</div>
    </li>
</script>

<script>
    //mock data
    var data = [
        {title : "hello",content : "lorem dkfief",price : 2000},
        {title : "hello2",content : "sdfdsf",price : 3000}
    ];
    var resultHTML = [];
    var html=document.querySelector("#template-List-item").innerHTML;
    
    data.forEach(function(c){
        var result=html.replace("{title}", c.title)
        .replace("{content}", c.content)
        .replace("{price}", c.price)
        resultHTML.push(result);
    })
    document.querySelector(".content").innerHTML = resultHTML;
</script>

</html>
```