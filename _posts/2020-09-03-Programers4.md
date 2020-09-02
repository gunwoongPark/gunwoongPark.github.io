---
layout: single
title: "프로그래머스 [구명보트] [네트워크]"
categories: 
   - Algorithm
author_profile: true
read_time: true
comments: true
---

# 구명보트

프로그래머스 Level 2 단계 문제이다.

그리드 문제로 최소한의 보트 이용을 통해 사람들을 무인도에서 구출하는 문제이고, 당연히 보트에는 제한 무게가 존재한다.

알고리즘은 가장 무게가 적게 나가는 사람과 가장 많이 나가는 사람을 묶는 식으로 설계했다.

비교적 간단한 문제이다.

```javascript
function solution(people, limit) {
    people.sort(function (a, b) {
        return a - b;
    })

    var answer = 0;

    let lightIndex = 0;
    let heavyIndex = people.length - 1;

    while (lightIndex < heavyIndex) {
        if (people[lightIndex] + people[heavyIndex] <= limit) {
            lightIndex++;
            heavyIndex--;
        }
        else {
            heavyIndex--;
        }
        answer++;
    }
    if (lightIndex === heavyIndex)
        answer++;

    return answer;
}

result = solution([70, 80, 50], 100);

console.log(result);
```

# 네트워크

프로그래머스 무려 Level 3 단계 문제이다.

그래프의 개념만 알고 있으면 쉽게 풀 수 있는 문제이다.

필자는 **DFS 깊이 기반 탐색 기법**을 활용하여 알고리즘을 구현했다.

```javascript
function DFS(start, graph, visit) {
    if (visit[start] === true) {
        return 0;
    }

    visit[start] = true;

    for (let idx = 0; idx < graph[start].length; ++idx) {

        if (graph[start][idx] === 1) {
            DFS(idx, graph, visit)
        }
    }

    return 1;
}

function solution(n, computers) {
    let visit = new Array();
    var answer = 0;

    for (let idx = 0; idx < n; ++idx)
        visit.push(false);

    for (let idx = 0; idx < n; ++idx) {
        if (DFS(idx, computers, visit))
            answer++;
    }

    return answer;
}

console.log(solution(3, [[1, 1, 0], [1, 1, 1], [0, 1, 1]]))
```