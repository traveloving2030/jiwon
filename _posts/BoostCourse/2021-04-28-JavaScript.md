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


# 자바스크립트 객체

<문제1>

- 아래의 data 값이 존재할 때, key값, value값, value 중 숫자로만 이루어진 key값 찾기

```js
const data = {
    "debug": "on",
    "window": {
        "title": "Sample Konfabulator Widget",
        "name": "main_window",
        "width": 500,
        "height": 500
    },
    "image": { 
        "src": "Images/Sun.png",
        "name": "sun1",
        "hOffset": 250,
        "vOffset": 250,
        "alignment": "center"
    },
    "text": {
        "data": "Click Here",
        "size": 36,
        "style": "bold",
        "name": "text1",
        "hOffset": 250,
        "vOffset": 100,
        "alignment": "center",
        "onMouseUp": "sun1.opacity = (sun1.opacity / 100) * 90;"
    }
}
```

1. key값 객체에서 찾기

```js
for(key in data){
    console.log(key)
}
```

2. `key값을 배열로 받아오기` => 중요!

- key값 배열 뭉탱이로 받아오기
```js
console.log(Object.keys(data))
```
- key값 배열 쪼개기 => `forEach 구문!`
```js
console.log(Object.keys(data).forEach(function(v){
    console.log(v)
}))
```

3. value값 객체에서 찾기

```js
for(key in data){
    console.log(data[key])
}
```

4. value값 배열에서 찾기

```js
console.log(Object.keys(data).forEach(function(v){
    console.log(data[v])
}))

```

5. value 중 숫자로만 이루어진 key값 찾기
<h2 style="color:red;"> Value 값이 특정한 값만 뽑아낼때 유용하므로 꼭 기억!</h2>

```js
var result = [];

function findNum(d){

    for ( key in d ){    
        var value = d[key] ;
        if( typeof value === "object") findNum(value);
        else if ( typeof value === "number") result.push(key);
    }
}

findNum(data);

console.log(" result : " + result);
```



<문제2>

- type이 sk인 놈들만 다 뽑기

```js
[{
	"id": 1,
	"name": "Yong",
	"phone": "010-0000-0000",
	"type": "sk",
	"childnode": [{
		"id": 11,
		"name": "echo",
		"phone": "010-0000-1111",
		"type": "kt",
		"childnode": [{
				"id": 115,
				"name": "hary",
				"phone": "211-1111-0000",
				"type": "sk",
				"childnode": [{
					"id": 1159,
					"name": "pobi",
					"phone": "010-444-000",
					"type": "kt",
					"childnode": [{
							"id": 11592,
							"name": "cherry",
							"phone": "111-222-0000",
							"type": "lg",
							"childnode": []
						},
						{
							"id": 11595,
							"name": "solvin",
							"phone": "010-000-3333",
							"type": "sk",
							"childnode": []
						}
					]
				}]
			},
			{
				"id": 116,
				"name": "kim",
				"phone": "444-111-0200",
				"type": "kt",
				"childnode": [{
					"id": 1168,
					"name": "hani",
					"phone": "010-222-0000",
					"type": "sk",
					"childnode": [{
						"id": 11689,
						"name": "ho",
						"phone": "010-000-0000",
						"type": "kt",
						"childnode": [{
								"id": 116890,
								"name": "wonsuk",
								"phone": "010-000-0000",
								"type": "kt",
								"childnode": []
							},
							{
								"id": 1168901,
								"name": "chulsu",
								"phone": "010-0000-0000",
								"type": "sk",
								"childnode": []
							}
						]
					}]
				}]
			},
			{
				"id": 117,
				"name": "hong",
				"phone": "010-0000-0000",
				"type": "lg",
				"childnode": []
			}
		]
	}]
}]
```


```js
var arr=[];

function findSK(d){
    d.forEach(function(v){
        if(v.type=="sk"){
            arr.push(v.name);
        }
        if(v.childnode.length>0){
            findSK(v.childnode);
        }

    })
}


findSK(data)
console.log(arr);
```