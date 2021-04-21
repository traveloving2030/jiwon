---
layout: post
title: "CSS Element 배치 방법"
date: 2021-04-17
excerpt: "web"
tag:
- WebProgramming
- servlet
- jsp
- tomcat
category: [ BoostCourse ]
comments: true

---

# 엘리먼트가 배치되는 방식 1

<div style="color:red; size:20px">
    display(block, inline, inline-block)
    position(static, absolute, relative, fixed)
    float(left, right)
</div>


## display

1. block : display속성이 block이거나 inline-block인 경우 그 엘리먼트는 벽돌을 쌓든 블록을 가지고 쌓입니다.
    `<div>, <p> 태그 등 대부분의 태그들은 기본적으로 블록`

2. inline : display 속성이 inline인 경우는 우측으로, 그리고 아래쪽으로 빈자리를 차지하며 흐릅니다. 높이와 넓이를 지정해도 반영안됨
    `<span>, <a>, <strong> 태그는 기본적으로 inline`


## position

1. position 속성으로 특별한 배치를 할 수 있습니다. position 속성은` 기본 static`입니다. 그냥 순서대로 배치됩니다.

2. absolute는 `기준점`에 따라서 특별한 위치에 위치함. `top / left / right / bottom` 으로 설정. 이때 `top, left` 값은 꼭 준다
    기준점을 상위엘리먼트로 단계적으로 찾아가는데 static이 아닌 position이 기준점입니다.
    ex) 상위엘리먼트가 static이다? 오 기준점 아니야!
        다시 위로 올라가서 엘리먼트가 relative야? 오 너가 나의 기준점!

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/7.PNG" width = "70%" />

- 여기서 absolute의 기준점은 상위 엘리먼트인 wrap 클래스가 되고 position = relative이므로 기준점이 될 수 있다!


3. relative는 원래 자신이 위치해야 할 곳을 기준(`static 기준`)으로 이동합니다. top / left / right / bottom로 설정합니다.
 
4. fixed는 viewport(전체화면) 좌측, 맨 위를 기준으로 동작합니다 . ex) 광고처럼 스코롤 해도 그대로 유지되는것

관련 코드 : http://jsbin.com/vegowut/edit?html,css,output



# 엘리먼트가 배치되는 방식 2

## Margin

- Margin으로도 배치 할 수 있음!

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/8.PNG" width = "70%" />



# CSS 레이아웃 실습

## 기본 소스코드
```html
<!DOCTYPE html>
<html>
<head>
    <style>
        header {
            background-color: #eee;
        }

        #wrap {
            background-color: #ccc;
        }

        footer {
            background-color: #eee;
        }

        .left{

        }
    </style>
</head>
<body>
    <header>부스트코스는 유익합니다!</header>
    <div id="wrap">
        <nav class="left">
            <ul>
                <li>menu</li>
                <li>home</li>
                <li>name</li>
            </ul>
        </nav>
        <div class="right"><h2>반가워요!</h2>
        <ul>
            <li>crong</li>
            <li>jk</li>
            <li>honux</li>
            <li>pobi</li>
        </ul>
        </div>
    </div>
    <footer>코드스쿼드(주)</footer>
</body>
</html> 

```

- 여기서 nav바는 왼쪽으로, 나머지는 오른쪽으로 넘기고 싶다! ----- `float 활용!!!`  

```html
<!DOCTYPE html>
<html>
<head>
    <style>
        header {
            background-color: #eee;
        }

        #wrap {

             /* 자식이 float인 경우 자기의 자식으로 생각하지 않는다(둥 떠있기때문) 
              ===> overflow 속성을 집어넣으면 자식으로 생각하게 해준다
              */
            overflow: auto;
            /* margin top : 10px, bottom : 0px*/
            margin: 20px 0px; 
        }

        footer {
            background-color: #eee;
            /* 다른데서 float을 주더라도 footer는 가만히 있게 함 */
            clear: both; 
        }

        .left, .right, .realright{
            /* nav바는 왼쪽으로, 나머지는 오른쪽 */
            float: left;
            height: 200px;
        }

        .left{
            width: 17%;
            margin-right: 3%;
            background-color: #c7c;
        }
         
        .right{
            width: 60%;
            text-align: center;
            background-color: #a7c;
        }
        .right > h2{
            position: relative;
        }

        .right .emoticon{
            position: absolute;
            top: 0px;
            right: 5%;
            color: #fff;
        }

        .realright{
            width: 17%;
            margin-left: 3%;
            background-color: #a7c;
        }
        li{
            list-style: none;
        }


    </style>
</head>
<body>
    <header>부스트코스는 유익합니다!</header>
    <div id="wrap">
        <nav class="left">
            <ul>
                <li>menu</li>
                <li>home</li>
                <li>name</li>
            </ul>
        </nav>
        <div class="right">
            <h2>
            <span>반가워요!</span>
            <div class="emoticon">:-)</div>
            </h2>
        <ul>
            <li>crong</li>
            <li>jk</li>
            <li>honux</li>
            <li>pobi</li>
        </ul>
        </div>
        <div class="realright">
            나는 오른쪽!
        </div>
    </div>
    <footer>코드스쿼드(주)</footer>
</body>
</html>
```

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/9.PNG" width = "70%" />
