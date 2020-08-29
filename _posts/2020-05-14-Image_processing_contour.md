---
layout: single
title: "영상처리 연결요소"
categories: 
   - Image Processing
author_profile: true
read_time: true
comments: true
---

# 연결요소

영상처리 기법 중 연결요소를 추출하는 예제를 두 가지 실습했다.

첫 번째는 여러 개의 동전이 있는 이미지에서 동전만 연결요소로 추출해내는 실습과

두 번째는 사람들이 지나다니는 영상에서 배경을 제거하고 움직이는 객체만 연결요소로 추출해내는 실습이다.

## 동전 검출

```python
from __future__ import print_function
import argparse
import cv2
import numpy as np
from matplotlib import pyplot as plt

def findContours(bin):
    kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (3,3))

    img = cv2.morphologyEx(bin, cv2.MORPH_OPEN, kernel)
    img = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel)

    (contours, hierarchy) = cv2.findContours(img, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    return img, contours

if __name__=='__main__':
    ap = argparse.ArgumentParser()
    ap.add_argument('-i', '--image', required=True, \
                    help = 'Path to the input image')
    args = vars(ap.parse_args())

    filename = args['image']

    image = cv2.imread(filename)

    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    gray = cv2.GaussianBlur(gray, (3,3), 0)

    bin = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY_INV, 21, 2)

    contour_img, contours = findContours(bin)

    new_img = np.zeros_like(contour_img, dtype="uint8")
    for idx in range (len(contours)):
        cntr = sorted(contours, key=cv2.contourArea, reverse=True)[idx]
        cv2.drawContours(new_img, [cntr], 0, (255,255,255), -1)

    plt.subplot(1,3,1), plt.imshow(gray, cmap='gray')
    plt.title('grayscale and blur'), plt.xticks([]), plt.yticks([])

    plt.subplot(1,3,2), plt.imshow(bin, cmap='gray')
    plt.title('threshold'), plt.xticks([]), plt.yticks([])

    plt.subplot(1,3,3), plt.imshow(new_img, cmap='gray')
    plt.title('contour'), plt.xticks([]), plt.yticks([])

    plt.show()
```

우선 코드의 메인부분부터 살펴 보겠다.

우선 이미지를 읽어와서 영상을 처리하기 위해 하는 전처리 기법인 그레이스케일로 변환(cv2.cvtColor)과

가우시안 블러(cv2.GaussianBlur)를 활용해 스무딩 처리를 하고, 쓰레시홀딩처리(cv2.adaptiveThreshold)를 하여 영상을 이진영상으로 변환한다.

이후 전처리된 영상을 연결요소를 추출하는 함수에 인자로 넘긴다.

함수부분을 살펴 보겠다.

우선 추출된 이진영상에 노이즈가 많아 모폴로지 기법을 통해 노이즈를 제거해주어 동전부분만 남게 한다.

모폴로지 기법을 하기 위한 커널을 크기가 3인 원 모양으로 만들고(cv2.getStructuringElement) 모폴로지 기법(cv2.morphologyEx)을 수행한다.

'모폴로지 열림' 기법은 주로 노이즈를 제거할 때 사용한다.

'모폴로지 닫힘' 기법은 주로 영상의 구멍이 있는 곳을 메울 때 사용한다.

이 후 연결요소를 추출하는 함수인 cv2.findContours를 사용하여 여러개의 연결요소(contours)와 그 연결요소의 관계(hierarchy)를 반환한다.

반환된 연결요소와 모폴로지 연산을 통해 추출된 이미지를 함수에서 메인으로 반환한다.

이 후 메인으로 돌아와 원본 이미지와 크기가 같지만 모두 0인 즉 검정 바탕의 이미지를 생성한다.(np.zeros_like)

반복문을 통해 크기가 큰 순서대로 추출된 연결요소를 추출해 방금 생성한 검정 바탕의 이미지에 흰 색으로 그린다.(cv2.drawContours)

> 큰 순서대로 추출이 되는 이유는 contours라는 연결요소가 포함된 리스트를 크기를 기준으로(key=cv2.contourArea) 내림차순 정렬(reverse=True)한다. 이 후 리스트의 인덱스를 통해 하나씩 접근하며 큰 순서대로 추출할 수 있다.

이 후 matplotlib를 활용하여 그래프식으로 이미지를 이쁘게 정렬하여 출력한다.

실행 결과는 이렇다.

![coin_extract_picture](/../assets/img/coin_extract.png)

## 움직이는 객체 검출

```python
from __future__ import print_function
import argparse
import numpy as np
import cv2

def movingObjectsDetect(fgmask):
    kernel = cv2.getStructuringElement(cv2.MORPH_CROSS, (5, 5))

    img = cv2.morphologyEx(fgmask, cv2.MORPH_OPEN, kernel)
    img = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel)

    (contours, hierarchy) = cv2.findContours(img, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    return img, contours

if __name__=='__main__':
    ap = argparse.ArgumentParser()
    ap.add_argument('-v', '--video', required=False,\
                    help ='Path to the video file')
    args = vars(ap.parse_args())

    fvideo = args.get("video")

    if fvideo is None:
        camera = cv2.VideoCapture(0)
    else:
        camera = cv2.VideoCapture(args['video'])

    fgbg = cv2.createBackgroundSubtractorMOG2()

    while True:
        (retval, frame) = camera.read()

        if fvideo is not None and not retval:
            break

        fgmask = fgbg.apply(frame)

        contour_img, contours = movingObjectsDetect(fgmask)

        new_img = np.zeros_like(contour_img, dtype="uint8")
        for idx in range(len(contours)):
            cntr = sorted(contours, key=cv2.contourArea, reverse=True)[idx]
            cv2.drawContours(new_img, [cntr], 0, (255,255,255), -1)

        new_img = cv2.cvtColor(new_img, cv2.COLOR_GRAY2RGB)

        result = np.hstack((frame,new_img))

        cv2.imshow('frame', result)

        if cv2.waitKey(1) & 0xFF == ord("q"):
            break

    camera.release()
```

우선 메인 코드부터 살펴보겠다.

사용자가 인자로 넘겨준 경로를 통해 비디오를 설정한다.(cv2.VideoCapture)

만약 비디오를 특별히 지정하지 않으면 자신의 컴퓨터 캠으로 비디오를 지정한다.

이 후 움직이는 객체를 배경으로부터 추출하여(cv2.createBackgroundSubtractorMOG2) 움직이는 객체는 흰 색 배경은 검은색으로 지정한다.

반복문을 통해 비디오로 송출되는 한 프레임마다 빠르게 영상처리를 한다.

> 동영상은 여러장의 연속적인 사진을 빠르게 바꾸며 보여주는 것을 우리는 동영상처럼 느낀다. 이것은 프레임이며 사실은 각 사진 한장 한장 씩 보여주지만 인간의 눈으로는 그것을 느낄 수 없어 실제로 동영상으로 보이는 것처럼 착각한다. 따라서 이번 실습에서는 각 프레임마다 움직이는 객체를 추출하는 알고리즘을 활용 할 것이다. 

배경이 제거된 영상은 움직이는 객체를 연결요소로 추출하는 함수에 인자로 넘겨준다.

인자로 받은 영상을 첫 번째 실습에서 똑같은 과정을 통해 연결요소를 추출하고 추출한 연결요소와 모폴로징이 된 이미지를 메인으로 반환한다.

> 두 번째 실습에서 모폴로징을 할 때 활용하는 커널모양은 십자가 모양을 활용한다. 십자가 모양 외에도 첫 번째 실습에서 활용된 타원모양, 그 외에도 사각형모양등 다양한 모양의 커널을 생성할 수 있다.

다시 메인으로 돌아와 첫 번째 실습과 똑같은 과정을 통해 움직이는 객체를 흰 색으로 움직이지 않는 객체는 검정색으로 새로운 이미지를 그린다.

해당 영상의 쉬운 비교를 위해 나는 영상을 수평으로 붙일것이다.(hstack)

하지만 원본인 컬러영상은 3차원이고 추출된 영상은 이진영상으로 2차원이기 때문에 붙이는 것이 제한되어 이진영상을 컬러 영상으로 변환한다.(cv2.cvtColor)

이 후 수평으로 붙인 영상을 송출한다.

![moving_object_extract_picture](/../assets/img/moving_object_extract.PNG)

