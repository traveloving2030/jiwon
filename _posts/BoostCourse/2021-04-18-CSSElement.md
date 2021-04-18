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

# 엘리먼트가 배치되는 방식

<div style="color:red; size:20px">
    display(block, inline, inline-block)
    position(static, absolute, relative, fixed)
    float(left, right)
</div>


1. display

ㄱ. block : display속성이 block이거나 inline-block인 경우 그 엘리먼트는 벽돌을 쌓든 블록을 가지고 쌓입니다.
    `<div>, <p> 태그 등 대부분의 태그들은 기본적으로 블록`

ㄴ. inline : display 속성이 inline인 경우는 우측으로, 그리고 아래쪽으로 빈자리를 차지하며 흐릅니다. 높이와 넓이를 지정해도 반영안됨
    `<span>, <a>, <strong> 태그는 기본적으로 inline`


2. position

ㄱ. position 속성으로 특별한 배치를 할 수 있습니다. position 속성은 기본 static입니다. 그냥 순서대로 배치됩니다.

ㄴ. absolute는 `기준점`에 따라서 특별한 위치에 위치합니다. `top / left / right / bottom` 으로 설정합니다.
    기준점을 상위엘리먼트로 단계적으로 찾아가는데 static이 아닌 position이 기준점입니다.

ㄷ. relative는 원래 자신이 위치해야 할 곳을 기준으로 이동합니다. top / left / right / bottom로 설정합니다.
 
ㄹ. fixed는 viewport(전체화면) 좌측, 맨 위를 기준으로 동작합니다