---
layout: single
title: "프로그래머스 [모의고사] [키패드 누르기]"
categories: 
   - Algorithm
author_profile: true
read_time: true
comments: true
---

오랜만에 프로그래머스 코딩문제를 풀었다.

모든 문제는 Python을 활용하여 풀었다.

# 모의고사

## 문제설명

수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.

1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...

2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...

3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...

1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.

## 제한 조건

시험은 최대 10,000 문제로 구성되어있습니다.

문제의 정답은 1, 2, 3, 4, 5중 하나입니다.

가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.

## Code

```python
def solution(answers):
    list_supo = [[1,2,3,4,5],[2, 1, 2, 3, 2, 4, 2, 5],[3,3,1,1,2,2,4,4,5,5]]
    result_dict = {}

    for idx in range(3):
        supo = list_supo[idx]
        cool_count = 0
        jdx = 0
        for sol in answers:
            if sol == supo[jdx]:
                cool_count+=1
            jdx = (jdx+1) % len(supo)
        result_dict[idx+1] = cool_count

    result = []
    s_list = sorted(result_dict.items(), key=lambda x: x[1], reverse=True)

    result.append(s_list[0][0])

    for idx in range(1, 3):
        if s_list[idx][1] == s_list[0][1]:
            result.append(s_list[idx][0])

    return result
```

간단한 완전탐색 문제이다.

우선 떠오른점이 zip 이었으나 시험문제가 더 길게 나올경우를 대비하여 zip 을 채택하진 못했다.

시험문제가 수포자들이 찍는 패턴보다 더 길게 나온다면 반복하며 순회해줘야한다.

예전 자료구조 강의때 들었던 원형큐 구현 원리가 기억이 났다.

```python
jdx = (jdx+1) % len(supo)
```

이런식으로 계속 순회하며 시험문제를 모두 돌며 답을 확인한다.

이 후, 문제에서 원하는대로 오름차순으로 정렬하면 된다.

# [카카오 인턴] 키패드 누르기

## 문제설명

스마트폰 전화 키패드의 각 칸에 다음과 같이 숫자들이 적혀 있습니다.

![카카오 인턴십 키패드 문제](/../assets/img/programers_kakao.PNG)

이 전화 키패드에서 왼손과 오른손의 엄지손가락만을 이용해서 숫자만을 입력하려고 합니다.

맨 처음 왼손 엄지손가락은 * 키패드에 오른손 엄지손가락은 # 키패드 위치에서 시작하며, 엄지손가락을 사용하는 규칙은 다음과 같습니다.

엄지손가락은 상하좌우 4가지 방향으로만 이동할 수 있으며 키패드 이동 한 칸은 거리로 1에 해당합니다.

왼쪽 열의 3개의 숫자 1, 4, 7을 입력할 때는 왼손 엄지손가락을 사용합니다.

오른쪽 열의 3개의 숫자 3, 6, 9를 입력할 때는 오른손 엄지손가락을 사용합니다.

가운데 열의 4개의 숫자 2, 5, 8, 0을 입력할 때는 두 엄지손가락의 현재 키패드의 위치에서 더 가까운 엄지손가락을 사용합니다.

4-1. 만약 두 엄지손가락의 거리가 같다면, 오른손잡이는 오른손 엄지손가락, 왼손잡이는 왼손 엄지손가락을 사용합니다.

순서대로 누를 번호가 담긴 배열 numbers, 왼손잡이인지 오른손잡이인 지를 나타내는 문자열 hand가 매개변수로 주어질 때, 각 번호를 누른 엄지손가락이 왼손인 지 오른손인 지를 나타내는 연속된 문자열 형태로 return 하도록 solution 함수를 완성해주세요.

## 제한 사항

* numbers 배열의 크기는 1 이상 1,000 이하입니다.

* numbers 배열 원소의 값은 0 이상 9 이하인 정수입니다.

* hand는 "left" 또는 "right" 입니다.

* "left"는 왼손잡이, "right"는 오른손잡이를 의미합니다.

* 왼손 엄지손가락을 사용한 경우는 L, 오른손 엄지손가락을 사용한 경우는 R을 순서대로 이어붙여 문자열 형태로 return 해주세요.

## Code

```python
import numpy as np

def solution(numbers, hand):
    result = []
    left_thumb = 10
    right_thumb= 11
    keypad = np.array([[1,2,3],
                      [4,5,6],
                      [7,8,9],
                      [10,0,11]])

    for num in numbers:
        if num == 1 or num == 4 or num == 7:
            left_thumb = num
            result.append('L')
        elif num == 3 or num == 6 or num == 9:
            right_thumb = num
            result.append('R')

        if num == 2 or num == 5 or num == 8 or num == 0:
            key_idx = np.where(keypad == num)
            left_idx = np.where(keypad == left_thumb)
            right_idx = np.where(keypad == right_thumb)

            KLdistance = abs(key_idx[0][0] - left_idx[0][0]) + abs(key_idx[1][0] - left_idx[1][0])
            KRdistance = abs(key_idx[0][0] - right_idx[0][0]) + abs(key_idx[1][0] - right_idx[1][0])

            if KLdistance > KRdistance :
                right_thumb = num
                result.append('R')

            elif KLdistance < KRdistance :
                left_thumb = num
                result.append('L')

            else :
                if hand == "right":
                    right_thumb = num
                    result.append('R')
                else :
                    left_thumb = num
                    result.append('L')

    result = ''.join(result)
    return result
```

2020년도 카카오 인턴십 문제이다.

넘파이를 활용하여 배열사이의 거리를 계산하면 간단하게 해결할 수 있는 문제이다.

넘파이에서 where을 사용하면 간단하게 배열의 인덱스를 구할 수 있다.

눌러야 할 번호의 거리와 현재 손가락들의 위치를 뺀 절대값을 비교하여 더 가까운 손가락이 움직일 수도록 했다.

```python
key_idx = np.where(keypad == num)
            left_idx = np.where(keypad == left_thumb)
            right_idx = np.where(keypad == right_thumb)

            KLdistance = abs(key_idx[0][0] - left_idx[0][0]) + abs(key_idx[1][0] - left_idx[1][0])
            KRdistance = abs(key_idx[0][0] - right_idx[0][0]) + abs(key_idx[1][0] - right_idx[1][0])

            if KLdistance > KRdistance :
                right_thumb = num
                result.append('R')

            elif KLdistance < KRdistance :
                left_thumb = num
                result.append('L')

            else :
                if hand == "right":
                    right_thumb = num
                    result.append('R')
                else :
                    left_thumb = num
                    result.append('L')
```

추가적으로 거리가 같을경우는 오른손잡이인지 왼손잡이인지를 판단하여 손가락을 결정한다. 
