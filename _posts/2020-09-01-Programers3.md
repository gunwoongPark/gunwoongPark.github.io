---
layout: single
title: "프로그래머스 [추석 트래픽] [기능 개발]"
categories: 
   - Algorithm
author_profile: true
read_time: true
comments: true
---

# 추석 트래픽

프로그래머스 무려 Level 3 단계 문제이다.

그리드 문제로 **문자열을 변형**할 사례가 많아보여 파이썬을 이용해 풀었다.

밀리초까지 단위가 있어 편하게 계산하기 위해 **1000을 곱해주었다.**

문제에서는 서버에 타임아웃을 **3초**로 제시하여 알고리즘을 짜는데 이용할 수 있다.

**탐욕 문제**로 최선의 선택을 하되 시간 복잡도를 최소한으로 하는 알고리즘을 설계하였다.

```python
import re

def solution(lines):
    result_list = []
    for line in lines:
        log_str = line.split(' ')
        S = log_str[1]
        T = log_str[2]

        S = S.split(':')

        hour = S[0]
        minute = S[1]
        second = S[2]

        result = (int(hour)*60*60 + int(minute)*60 + float(second))*1000

        T = ''.join(re.findall('\d+', T))
        if (len(T) > 1):
            T = float(T[:1] + '.' + T[1:])
        else:
            T = float(T)

        result_list.append((result - (T*1000)+1, result))

    answer = []
    dummy = 1

    for idx in range(len(result_list)-1):
        count = 1
        for jdx in range(idx+1, len(result_list)):
            if result_list[jdx][0] - result_list[idx][1] < 1000:
                count+=1
            elif result_list[jdx][1] - result_list[idx][1] >= 4000:
                break
        answer.append(count)
    answer.append(dummy)
    max_answer = max(answer)

    if max_answer == 0:
        return 1

    return max_answer


final_result = solution([
'2016-09-15 01:00:04.001 2.0s',
'2016-09-15 01:00:07.000 2s'
])

print(final_result)
```

# 기능 개발

프로그래머스 Level 2 단계 문제이다.

자바스크립트를 이용하였고, **자료구조** **스택**이나 **큐**에 대한 간단한 지식만 있으면 수월하게 풀 수 있다.

```javascript
function solution(progresses, speeds) {
    var answer = [];
    let date = 0;
    let count = 0;
    let flag = false;
    while (true) {
        if (progresses.length === 0)
            break;
        for (let idx = 0; idx < progresses.length; ++idx) {
            progresses[idx] += speeds[idx];
        }
        while (progresses[0] >= 100) {
            progresses.shift();
            speeds.shift();
            count += 1;
            flag = true;
        }
        if (flag) {
            answer.push(count);
            count = 0;
            flag = false;
        }
    }
    return answer;
}

resultArray = solution([40, 93, 30, 55, 60, 65], [60, 1, 30, 5, 10, 7]);

console.log(resultArray);
```