---
layout: single
title: "자바스크립트 단축 평가 논리 계산법"
categories: 
   - Javascript
author_profile: true
read_time: true
comments: true
---

자바 스크립트를 공부하며 배운 개념인 단축 평가 논리 계산법에 대하여 알아보겠다.

이전에 배워왔던 논리 연산자를 통하여 조건문을 작성해 본 적이 많을 것이다.

하지만, 오늘 배운 단축 평가 논리 계산법을 활용하면 코드가 상당히 간결해지는 것은 물론 가독성도 좋아진다.

그럼 지금부터 알아보겠다.

# 기존의 &&(and) 와 ||(or)

기존의 '&&' 와 '&#124;&#124;' 논리 연산자는 두 가지 조건에 대하여 가정을 할 때 자주 사용되곤 했다.

간단한 예시를 곱셈을 할 경우 결과 부호를 정하는 함수를 사용하는 코드를 통해 들어보겠다.

```javascript
  function multiSign(num1, num2) {
  if (num1 > 0 && num2 > 0) {
    return "plus";
  } else if (num1 < 0 && num2 > 0) {
    return "minus";
  } else if (num1 > 0 && num2 < 0) {
    return "minus";
  } else if (num1 < 0 && num2 < 0) {
    return "plus";
  }
}

console.log(multiSign(1, 2));

console.log(multiSign(1, -2));

console.log(multiSign(-1, 2));

console.log(multiSign(-1, -2));
```

```
plus
minus
minus
plus
```

다음과 같이 출력이 됨을 볼 수 있다.

이처럼 유용하게 쓰이는 '&&', '&#124;&#124;' 연산자이다. 

# 단축 평가 논리 계산법

이제부터는 조건문에서 자주 유용하게 쓰일 단축 평가 논리 계산법에 대하여 차례로 알아보겠다.

## && 연산자

우선 '&&' 연산자부터 간단한 출력 예시 코드를 통해 알아보겠다.

```javascript
console.log(true && "hello");
console.log(false && "hello");
console.log("hello" && "bye");
console.log(null && "hello");
console.log(undefined && "hello");
console.log("" && "hello");
console.log(0 && "hello");
console.log(1 && "hello");
console.log([] && "hello");
console.log(1 && 1);
```

```
hello
false
bye
null
undefined
""
0
hello
hello
1
```

'&&' 연산자는 '&&'연산자를 기준으로 좌측값이 'Truthy' 한 값이라면 우측값을 출력하고 아닐 경우 좌측값을 출력하는 예시코드이다.

> 'Truthy' 란 말 그대로 참값을 가질것만 같은 값을 의미한다. 대표적인 예로 true, 0이 아닌 수, 문자열, 배열등이 존재한다.

## || 연산자

다음은 '&#124;&#124;' 연산자에 대한 간단한 출력 예시 코드를 통해 알아보겠다.

```javascript
console.log(false || "hello");
console.log("" || "hello");
console.log(null || "hello");
console.log(undefined || "hello");
console.log(0 || 1);
console.log(1 || 0);
console.log(true || "hello");
console.log("hi" || "hello");
console.log([] || "hello");
```

```
hello
hello
hello
hello
1
1
true
hi
[]
```

눈치빠른 사람이라면 미리 알 수 있듯 '&&' 연산자와 반대로 '&#124;&#124;' 연산자는 '&#124;&#124;'연산자를 기준으로 좌측값이 'Falsy' 한 값이라면 우측값을 출력하고 아닐 경우 좌측값을 출력하는 예시코드이다.

> 'Falsy' 란 말 그대로 거짓값을 가질것만 같은 값을 의미한다. 대표적인 예로 false, null, undefined, 0, 빈 문자열등이 존재한다.
