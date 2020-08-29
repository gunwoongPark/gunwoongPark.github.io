---
layout: single
title: "Local Search"
categories: 
   - AI
author_profile: true
read_time: true
comments: true
---

[영리한 친구](https://sina-kim.github.io)의 제의를 통해 갑자기 블로그를 만들어지고 싶어졌다.

그래서 만든 블로그이다.

목표는 매일 매일 하루에 하나씩 커밋을 하는 것이다.

나는 프로그래머가 되고싶은 사람이다.

솔직히 아직까지는 여러가지로 자신이 없다.

내가 개발자가 되고싶은지 아무런 기업이나 어떻게든 들어가서 전산업을 하고싶은지도 모르겠고..

하지만 이런 커밋 하나 하나가 쌓여가 내가 성장하는 양분이 될 것같은 기분이 들기에 이렇게 쓸데없는 내용이더라도 커밋을 한다.

내 진로를 정하는 양분 또한 될 것이다.

기억보다는 기록을 한자씩 적어가며 내가 성장해 나가리라 믿는다.

***

# Local Search

Local search는 Tree search같은 탐색 기법과 달리 좋은 해를 찾아가는데 경로를 저장하지 않고 오로지 좋은 해만을 찾아가는 탐색 기법이다.

대표적인 기법으로는 Hill Climbing 기법이 존재한다.

Hill Climbing 기법은 계속해서 주변의 좋은 이웃해를 찾아가며 최종적으로 local maximum이나 global maximum으로 찾아가는게 목적인 알고리즘이다.

해를 찾아가다 보면 여러가지 좋지 못한 상황에 마주하게될 때도 있다.

예를 들자면 계속해서 찾아가다보니 주변이 모두 같은 가중치의 해만 존재해 고원에 떨어지는 경우라던지

이러한 문제점을 해결하기 위해 그리고 더 성능이 좋은 탐색기법을 위해 여러가지 알고리즘이 파생되었다.

- **Hill Climbing Search**

  모든 이웃해 순열 조합을 차례로 나열하여 가장 좋은 해로 이동한다. 
  
  해를 구하는게 점점 언덕을 오르는 꼴이라서 이름이 hill climbing 탐색 기법이다.

- **First Chocie Hill Climbing Search**

  이웃해가 무작위로 생성되고 만약 만난 이웃해가 현재해보다 좋을경우 바로 이동한다.
  
  NQueens 문제를 해결할 때 압도적으로 좋은 성능을 보인다.

- **Stochastic Hill Climbing Search**

  생성된 이웃 해중에서 가장 좋은 해로 갈 확률이 높아지는 알고리즘이다.
  
  확률을 활용한 알고리즘이기 문제를 푸는 중요한 척도 중 하나인 소요시간이 상당히 무작위다.
  
- **Random Restart Hill Climbing Search**

  무작위한 시작위치에 초기화되어 해를 찾아간다. 여러번 반복하다 보면 global maxima로 갈 확률이 높아진다.
  
  NQueens Problem를 해결할 때, 300만 퀸의 갯수를 1분 안에 푼 논문이 있다.
  
- **Simulated Annealing**

  이웃해가 무작위로 생성되고 현재해와 같거나 좋으면 이동하고 좋지 않을 경우 확률적으로 이동한다. 
  
  확률은 특정한 수식에 의하여 결정된다.
  
  확률을 정하는 수식에는 온도라고 표현되는 변수가 있는데 해당 변수를 낮추기 때문에 이름처럼 담금질이라고도 표현된다.
  
  특정한 수식 : e^-(deltaE/T)
  
  온도(T)가 높을수록 좋지 못한 이웃해로 이동할 확률이 높아진다.
  
  초반같은 경우 여러번 이동해 최대한 global maximum 쪽으로 이동하는게 궁극적인 목표이다.
  
  온도가 낮을수록 좋지 못한 이웃해로 이동할 확률이 줄어들어 안정적으로 해를 찾아 이동할 수 있다.
  
  delta E를 통해 안좋은 이웃해로 이동하더라도 그나마 좋은 이웃해로 이동할 확률이 높아진다.
  
***
  
NQueens Problem을 해결한다는 주제를 잡았다.

Local search 기법으로 최종 목표는 최고의 해(optimal solution)을 찾아가는 알고리즘이다.

## 코드

# main

```python
import argparse
import util
import sys
from msvcrt import getch
import time
import itertools
import random
import copy
from math import exp
import numpy as np
from collections import namedtuple
import tkinter as tk

g_exe_time=0
g_queen_count = 0

def ExhaustiveSearch(queen_count):
    print('>>> Exhaustive Search')
    global g_exe_time
    inital_clock = time.time()
    solution_init = []

    for idx in range(queen_count):
        solution_init.append(idx + 1)

    best_obj_value = sys.maxsize

    solution_init = itertools.permutations(solution_init)

    while True:
        solution = solution_init.__next__()

        obj_value = util.ViolateCount(solution)

        if obj_value == 0:
            if util.Validate(solution):
                PrintResult(solution, queen_count, inital_clock)
                return solution

            else:
                print('성공으로 나왔으나 결과 이상!!!')
                return True
            break

        elif obj_value < best_obj_value:
            best_obj_value = obj_value
            print(best_obj_value)

        if util.GetExeClockTime(inital_clock) > g_exe_time:
            print('해 도출 실패!!!')
            return False

def PrintResult(solution, queen_count, initial_clock):
    print('queen 개수 : {}'.format(queen_count))

    for idx in range(queen_count):
        print(solution[idx], end=' ')
    print()

    print('수행 시간 : {}'.format(util.GetExeClockTime(initial_clock)))

# 이중 반복문을 돈 후
# 가장 좋은 곳으로 이동 -> 평범한 힐 클라이밍
def HillClimbingSearch(queen_count):
    print('>>> Hill-Climbing Search')
    initial_clock = time.time()
    global g_exe_time

    solution = []
    cur_obj_value = GenInitSolutionRandom(solution, queen_count)

    if cur_obj_value == 0:
        return solution

    iterate_count = 0

    while True:
        iterate_count +=1
        best_next_solutions = []
        best_next_obj_value = cur_obj_value
        for idx in range(queen_count-1):
            for jdx in range(idx+1, queen_count):
                solution[idx], solution[jdx] = solution[jdx], solution[idx]
                next_obj_value = util.ViolateCount(solution)
                if next_obj_value == 0:
                    if util.Validate(solution):
                        print('성공!!!')
                        print('반복 횟수 : {}'.format(iterate_count))
                        PrintResult(solution, queen_count, initial_clock)
                        return solution
                    else:
                        print('성공으로 나왔으나 결과 이상!!!')
                        return True

                elif next_obj_value<best_next_obj_value:
                    best_next_solutions.clear()
                    copy_solution = copy.deepcopy(solution)
                    best_next_solutions.append(copy_solution)

                    best_next_obj_value = next_obj_value
                    print('[{}] {}'.format(iterate_count, best_next_obj_value))

                #동일 가중치 포함
                elif next_obj_value == best_next_obj_value:
                    copy_solution = copy.deepcopy(solution)
                    best_next_solutions.append(copy_solution)

                solution[idx], solution[jdx] = solution[jdx], solution[idx]

        if len(best_next_solutions) > 0:
            solution = random.choice(best_next_solutions)
            cur_obj_value=best_next_obj_value
        else:
            print('실패 : 현재해와 같거나 더 좋은 해가 존재하지 않음!')
            return True

        if util.GetExeClockTime(initial_clock)>=g_exe_time:
            return False

def GenInitSolutionRandom(solution, queen_count):
    for idx in range(queen_count):
        solution.append(idx+1)
    random.shuffle(solution)
    cur_obj_value = util.ViolateCount(solution)
    print('초기값 : {}'.format(cur_obj_value))

    return cur_obj_value

# 임의로 선택한 이웃쌍을 바꿈
# 바꾼 값이 좋다 -> 일단 이동
# first_choice
def SimpleHillClimbingSearch(queen_count):
    print('>>>Simple Hill-Climbing Search')
    initial_clock = time.time()

    solution = []
    cur_obj_value = GenInitSolutionRandom(solution, queen_count)

    if cur_obj_value == 0:
        return solution

    iterate_count = 0

    while True:
        iterate_count +=1
        idx = random.randrange(0, queen_count)
        jdx = random.randrange(0, queen_count)

        solution[idx], solution[jdx] = solution[jdx], solution[idx]

        next_obj_value = util.ViolateCount(solution)

        if next_obj_value == 0:
            if util.Validate(solution):
                print('성공!!!')
                print('반복 횟수 : {}'.format(iterate_count))
                PrintResult(solution, queen_count, initial_clock)
                return solution

            else:
                print('성공으로 나왔으나 결과 이상!!!')
                return True
            
        elif next_obj_value <= cur_obj_value:
            #동일 가중치 미 포함
            if next_obj_value<cur_obj_value:
                cur_obj_value = next_obj_value
                print('[{}] {}'.format(iterate_count,cur_obj_value))

        else:
            solution[idx], solution[jdx] = solution[jdx], solution[idx]

        if (util.GetExeClockTime(initial_clock) >= g_exe_time):
            return False

def SimulatedAnnealing(queen_count):
    print('Simulated Annealing')
    initial_clock = time.time()

    solution = []
    cur_obj_value = GenInitSolutionRandom(solution, queen_count)

    if cur_obj_value == 0:
        return solution

    iterate_count = 0

    T = 1.0
    alpha = 0.999

    while True:
        iterate_count +=1

        idx = random.randrange(0, queen_count)
        jdx = random.randrange(0,queen_count)

        solution[idx], solution[jdx] = solution[jdx], solution[idx]

        next_obj_value = util.ViolateCount(solution)

        if next_obj_value == 0:
            if util.Validate(solution):
                print('성공!!!')
                print('반복 횟수 : {}'.format(iterate_count))
                PrintResult(solution,queen_count,initial_clock)
                return solution

            else:
                print('성공으로 나왔으나 결과 이상!!!')
                return True

        elif next_obj_value<=cur_obj_value:
            #동일 가중치 미 포함
            if next_obj_value<cur_obj_value:
                cur_obj_value = next_obj_value
                print('[{}] {}'.format(iterate_count, cur_obj_value))

        else:
            delta_E = next_obj_value-cur_obj_value
            move_probability = exp(-(delta_E/T))
            if random.random() <= move_probability:
                cur_obj_value = next_obj_value
                print('[{}] {}, {}'.format(iterate_count, cur_obj_value, move_probability))
            else:
                solution[idx], solution[jdx] = solution[jdx], solution[idx]

        T = T*alpha
        if T<0.01 :
            T= 0.01

        if (util.GetExeClockTime(initial_clock) >= g_exe_time):
            return False

# 무작위로 생성된 초기 상태에서 일련의 힐 클라이밍 수행
# 교수님이 짜주신 코드중 SimpleHillClimbingSearch기법->first choice 채택
def RandomRestartHillClimbingSearch(queen_count):
    print('>>> Random Restart Hill-Climbing Search')

    Result = namedtuple('Result', ['solution', 'count', 'time', 'score', 'flag'])  # flag = 0 정상 1 성공이나 이상 2 실패
    result_solutions = []

    for idx in range(5):
        initial_clock = time.time()
        iterate_count = 0
        solution = []
        cur_obj_value = GenInitSolutionRandom(solution, queen_count)
        for jdx in range(len(solution)):
            print(solution[jdx], end = ' ')
        print()

        if cur_obj_value == 0:
            end_time = util.GetExeClockTime(initial_clock)
            result_solutions.append(Result(solution, iterate_count, end_time, cur_obj_value, 0))
            print('queen 개수 : {}'.format(queen_count))
            print('반복 횟수 : {}, 수행 시간 : {}, 중복 퀸 수 : {}'.format(iterate_count, end_time, cur_obj_value))
            for jdx in range(len(solution)):
                print(solution[jdx], end=' ')
            print()
            print()
            continue

        while True:
            iterate_count += 1
            idx = random.randrange(0, queen_count)
            jdx = random.randrange(0, queen_count)

            solution[idx], solution[jdx] = solution[jdx], solution[idx]

            next_obj_value = util.ViolateCount(solution)

            if next_obj_value == 0:
                if util.Validate(solution):
                    end_time = util.GetExeClockTime(initial_clock)
                    result_solutions.append(Result(solution, iterate_count, end_time, next_obj_value, 0))
                    print('queen 개수 : {}'.format(queen_count))
                    print(
                        '반복 횟수 : {}, 수행 시간 : {}, 중복 퀸 수 : {}'.format(iterate_count, end_time, next_obj_value))
                    for jdx in range(len(solution)):
                        print(solution[jdx], end=' ')
                    print()
                    print()
                    break
                else:
                    end_time = util.GetExeClockTime(initial_clock)
                    result_solutions.append(Result(solution, iterate_count, end_time, next_obj_value, 1))
                    print('queen 개수 : {}'.format(queen_count))
                    print(
                        '반복 횟수 : {}, 수행 시간 : {}, 중복 퀸 수 : {}'.format(iterate_count, end_time, next_obj_value))
                    for jdx in range(len(solution)):
                        print(solution[jdx], end=' ')
                    print()
                    print()

                    break

            elif next_obj_value <= cur_obj_value:
                #동일 가중치 미 포함
                if next_obj_value < cur_obj_value:
                    cur_obj_value = next_obj_value

            else:
                solution[idx], solution[jdx] = solution[jdx], solution[idx]

            if (util.GetExeClockTime(initial_clock) >= g_exe_time/5):
                end_time = util.GetExeClockTime(initial_clock)
                result_solutions.append(Result(solution, iterate_count, end_time, next_obj_value, 2))
                print('queen 개수 : {}'.format(queen_count))
                print('반복 횟수 : {}, 수행 시간 : {}, 중복 퀸 수 : {}'.format(iterate_count, end_time, next_obj_value))
                print()
                for jdx in range(len(solution)):
                    print(solution[jdx], end=' ')
                print()
                print()
                break

    result_solutions.sort(key = lambda x: x[2])
    for idx in range(5):
        if result_solutions[idx][3] == 0:
            print('>>> Optimal Solution')
            print('queen 개수 : {}'.format(queen_count))
            print('반복 횟수 : {}, 수행 시간 : {}, 중복 퀸 수 : {}'.format(result_solutions[idx][1], result_solutions[idx][2], result_solutions[idx][3]))
            for jdx in range(len(result_solutions[idx][0])):
                print(result_solutions[idx][0][jdx], end=' ')
            print()
            print()
            return result_solutions[idx][0]
            break

def StochasticHillClimbingSearch(queen_count):
    print('>>> Stochastic-Hill-Climbing Search')
    initial_clock = time.time()
    global g_exe_time
    worst = util.WorstViolateCount(queen_count)

    Candidate = namedtuple('Candidate', ['index', 'value'])

    solution = []
    cur_obj_value = GenInitSolutionRandom(solution, queen_count)

    if cur_obj_value == 0:
        return solution

    iterate_count = 0

    while True:
        iterate_count +=1
        best_next_solutions = []

        for idx in range(queen_count-1):
            for jdx in range(idx+1, queen_count):
                solution[idx], solution[jdx] = solution[jdx], solution[idx]

                next_obj_value = util.ViolateCount(solution)

                if next_obj_value == 0:
                    if util.Validate(solution):
                        print('성공!!!')
                        print('반복 횟수 : {}'.format(iterate_count))
                        PrintResult(solution, queen_count, initial_clock)

                        return solution
                    else:
                        print('성공으로 나왔으나 결과 이상!!!')
                        return True

                elif next_obj_value <= cur_obj_value:
                    #동일 가중치 미 포함
                    if next_obj_value < cur_obj_value:
                        best_next_solutions.append(Candidate((idx, jdx), next_obj_value))

                solution[idx], solution[jdx] = solution[jdx], solution[idx]

        if len(best_next_solutions) > 0:
            probability = []

            for idx in range(len(best_next_solutions)):
                probability.append(worst - best_next_solutions[idx][1])

            select_solution = random.choices(best_next_solutions, weights=probability)[0]

            solution[select_solution[0][0]], solution[select_solution[0][1]] = \
                solution[select_solution[0][1]], solution[select_solution[0][0]]
            print('[{}] {}, {}'.format(iterate_count, cur_obj_value,select_solution[1]))
            cur_obj_value = select_solution[1]
        else:
            print('실패 : 현재해와 같거나 더 좋은 해가 존재하지 않음!')
            return True

        if util.GetExeClockTime(initial_clock)>=g_exe_time:
            return False

def ShowBoard (solution):
    window = tk.Tk()
    window.geometry("800x800+0+0")

    canvas = tk.Canvas(window, width=800, height= 800)
    side = 790//len(solution)

    color = lambda x: 'lightgray' if x%2 == 0 else 'white'
    for idx in range(len(solution)):
        for jdx in range(len(solution)):
            canvas.create_rectangle(idx * side, jdx * side, (idx + 1) * side, (jdx + 1) * side, fill=color(idx+jdx))

    for idx in range(len(solution)):
        canvas.create_oval(idx*side, (solution[idx]-1)*side, idx*side+side, (solution[idx]-1)*side+side, fill = 'red')

    canvas.pack()
    window.mainloop()

if __name__=='__main__':
    ap = argparse.ArgumentParser()
    ap.add_argument('-q', '--queens', type=int, required=True, \
                    help='Number of Queens')
    ap.add_argument('-m', '--method', type=int, required=True, \
                    help='Type of method')
    ap.add_argument('-t', '--time', type=int, required=True, \
                    help='Time limit')

    args = vars(ap.parse_args())

    queen_count = args['queens']
    method_number = args['method']
    g_exe_time = args['time']

    search_method = {1:'EXHAUSTIVE',
                     2:'HILL_CLIMBING',
                     3:'SIMPLE_HILL_CLIMBING',
                     4:'SIMULATED_ANNEALING',
                     5:'RANDOM_RESTART_HILL_CLIMBING',
                     6:'STOCHASTIC_HILL_CLIMBING'}

    solution = []

    if search_method[method_number] == 'EXHAUSTIVE':
        solution = ExhaustiveSearch(queen_count)
    elif search_method[method_number] == 'HILL_CLIMBING':
        solution = HillClimbingSearch(queen_count)
    elif search_method[method_number] == 'SIMPLE_HILL_CLIMBING':
        solution = SimpleHillClimbingSearch(queen_count)
    elif search_method[method_number] == 'SIMULATED_ANNEALING':
        solution = SimulatedAnnealing(queen_count)
    elif search_method[method_number] == 'RANDOM_RESTART_HILL_CLIMBING':
        solution = RandomRestartHillClimbingSearch(queen_count)
    elif search_method[method_number] == 'STOCHASTIC_HILL_CLIMBING':
        solution = StochasticHillClimbingSearch(queen_count)

    if isinstance(solution, list) or isinstance(solution, tuple):
        ShowBoard(solution)

    print('종료 : 아무 키나 누르세요.')
    getch()
    sys.exit()
```

핵심적인 흐름과 알고리즘이 존재하는 코드이다.

사용자에게 어떤 기법을 사용할지 입력받고 해당 기법을 이용해 Hill Climbing을 진행한다.

# util

```python
import time
def ViolateCount(solution):
    count = 0

    for idx in range(len(solution)-1):
        row1 = solution[idx]
        for jdx in range(idx+1, len(solution)):
            row2 = solution[jdx]

            if row1 == (row2+(jdx-idx)) or row1 == (row2 - (jdx-idx)):
                count += 1

    return count

def Validate(solution):
    for idx in range(len(solution)-1):
        row1 = solution[idx]
        for jdx in range(idx+1, len(solution)):
            row2 = solution[jdx]

            if row1 == (row2+(jdx-idx)):
                return False

            if row1 == (row2-(jdx-idx)):
                return False

    return True

def GetExeClockTime(initial_clock):
    current = time.time()
    return current-initial_clock

def WorstViolateCount(queen_count):
    sum = 0
    for idx in range(queen_count-1):
        sum += queen_count-(idx+1)
    return sum
```

main 코드를 진행하기 위해 여러 기능을 내포하는 코드이다.
