---
layout: single
title: "프로그래머스 [다리를 지나는 트럭] [카펫] [소수 찾기]"
categories:
  - Algorithm
author_profile: true
read_time: true
comments: true
---

# 다리를 지나는 트럭

### 문제 설명

트럭 여러 대가 강을 가로지르는 일 차선 다리를 정해진 순으로 건너려 합니다. 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지 알아내야 합니다. 트럭은 1초에 1만큼 움직이며, 다리 길이는 bridge_length이고 다리는 무게 weight까지 견딥니다.

> 트럭이 다리에 완전히 오르지 않은 경우, 이 트럭의 무게는 고려하지 않습니다.

예를 들어, 길이가 2이고 10kg 무게를 견디는 다리가 있습니다. 무게가 [7, 4, 5, 6]kg인 트럭이 순서대로 최단 시간 안에 다리를 건너려면 다음과 같이 건너야 합니다.

| 경과 시간 | 다리를 지난 트럭 | 다리를 건너는 트럭 | 대기 트럭 |
| :-------: | :--------------: | :----------------: | :-------: |
|     0     |        []        |         []         | [7,4,5,6] |
|    1~2    |        []        |        [7]         |  [4,5,6]  |
|     3     |       [7]        |        [4]         |   [5,6]   |
|     4     |       [7]        |       [4,5]        |    [6]    |
|     5     |      [7,4]       |        [5]         |    [6]    |
|    6~7    |     [7,4,5]      |        [6]         |    []     |
|     8     |    [7,4,5,6]     |         []         |    []     |

따라서, 모든 트럭이 다리를 지나려면 최소 8초가 걸립니다.

solution 함수의 매개변수로 다리 길이 bridge_length, 다리가 견딜 수 있는 무게 weight, 트럭별 무게 truck_weights가 주어집니다. 이때 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지 return 하도록 solution 함수를 완성하세요.

### 제한 사항

- bridge_length는 1이상 10,000 이하입니다.
- weight는 1이상 10,000 이하입니다.
- truck_weights의 길이는 1이상 10,000 이하입니다.
- 모든 트럭의 무게는 1이상 weight 이하입니다.

### 입출력 예

| bridge_length | weight |          truck_weights          | return |
| :-----------: | :----: | :-----------------------------: | :----: |
|       2       |   10   |            [7,4,5,6]            |   8    |
|      100      |  100   |              [10]               |  101   |
|      100      |  100   | [10,10,10,10,10,10,10,10,10,10] |  110   |

### 코드

```javascript
function solution(bridge_length, weight, truck_weights) {
  var time = 0;
  let bridge = [];
  let maxWeight = 0;

  for (let idx = 0; idx < bridge_length; ++idx) {
    bridge.push(0);
  }

  bridge.unshift(truck_weights.shift());
  bridge.pop();
  time += 1;

  maxWeight += bridge[0];

  while (maxWeight !== 0) {
    maxWeight -= bridge.pop();
    time += 1;

    if (weight - maxWeight >= truck_weights[0]) {
      bridge.unshift(truck_weights.shift());
      maxWeight += bridge[0];
    } else {
      bridge.unshift(0);
    }
  }

  return time;
}
```

### 리뷰

큐를 사용하면 되는 문제이다.

큐를 이용해 가상으로 다리를 만들고 배열의 내장함수를 활용해 트럭이 이동하는 모양을 구현하면 된다.

초반에 생각을 어렵게 하여 다리를 구현할 생각보다는 반복문을 여러 중첩으로 구현하여 시간이 조금 걸렸다.

코드를 작성하는 데 있어 좀 더 익숙해져야 겠다.

---

# 카펫

### 문제 설명

Leo는 카펫을 사러 갔다가 아래 그림과 같이 중앙에는 노란색으로 칠해져 있고 테두리 1줄은 갈색으로 칠해져 있는 격자 모양 카펫을 봤습니다.

![카펫 문제](/../assets/img/programers_carpet.PNG)

Leo는 집으로 돌아와서 아까 본 카펫의 노란색과 갈색으로 색칠된 격자의 개수는 기억했지만, 전체 카펫의 크기는 기억하지 못했습니다.

Leo가 본 카펫에서 갈색 격자의 수 brown, 노란색 격자의 수 yellow가 매개변수로 주어질 때 카펫의 가로, 세로 크기를 순서대로 배열에 담아 return 하도록 solution 함수를 작성해주세요.

### 제한 사항

- 갈색 격자의 수 brown은 8 이상 5,000 이하인 자연수입니다.
- 노란색 격자의 수 yellow는 1 이상 2,000,000 이하인 자연수입니다.
- 카펫의 가로 길이는 세로 길이와 같거나, 세로 길이보다 깁니다.

### 입출력 예

| brown | yellow | return |
| :---: | :----: | :----: |
|  10   |   2    | [4, 3] |
|   8   |   1    | [3, 3] |
|  24   |   24   | [8, 6] |

### 코드

```javascript
function divisors(integer) {
  let result = [];
  for (var i = 1; i <= integer; i++) {
    if (integer % i == 0) {
      result.push(i);
    }
  }
  return result;
}

function solution(brown, yellow) {
  var answer = [];

  let divArr = divisors(yellow);
  let hafArr;
  if (divArr.length % 2 === 0) {
    hafArr = divArr.slice(0, divArr.length / 2);
  } else {
    hafArr = divArr.slice(0, divArr.length / 2 + 1);
  }

  let YW = 0;
  let YH = 0;

  for (let idx = 0; idx < hafArr.length; ++idx) {
    YW = yellow / hafArr[idx];
    YH = hafArr[idx];
    if (brown === (YW + 2) * 2 + YH * 2) {
      break;
    }
  }

  answer.push(YW + 2, YH + 2);

  return answer;
}
```

### 리뷰

Broute Force 문제이다.

노란색 타일 개수의 약수를 구하여 수가 적은 쪽을 사각형의 세로 길이의 개수로 지정하여 알고리즘을 구현한다.

이 후 타일의 가로 길이와 세로 길이는 +2등을 더하여 구한다.

---

# 소수 찾기

### 문제 설명

한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.

### 제한 사항

- numbers는 길이 1 이상 7 이하인 문자열입니다.
- numbers는 0~9까지 숫자만으로 이루어져 있습니다.
- "013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.

### 입출력 예

| numbers | return |
| :-----: | :----: |
|  "17"   |   3    |
|  "011"  |   2    |

### 코드

```javascript
function checkNum(number) {
  if (number === 1 || number === 0) return false;
  let count = 0;
  for (let idx = 1; idx <= number; ++idx) {
    if (number % idx === 0) count += 1;
  }

  if (count == 2) return true;
  else return false;
}

const getPermutations = function (arr, selectNumber) {
  const results = [];
  if (selectNumber === 1) return arr.map((value) => [value]);

  arr.forEach((fixed, index, origin) => {
    const rest = [...origin.slice(0, index), ...origin.slice(index + 1)];
    const permutations = getPermutations(rest, selectNumber - 1);
    const attached = permutations.map((permutation) => [fixed, ...permutation]);
    results.push(...attached);
  });

  return results;
};

function solution(numbers) {
  var answer = [];

  let strArr = numbers.split("");
  let uniqueArr = [];

  for (let idx = 1; idx <= strArr.length; ++idx) {
    const resultArr = getPermutations(strArr, idx);
    uniqueArr = [];

    resultArr.forEach((el) => {
      let temp = el.join("");
      temp = parseInt(temp);
      // if (temp.length === idx)
      uniqueArr.push(temp);
    });

    const set = new Set(uniqueArr);
    uniqueArr = [...set];

    uniqueArr.forEach((el) => {
      if (checkNum(parseInt(el))) answer.push(parseInt(el));
    });
  }

  const set = new Set(answer);
  answer = [...set];

  return answer.length;
}
```

### 리뷰

Broute Force 문제이다.

소수를 찾는 것은 간단하다.

하지만 순열이 더해져 살짝 복잡해졌다고 볼 수 있다.

Javascript의 경우에는 순열을 반환하는 내장함수나 라이브러리가 존재하지 않아 이를 구현하는 데 있어 복잡했다.
