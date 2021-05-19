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

