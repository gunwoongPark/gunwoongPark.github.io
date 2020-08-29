---
layout: single
title: "영상처리 Perspective"
categories: 
   - Image Processing
author_profile: true
read_time: true
comments: true
---

# Perspective

스마트폰으로 찍은 영수증을 제대로 된 형태로 정렬시켜주는 프로그램을 만들어 봤다.

```python
from __future__ import print_function
import cv2
import numpy as np
import argparse

if __name__ == "__main__" :
    # 명령행 인자 처리
    ap = argparse.ArgumentParser()
    ap.add_argument('-i', '--image', required = True, \
            help = 'Path to the input image')
    args = vars(ap.parse_args())

    filename = args['image']

    image = cv2.imread(filename)
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    canny = cv2.Canny(gray, 0, 255)

    (contours, _) = cv2.findContours(canny, cv2.RETR_LIST, cv2.CHAIN_APPROX_SIMPLE)

    for idx in range(5):
        cntr = sorted(contours, key=cv2.contourArea, reverse=True)[idx]
        epsilon = 0.01 * cv2.arcLength(cntr, True)
        approx = cv2.approxPolyDP(cntr, epsilon, True)
        if len(approx) == 4:
            canny = cv2.drawContours(canny, [approx], 0, (255,255,255), -1)
            break

    cv2.imshow('canny',canny)
    cv2.waitKey(0)

    pts1 = np.float32([approx[3][0], approx[0][0], approx[2][0], approx[1][0]])
    pts2 = np.float32([[0,0], [480,0], [0,720], [480,720]])

    matrix = cv2.getPerspectiveTransform(pts1, pts2)
    dst = cv2.warpPerspective(gray, matrix, (480, 720))

    dst = cv2.adaptiveThreshold(dst, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY,21, 5)

    cv2.imshow('dst', dst)
    cv2.waitKey(0)
```

전에 해본 실습과 마찬가지로 전처리 과정을 거친 후 연결요소를 추출한다.

추출한 연결요소를 근사화(cv2.approxPolyDP)하고 이를 통해 사각형으 네 점을 찾는다.

이후 이 네 점을 Perspective 변환을 통해 특정한 해상도를 갖는 사진으로 변환시켜 우리가 원하는 정보를 얻는다.

밑의 사진은 처리가 되기 전 영수증 이미지이다.

![original_receipt_picture](/../assets/img/receipt.jpg)

밑은 처리가 된 이미지이다.

![dst_receipt_picture](/../assets/img/dst_receipt.PNG)
