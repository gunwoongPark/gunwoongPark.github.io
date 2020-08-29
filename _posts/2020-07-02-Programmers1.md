---
layout: single
title: "프로그래머스 [크레인 인형뽑기 게임] [완주하지 못한 선수] [K번째수]"
categories: 
   - Algorithm
author_profile: true
read_time: true
comments: true
---

# 크레인 인형뽑기 게임

정사각형 모양의 N x N 모양의 2차원 배열로 이루어진 공간에서 정해진 위치로 순서대로 크레인을 이동하며 인형을 뽑는다.

뽑은 인형은 바구니에 넣는데 바구니에 넣은 인형이 같은 종류가 붙어있을 경우 해당 인형은 그 바구니에서 사라지고 사라진 인형의 수 만큼 count가 올라간다.

크레인이 내려가다 바닥에 닿는다면 즉 아무런 인형이 없다면 다시 올라온다.

![crain](/../assets/img/crain.PNG)

## Code

```python
def solution(board, moves):
    answer = 0
    basket = []

    for idx in range(len(moves)):
        width = moves.pop(0) - 1

        for height in range(len(board[0])):
            if board[height][width] == 0:
                continue
            else :
                basket.append(board[height][width])
                if len(basket) >= 2:
                    if basket[-1] == basket[-2]:
                        basket.pop()
                        basket.pop()
                        answer += 2

                board[height][width] = 0
                break

    return answer
```

### Details

크레인이 닿을 위치를 지정하는 원소들이 모여있는 리스트 'moves'에서 순서대로 하나씩 값을 pop한다.

```python
width = moves.pop(0) - 1
```

pop된 값은 2차원 배열의 행을 의미한다.

이 후 pop한 2차원 배열을 통해 반복문을 돌며 비어있는 공간(0)일 경우는 계속 크레인을 내려가게 하고, 비어있지 않고 첫 번째 인형을 만날 경우 해당 인형을 가져온 후 그 인형이 있던 위치를 0으로 바꾼다.

추가적으로 바구니에 넣는데 넣은 후 바구니 안의 인형이 같은 종류가 2개 이상일 경우는 해당 인형들을 pop하고 count를 증가시킨다.

```python
for height in range(len(board[0])):
            if board[height][width] == 0:
                continue
            else :
                basket.append(board[height][width])
                if len(basket) >= 2:
                    if basket[-1] == basket[-2]:
                        basket.pop()
                        basket.pop()
                        answer += 2

                board[height][width] = 0
                break
```

***

# 완주하지 못한 선수

마라톤 선수들 중 완주한 선수의 명단을 토대로 참가 선수 중 완주하지 못한 선수를 반환한다.

## Code

```python
def solution(participant, completion):
    participant.sort()
    completion.sort()

    for player, completer in zip(participant, completion):
        if player != completer:
            return player
    return participant[-1]
```

### Details

이러한 탐색 문제는 항상 정렬된 상태가 시간 복잡도를 줄여주기 때문에 우선 정렬을 시행한다.

```python
participant.sort()
completion.sort()
```

이 후, 정렬된 리스트를 바탕으로 반복문을 순회한다.

시간을 효율적으로 사용하기 위해 채택한 방법은 zip을 사용하여 두 개의 list를 같이 순회한다.

이 때 순회 도중 참가자와 완주자의 명단이 다르면 해당 선수는 완주하지 못한 선수로 간주하고 return한다.

zip을 통해 순회를 진행하면 두 개의 list들 중 길이가 더 짧은 list를 기준으로 순회가 끝나므로 반복문이 끝나면 마지막 참가자를 반환한다.

```python
for player, completer in zip(participant, completion):
    if player != completer:
    return player
return participant[-1]
```

***

# K번째수

인자로 주어지는 명령을 통해 배열을 자르고 자른 배열의 특정한 수를 반환한다.

![Knumber](/../assets/img/commands.PNG)

## Code

```python
def solution(array, commands):
    answer = []
    for idx in range(len(commands)):
        sli_1 = commands[idx][0]-1
        sli_2 = commands[idx][1]
        result_idx = commands[idx][2]
        new_arr = array[sli_1:sli_2]
        new_arr.sort()
        answer.append(new_arr[result_idx-1])
   
    return answer
```

### Details

명령어로 주어지는 첫 번째 값과 두 번째 값을 토대로 슬라이싱을 할 인덱스와 몇 번째에 있는 인덱스를 구한다.

```python
sli_1 = commands[idx][0]-1
sli_2 = commands[idx][1]
result_idx = commands[idx][2]
```

해당 값들을 이용해 배열을 슬라이싱하고 슬라이싱 한 배열을 정렬한 후 결과값을 append한다.

```python
new_arr = array[sli_1:sli_2]
new_arr.sort()
answer.append(new_arr[result_idx-1])
```
