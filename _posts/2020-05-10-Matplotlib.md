---
layout: single
title: "Matplotlib"
categories: 
   - Python
author_profile: true
read_time: true
comments: true
---

# Matplotlib 파이썬으로 그래프 그리기

matplotlib는 파이썬에서 그래프를 그릴 수 있도록 도와주는 모듈중 하나이다.

자주 쓰이는 곳은 대학교 과제로 낼 때?, 어떤 입력값에 의한 출력값을 도식화하여 표현하고 싶거나 확인하고싶을때 주로 사용된다.

물론 본인은 전자의 상황에 주로 사용한다.

Python은 상당히 편리한 언어이다.

단언코 내가 배워본 모든 언어(C, C++, JAVA)를 통틀어 가장 편리하다고 말할 수 있다.

가장 큰 특징이라 말하자면 객체지향을 기반으로 이루어진 언어이다.

> 객체지향이란 프로그래밍에서 상속, 다형성, 데이터 추상화, 캡슐화, 정보은닉등의 특징을 가지고 있다.
대부분의 프로그래밍 언어가 이와같은 특징을 활용하기에 객체지향을 채택하고있다.
저 특징들에 대해서는 지금 자세하게 다루기엔 본 주제와 어긋나기에 간단히 이 정도만 다루고, 나중에 따로 정리하겠다.

각설하고, matplotlib에 대해서 알아보겠다.

우선 모듈을 import해야한다.

```python
import matplotlib.pyplot as plt
```

이제 matplotlib에 있는 내장 함수들을 활용할 수 있다.

그래프는 모두가 알듯 x축과 y축으로 이루어져 있다.

오늘은 간단하게 x축과 y축에 특정한 범위를 넣어주어 그래프를 그리는 것과

그래프의 x축과 y축에 설명을 적고 그래프의 제목을 붙이는 것까지만 해보겠다.

우선 x축과 y축을 지정하는데 x축과 y축에 이어지는 꼴을 대입한다

예를 들자면 리스트나, 튜플, 아니면 클래스를 넣어도 괜찮다

```python
x = range(1,10)
x = [1,2,3,4,5]
x = (1,2,3,4,5)
```

y축 또한 마찬가지로 저런 정보를 넣어준다.

오늘은 예시를 드느라 저렇게 지정하는 값을 넣어주지만 점점 입력값이 증가하는 프로그램을 만들었다면

입력값을 x로 두고 그에 출력된 값을 y로 둘 수 있다.

오늘 한 예시로는 파일로 저장된 정보를 불러와 x와 y에 대입하였다

```python
  f = open(fname, 'r')

  contents = []
  while True:
      line = f.readline()
      if not line: break
      element = list(map(int, map(float, line.split(':'))))
      contents.append(element)

  f.close()

  x = []
  y = []

  for idx in range(len(contents)):
      x.append(contents[idx][0])
      y.append(contents[idx][2])
```

내가 읽어온 파일은 '정수:실수:정수' 꼴로 한 줄씩 저장되어있다.

아무튼 저런식으로도 사용이 가능하다.

다음으로 저렇게 넣은 정보를 기반으로 그래프를 그려보겠다.

```python
plt.plot(x, y)
plt.title('Exhaustive Search')
plt.xlabel('Queen')
plt.ylabel('time(sec)')
plt.show()
```

plt는 내가 위에서 모듈을 import할 때 저런식으로 사용하고자 해서 만든 이름이다.

코드를 보면 첫 번째 줄은 x와 y를 plot이라는 내장함수에 인자로 주어 그래프를 생성한다.

두 번째 줄은 title이라는 내장함수에 문자열을 인자로 주어 그래프의 제목을 출력한다.

세 번째 줄은 xlabel이라는 내장함수에 문자열을 인자로 주어 그래프의 x축에 대한 정보를 출력한다.

네 번째 줄은 ylabel이라는 내장함수에 문자열을 인자로 주어 그래프의 y축에 대한 정보를 출력한다.

마지막 줄은 내가 만든 plt라는 그래프를 출력시킨다.

해당 코드를 진행하면 다음과 같이 그래프가 나온다.

![exhaustive_search_picture](/../assets/img/exhaustive_time.png)

넣어준 인자에 맞게 이쁘게 그래프를 그려주는 것을 볼 수 있다.
