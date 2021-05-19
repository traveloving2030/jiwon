---
layout: post
title: "requestAnimationFrame & CSS3 Transition 활용"
date: 2021-05-02
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# requestAnimationFrame 활용

```html
<!DOCTYPE html>
<html>
<header>
    <style>
        .outside{
            position: relative;
            background-color: blueviolet;
            width: 100px;
            font-size: 0.8em;
            color: #fff;
        }
    </style>
</header>
<body>
    <div class="outside">제가 좋아하는 과일은...</div>
</body>
<script>
    var count = 0;
    var el = document.querySelector(".outside");
    el.style.left="0px";

    function run(){
        if(count>30){
            return;
        }
        count += 1;
        el.style.left = parseInt(el.style.left) + count + "px";
        requestAnimationFrame(run);
    }
    requestAnimationFrame(run);
</script>
</html>
```

# CSS3 Transition 활용

- CSS Transition을 이용하는 것이 `가장 빠르다`!!
- `transition` : 한 가지 상태에서 다른 상태로 변화함
- transition: `트렌지션 속성`, `트렌지션 실행시간`, `트렌지션 실행되는 속도`, `언제 트렌지션 시작할지`

- 아래 예시의 transition은 모든 속성을 지원하고 0.25초 동안 실행되며, 기본값(천천히 시작해서 빠르게 진행하며 천천히 끝남)의 속도로, 지연없이

```css
    style{
        transition: all 0.25s ease 0s; width: 36480px;"
    }
```



- 예제 1-1
    - 바로 아래 코드는 VScode에서 하면 적용안됨. 밑에 script 부분을 크롬 개발자도구에서 적용
    - 글자가 풍선처럼 부풀어오름

```html
<!DOCTYPE html>
<html>
<header>
    <style>
        .outside{
            position: relative;
            background-color: blueviolet;
            width: 100px;
            margin-top: 100px;
            font-size: 0.8em;
            color: #fff;

        }
    </style>
</header>
<body>
    <div class="outside">제가 좋아하는 과일은...</div>
</body>
</html>
```

```js
    var base = document.querySelector(".outside");
    base.style.transform="scale(2)";
    base.style.transition="transform 2s"
    base.style.left = "300px"; 
```



- 예제 1-2
    - translate3D를 이용하여 transform 후 transition 

```html
<!DOCTYPE html>
<html>
<header>
    <style>
        .outside{
            position: relative;
            background-color: blueviolet;
            width: 100px;
            margin-top: 100px;
            font-size: 0.8em;
            color: #fff;
        }
    </style>
</header>
<body>
    <div class="outside">제가 좋아하는 과일은...</div>
</body>

</html>
```

```js
    var base = document.querySelector(".outside");
    base.style.transform="translate3d(50px, 0px, 0px)";
    base.style.transition="all 0.25s ease 3s";
```


- 예제 2
    - 버튼을 클릭할때 오른쪽으로 이동시키기

```html
<!DOCTYPE html>
<html>
<header>
    <style>
        .outside{
            position: relative;
            background-color: blueviolet;
            width: 100px;
            margin-top: 100px;
            font-size: 0.8em;
            color: #fff;
            transform: scale(1);
            /* ease-in-out : 천천히 시작해서 천천히 끝남 */
            transition: left 3s ease-in-out; 
            
        }
    </style>
</header>
<body>
    <div class="outside" style="left: 100px;">제가 좋아하는 과일은...</div>
    <button>right!!</button>
</body>
<script>
    var target = document.querySelector(".outside");
    var btn = document.querySelector("button");
    btn.addEventListener("click", function(){
        var pre = parseInt(target.style.left);
        target.style.left=pre+100+"px";
    })
</script>
</html>
```


- 예제 3
    - 엘리먼트에 transition 속성을 주고, hover했을 때 좌측 상단에서, 우측 하단으로 움직이는 animation

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <style>
    .hi {
    color: blue;
    background-color: yellow;
    width: 50px;
    height: 50px;
    padding: 10px;
    transition: transform 2s;
    transform: translate(0px, 0px);
    }
  </style>
</head>
<body>
  <div class="hi">Hi there</div>
</body>
<script>
    var hi = document.querySelector(".hi");
    hi.addEventListener("mouseover", function(){
    hi.style.transform = "translate("+500+"px, "+500+ "px)";
});
</script>
</html>
```