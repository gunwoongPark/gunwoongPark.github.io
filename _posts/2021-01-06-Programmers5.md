---
layout: single
title: "프로그래머스 [두 개 뽑아서 더하기] [124 나라의 숫자] [프린터]"
categories:
  - Algorithm
author_profile: true
read_time: true
comments: true
---

# 두 개 뽑아서 더하기

### 문제 설명

정수 배열 numbers가 주어집니다. numbers에서 서로 다른 인덱스에 있는 두 개의 수를 뽑아 더해서 만들 수 있는 모든 수를 배열에 오름차순으로 담아 return 하도록 solution 함수를 완성해주세요.

### 제한 사항

- numbers의 길이는 2 이상 100 이하입니다.
  - numbers의 모든 수는 0 이상 100 이하입니다.

### 입출력 예

|     numbers     |       result       |
| :-------------: | :----------------: |
| [2, 1, 3, 4, 1] | [2, 3, 4, 5, 6, 7] |
|  [5, 0, 2, 7]   |  [2, 5, 7, 9, 12]  |

### 코드

```javascript
function solution(numbers) {
  var answer = [];

  for (let idx = 0; idx < numbers.length; ++idx) {
    for (let jdx = idx + 1; jdx < numbers.length; ++jdx) {
      if (answer.length < 1) answer.push(numbers[idx] + numbers[jdx]);
      else if (answer.indexOf(numbers[idx] + numbers[jdx]) == -1)
        answer.push(numbers[idx] + numbers[jdx]);
    }
  }

  answer.sort((a, b) => {
    return a - b;
  });

  return answer;
}
```

### 리뷰

자바스크립트를 사용하여 코드를 작성했다.

문제는 간단하게 배열을 순회할 수 있는지를 묻는 문제이다.

이중 반복문을 통해 배열을 순회하며 더하여 나올 수 있는 모든 수를 중복 없이 새로운 배열에 저장하고,

저장한 배열을 오름차순으로 정렬하기 위해 배열 내장함수인 sort를 활용해 간단하게 오름차순으로 정렬했다.

자바스크립트의 유용한 함수를 잘 활용하지 못한 것 같아 아쉽다.

---

# 124 나라의 숫자

### 문제 설명

124 나라가 있습니다. 124 나라에서는 10진법이 아닌 다음과 같은 자신들만의 규칙으로 수를 표현합니다.

1. 124 나라에는 자연수만 존재합니다.
2. 124 나라에는 모든 수를 표현할 때 1, 2, 4만 사용합니다.

예를 들어서 124 나라에서 사용하는 숫자는 다음과 같이 변환됩니다.

| 10진법 | 124나라 | 10진법 | 124나라 |
| ------ | ------- | ------ | ------- |
| 1      | 1       | 6      | 14      |
| 2      | 2       | 7      | 21      |
| 3      | 4       | 8      | 22      |
| 4      | 11      | 9      | 24      |
| 5      | 12      | 10     | 41      |

자연수 n이 매개변수로 주어질 때, n을 124 나라에서 사용하는 숫자로 바꾼 값을 return 하도록 solution 함수를 완성해 주세요.

### 제한 사항

- n은 500,000,000이하의 자연수 입니다.

### 입출력 예

|  n  | result |
| :-: | :----: |
|  1  |   1    |
|  2  |   2    |
|  3  |   4    |
|  5  |   11   |

### 코드

```javascript
function solution(n) {
  var answer = "";

  while (n > 0) {
    let temp = n % 3;
    n = parseInt(n / 3);

    if (!temp) {
      n -= 1;
      temp = 4;
    }

    answer = temp + answer;
  }

  return answer;
}
```

### 리뷰

생각보다 어려운 문제였다.

A4용지에서 규칙을 찾다 겨우 발견했다.

우선 3개 단위로 숫자가 변경되기 때문에 인자로 주어진 수가 3으로 나누어서 0으로 나누어 떨어지는지 판단한다.

이 때, 0으로 나누어 떨어질 경우 해당 숫자를 1 감소하고 정답으로 반환할 문자열에 4를 붙여준다.

이를 반복하여 주어진 수가 0이 될 때 까지 반복한다.

---

# 프린터

### 문제 설명

일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.

1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.

예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.

내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.

현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.

### 제한 사항

- 현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.
- 인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.
- location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.

### 입출력 예

| priorities         | location | return |
| ------------------ | -------- | ------ |
| [2, 1, 3, 2]       | 2        | 1      |
| [1, 1, 9, 1, 1, 1] | 0        | 5      |

### 코드

```javascript
function solution(priorities, location) {
  var answer = 0;

  for (let idx = 0; idx < priorities.length; ++idx) {
    let judge = "";
    location == idx ? (judge = "O") : (judge = "X");

    priorities[idx] = {
      num: priorities[idx],
      judge,
    };
  }

  while (priorities.length > 0) {
    let max = Math.max.apply(
      Math,
      priorities.map((print) => {
        return print.num;
      })
    );

    if (priorities[0].num < max) {
      priorities.push(priorities[0]);
      priorities.shift();
    } else {
      answer += 1;
      if (priorities[0].judge === "O") break;
      priorities.shift();
    }
  }

  return answer;
}
```

### 리뷰

우선순위 큐를 사용하는 문제이다.

주어지는 프린트 큐 배열의 내부를 객체 형태로 수정 해 각 문서를 구분할 수 있도록 한다.

이 후, 간단한 반복문과 배열 내장함수를 잘 사용하면 해결할 수 있는 비교적 쉬운 문제이다.

이번 문제를 통해 새롭게 알게 된 것은 바로 `Math` 내장 객체이다.

이 객체를 사용해 배열 내부에서 가장 높은 중요도의 값을 간편하게 추출할 수 있었다.
