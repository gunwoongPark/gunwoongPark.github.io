---
layout: single
title: "자바스크립트 배열 내장함수"
categories: 
   - Javascript
author_profile: true
read_time: true
comments: true
---

최근 자바스크립트언어를 공부하고있다.

특히나 배열에서 자주 쓰일 것같은 내장 함수들을 정리해봤다.

해당 코드들은 현재 패스트캠퍼스의 '벨로퍼트'님의 예제를 따라 작성한 것이다.

<br/>
<br/>
<br/>

## forEach

'forEach' 는 기존의 우리가 배웠던 for문과 흡사하다.

특히 파이썬의 for문과 흡사하다.

파라미터로 특정 작업을 처리하는 함수를 넣어주어 유용하게 사용할 수 있다.

### 예제
```javascript
const superheroes = ["아이언맨", "캡틴 아메리카", "토르", "닥터 스트레인지"];

superheroes.forEach(hero => {
    console.log(hero);
});
```
### 결과
```
아이언맨
캡틴 아메리카
토르
닥터 스트레인지
```

<br/>
<br/>

## map

'map' 은 배열안의 요소들을 변화시킬 때 사용한다.

반환 값으로 변화된 새로운 배열이 반환된다.

파라미터로 함수를 넣어주어 유용하게 사용할 수 있다.

### 예제
```javascript
const array = [1, 2, 3, 4, 5, 6, 7, 8];

const square = n => n * n;
const squared = array.map(square);
console.log(squared);
```
### 결과
```
[
   1,  4,  9, 16,
  25, 36, 49, 64 
]
```

<br/>
<br/>

## indexOf

'indexOf' 는 배열의 요소가 몇 번째 인덱스에 있는지 찾아주어 반환하는 함수이다.

### 예제
```javascript
const superheroes = ["아이언맨", "캡틴 아메리카", "토르", "닥터 스트레인지"];

const index = superheroes.indexOf("토르");
console.log(index);
```
### 결과
```
2
```

<br/>
<br/>

## findIndex

'indexOf' 의 경우 단순히 배열에 요소마다 한 종류의 값만 저장되어 있는 경우 활용할 수 있는 반면,

'findIndex' 는 배열 안에 객체로 여러가지 속성 데이터들이 저장되어 있는 경우에 파라미터로 탐색기준을 함수형태로 넘겨 해당 요소가 몇 번째 인덱스에 있는지 찾아주어 반환하는 함수이다.

### 예제
```javascript
const todos = [
    {
        id: 1,
        text: "자바스크립트 입문",
        done: true
    },
    {
        id: 2,
        text: "함수 배우기",
        done: true
    },
    {
        id: 3,
        text: "객체와 배열 배우기",
        done: true
    },
    {
        id: 4,
        text: "배열 내장함수 배우기",
        done: false
    }
];

const index = todos.findIndex(todo => todo.id === 3);
console.log(index);
```
### 결과
```
2
```

<br/>
<br/>

## filter

'filter' 는 배열에서 특정 조건을 만족하는 배열을 반환하는 유용한 함수이다.

파라미터로 함수를 넣어 유용하게 사용할 수 있다.

### 예제
```javascript
const todos = [
    {
        id: 1,
        text: "자바스크립트 입문",
        done: true
    },
    {
        id: 2,
        text: "함수 배우기",
        done: true
    },
    {
        id: 3,
        text: "객체와 배열 배우기",
        done: true
    },
    {
        id: 4,
        text: "배열 내장함수 배우기",
        done: false
    }
];

const tasksNotDone = todos.filter(todo => todo.done === false);
console.log(tasksNotDone);
```
### 결과
```
[ { id: 4, text: '배열 내장함수 배우기', done: false } ]
```

<br/>
<br/>

## splice

'splice' 는 배열에서 특정 요소를 제거할 때 사용하는 함수이다.

반환하는 값이 따로 존재하지 않고, 함수를 사용한 배열 자체에 접근하여 값들을 변경시킨다.

### 예제
```javascript
const numbers = [10, 20, 30, 40];
const index = numbers.indexOf(30);

numbers.splice(index, 1);
console.log(numbers);
```
### 결과
```
[ 10, 20, 40 ]
```

<br/>
<br/>

## slice

'slice' 는 배열을 잘라 내어 새로운 배열을 반환하는 함수이다.

파이썬의 'slicing' 을 생각하면 편하다.

### 예제
```javascript
const numbers = [10, 20, 30, 40];
const sliced = numbers.slice(0, 2);

console.log(sliced);
console.log(numbers);
```
### 결과
```
[ 10, 20 ]        
[ 10, 20, 30, 40 ]
```

<br/>
<br/>

## shift

'shift' 는 배열의 첫 번째 요소를 추출하여 반환하는 함수이다.

### 예제
```javascript
const numbers = [10, 20, 30, 40];
const value = numbers.shift();

console.log(value);
console.log(numbers);
```
### 결과
```
10            
[ 20, 30, 40 ]
```

<br/>
<br/>

## pop

'pop' 은 'shift'와 달리 배열의 맨 마지막 요소를 추출하여 반환하는 함수이다.

### 예제
```javascript
const numbers = [10, 20, 30, 40];
const value = numbers.pop();

console.log(value);
console.log(numbers);
```
### 결과
```
40
[ 10, 20, 30 ]
```

<br/>
<br/>

## unshift

'unshift' 는 'shift' 함수와 반대로 생각하면 된다.

배열에 첫 번째 요소로 파라미터로 주어진 값을 삽입한다.

반환하는 값이 따로 존재하지 않고, 함수를 사용한 배열 자체에 접근하여 값들을 변경시킨다.

### 예제
```javascript
const numbers = [10, 20, 30, 40];
numbers.unshift(5);
console.log(numbers);
```
### 결과
```
[ 5, 10, 20, 30, 40 ]
```

<br/>
<br/>

## concat

'concat' 는 다수의 배열을 하나의 배열로 합쳐주는 함수이다.

'배열1.함수(배열2)' 이런식으로 사용하며 배열1 뒤에 배열2를 붙여서 새로운 배열을 반환한다.

### 예제
```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const concated = arr1.concat(arr2);

console.log(concated);
```
### 결과
```
[ 1, 2, 3, 4, 5, 6 ]
```

<br/>
<br/>

## join

'join' 은 배열을 파라미터로 받은 구분자를 각 요소들 사이에 넣어주어 문자열로 변환하여 반환하는 함수이다.

### 예제
```javascript
const array = [1, 2, 3, 4, 5];
console.log(array.join());
console.log(array.join(" "));
console.log(array.join(", "));
```
### 
```
1,2,3,4,5
1 2 3 4 5
1, 2, 3, 4, 5
```
