---
layout: single
title: "영상처리 바코드 검출"
categories: 
   - Image Processing
author_profile: true
read_time: true
comments: true
---

# 바코드 검출

## 문제 분석

바코드가 있는 영상으로부터 바코드 영역만을 분리하여 검출한다.

분리한 바코드 영역은 결과 영상에 사각형으로 위치를 표시하고 검출 위치는 따로 파일로 저장한다.

***

## 설계 및 구현

영상을 처리하기 전에 우선 필요한 정보는 검은색과 흰 색으로 이루어진 바코드 객체이다.

따라서 칼라 영상에서 필요가 없는 정보를 줄이기 위해 그레이스케일로 영상을 변환한다.

그레이스케일로 처리한 영상을 수평과 수직방향에 따라서 각각 경계 검출을 시행한다.

> 바코드가 무조건 수평방향으로 있으리란 보장은 없다.

영상처리를 위하여 OpenCV를 활용하고, 언어는 파이썬을 사용하였다.

```python
sobelx = cv2.Sobel(image, cv2.CV_8U, 1, 0, ksize=-1)
sobely = cv2.Sobel(image, cv2.CV_8U, 0, 1, ksize=-1)
```

수평으로 바코드가 존재할 경우는 경계검출을 수평으로 진행한 방향의 결과값을 수직으로 진행한 결과 값을 빼서 결과를 도출한다.

```python
hori_dst = cv2.subtract(sobelx, sobely)
```

'subtract()' 함수를 이용하면 이미지간의 산술 연산 중 뺄셈을 할 수 있다.

또한, 연산 후 음수가 되는 값은 자동으로 0으로 처리해주는 편리한 함수이다.

수평방향과 반대로 수직으로 바코드가 존재할 경우는 수직값에서 수평값을 빼서 결과를 도출한다.

```python
vert_dst = cv2.subtract(sobely, sobelx)
```

도출해낸 결과를 가우시안 블러링 처리한다.

```python
hori_dst = cv2.GaussianBlur(hori_dst, (5, 5), 0)
vert_dst = cv2.GaussianBlur(vert_dst, (5, 5), 0)
```

흰 색과 검은색으로 이루어진 바코드 영역을 더 쉽게 검출해 내기 위해서 영상을 이진화 한다.

```python
hori_th, hori_dst = cv2.threshold(hori_dst, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
vert_th, vert_dst = cv2.threshold(vert_dst, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
```

'THRESH_OTSU' 옵션을 활용하여 간편하게 적절한 임계값을 지정해 영상을 이진화 처리할 수 있다.

이진화 된 영상을 모폴로징 연산을 진행해 영상을 최적화한다.

영상의 바코드 영역의 빈틈들을 제거하기 위해 수평 방향의 바코드는 커널을 좌우가 긴 직사각형의 모양으로, 수직 방향의 바코드는 커널의 상하가 긴 직사각형 모양으로 닫힘(침식->팽창) 연산을 수행한다.

```python
hori_kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (93, 1))
vert_kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (1, 93))
```

수평모양의 바코드 영상을 닫힘 연산하기 위해 수평으로 긴 구조적 요소를 만들고, 수직모양의 바코드 영상을 닫힘 연산하기 위해 수직으로 긴 구조적 요소를 만든다.

```python
hori_dst = cv2.morphologyEx(hori_dst, cv2.MORPH_CLOSE, hori_kernel)
vert_dst = cv2.morphologyEx(vert_dst, cv2.MORPH_CLOSE, vert_kernel)
```

이 후, 닫힘 연산을 수행한다.

전처리된 영상의 노이즈를 제거하기위해 침식연산을 진행하고 이 후, 팽창 연산을 통해 바코드 영역을 확대한다.

```python
small_rect = cv2.getStructuringElement(cv2.MORPH_RECT, (11, 11))
```

침식과 팽창 연산을 위해 작은 정사각형 모양의 구조적 요소를 만들었다.

```python
hori_dst = cv2.erode(hori_dst, small_rect, iterations=12)
hori_dst = cv2.dilate(hori_dst, small_rect, iterations=12)

vert_dst = cv2.erode(vert_dst, small_rect, iterations=12)
vert_dst = cv2.dilate(vert_dst, small_rect, iterations=12)
```

이 후, 침식->팽창 연산을 12번씩 반복하여 영상을 최적화한다.

최적화된 영상에서 가장 적절한 값을 추출해낸다.

```python
(contours, hierarchy) = cv2.findContours(hori_dst, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
if len(contours) > 0:
    cntr = sorted(contours, key=cv2.contourArea, reverse=True)[0]
    hori_points = cv2.boundingRect(cntr)
else:
    hori_points = [0, 0, 0, 0]

(contours, hierarchy) = cv2.findContours(vert_dst, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
if len(contours) > 0:
    cntr = sorted(contours, key=cv2.contourArea, reverse=True)[0]
    vert_points = cv2.boundingRect(cntr)
else:
    vert_points = [0, 0, 0, 0]
```

영상의 외곽선을 활용하여 연결요소를 생성한다.

수평방향과 수직방향에 대해 각각 해당 연산을 수행한다.

연결요소들을 찾고 해당 연결요소들 중 가장 크기정보가 큰 연결요소를 찾기 위해 연결요소의 크기를 기준으로 내림차순 정렬한 후 가장 첫 번째 요소의 값을 추출한다.

> 이 경우 가장 큰 연결요소가 추출된다.

추출된 연결요소의 외곽선만을 포함하는 영상을 생성하기 위해 'boundingRect()' 함수를 활용하여 왼쪽 상단의 x, y좌표와 길이, 넓이 정보를 받는다.

만약 아무 연결요소도 검출하지 못하면 각 값을 0을 넣도록 예외처리한다.

이 후, 모든 정보를 토대로 값을 반환한다.

```python
hori_size = hori_points[2] * hori_points[3]
vert_size = vert_points[2] * vert_points[3]

if hori_size > vert_size:
    points = hori_points
else:
    points = vert_points

return (points[0], points[1], points[0] + points[2], points[1] + points[3])
```

생성된 연결요소의 정보들 중 각각의 넓이를 구하여 더 큰 값에 해당하는 영상의 정보를 반환한다.

반환받은 값을 토대로 사각형을 그린다.

```python
detectimg = cv2.rectangle(image, (points[0], points[1]), (points[2], points[3]), (0, 255, 0), 2)
```

왼쪽 위 좌표와 오른쪽 아래 좌표를 토대로 초록색 박스를 생성한다.

***

## 결과

### 수평모양 바코드
![hori_barcord](/../assets/img/barcode_07_res.jpg)

***

### 수직모양 바코드
![vert_barcord](/../assets/img/barcode_08_res.jpg)

***

## 코드

### 바코드 검출

```python
from __future__ import print_function
import argparse
import numpy as np
import cv2
import os
import glob
import time

def detectBarcode(image):
    sobelx = cv2.Sobel(image, cv2.CV_8U, 1, 0, ksize=-1)
    sobely = cv2.Sobel(image, cv2.CV_8U, 0, 1, ksize=-1)

    hori_dst = cv2.subtract(sobelx, sobely)
    vert_dst = cv2.subtract(sobely, sobelx)

    hori_dst = cv2.GaussianBlur(hori_dst, (5, 5), 0)
    vert_dst = cv2.GaussianBlur(vert_dst, (5, 5), 0)

    hori_th, hori_dst = cv2.threshold(hori_dst, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
    vert_th, vert_dst = cv2.threshold(vert_dst, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

    # 직사각형 모양으로 닫힘
    hori_kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (93, 1))
    vert_kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (1, 93))

    hori_dst = cv2.morphologyEx(hori_dst, cv2.MORPH_CLOSE, hori_kernel)
    vert_dst = cv2.morphologyEx(vert_dst, cv2.MORPH_CLOSE, vert_kernel)

    # 작은 사각형 모양으로 침식->팽창 노가다
    small_rect = cv2.getStructuringElement(cv2.MORPH_RECT, (11, 11))

    hori_dst = cv2.erode(hori_dst, small_rect, iterations=12)
    hori_dst = cv2.dilate(hori_dst, small_rect, iterations=12)

    vert_dst = cv2.erode(vert_dst, small_rect, iterations=12)
    vert_dst = cv2.dilate(vert_dst, small_rect, iterations=12)

    (contours, hierarchy) = cv2.findContours(hori_dst, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    if len(contours) > 0:
        cntr = sorted(contours, key=cv2.contourArea, reverse=True)[0]
        hori_points = cv2.boundingRect(cntr)
    else:
        hori_points = [0, 0, 0, 0]

    (contours, hierarchy) = cv2.findContours(vert_dst, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    if len(contours) > 0:
        cntr = sorted(contours, key=cv2.contourArea, reverse=True)[0]
        vert_points = cv2.boundingRect(cntr)
    else:
        vert_points = [0, 0, 0, 0]

    hori_size = hori_points[2] * hori_points[3]
    vert_size = vert_points[2] * vert_points[3]

    if hori_size > vert_size:
        points = hori_points
    else:
        points = vert_points

    return (points[0], points[1], points[0] + points[2], points[1] + points[3])


if __name__ == '__main__':
    ap = argparse.ArgumentParser()
    ap.add_argument("-d", "--dataset", required = True, help = "path to the dataset folder")
    ap.add_argument("-r", "--detectset", required = True, help = "path to the detectset folder")
    ap.add_argument("-f", "--detect", required = True, help = "path to the detect file")
    args = vars(ap.parse_args())
    
    dataset = args["dataset"]
    detectset = args["detectset"]
    detectfile = args["detect"]

    # 결과 영상 저장 폴더 존재 여부 확인
    if(not os.path.isdir(detectset)):
        os.mkdir(detectset)

    # 결과 영상 표시 여부
    verbose = False

    # 검출 결과 위치 저장을 위한 파일 생성
    f = open(detectfile, "wt", encoding="UTF-8")  # UT-8로 인코딩

    start = time.time()

    # 바코드 영상에 대한 바코드 영역 검출
    for imagePath in glob.glob(dataset + "/*.jpg"):
        print(imagePath, '처리중...')

        # 영상을 불러오고 그레이 스케일 영상으로 변환
        image = cv2.imread(imagePath)
        gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

        # 바코드 검출

        points = detectBarcode(gray)

        # 바코드 영역 표시
        detectimg = cv2.rectangle(image, (points[0], points[1]), (points[2], points[3]), (0, 255, 0), 2)  # 이미지에 사각형 그리기

        # 결과 영상 저장
        loc1 = imagePath.rfind("\\")
        loc2 = imagePath.rfind(".")
        fname = 'result/' + imagePath[loc1 + 1: loc2] + '_res.jpg'
        cv2.imwrite(fname, detectimg)

        # 검출한 결과 위치 저장
        #print(imagePath[loc1 + 1: loc2], len(imagePath[loc1 + 1: loc2]))
        f.write(imagePath[loc1 + 1: loc2])
        f.write("\t")
        f.write(str(points[0]))
        f.write("\t")
        f.write(str(points[1]))
        f.write("\t")
        f.write(str(points[2]))
        f.write("\t")
        f.write(str(points[3]))
        f.write("\n")

        if verbose:
            cv2.imshow("image", image)
            cv2.waitKey(0)
    end = time.time()
    print('소요 시간 : {}'.format(end-start))
```

### 정확도 검사

```python
import numpy as np
import argparse
import pdb

# read filename and position data from a given file
# and create a dictionary
def readRectData(fname):
	file = open(fname, 'r', encoding='UTF8')

	pos_dict = {}
	num = 0
	for line in file:
		data = line.split()
		pos_dict[data[0].replace(u"\ufeff", '')] = [int(x) for x in data[1:]]

		num = num + 1

	print('total number of list: ', num)

	file.close()

	return num, pos_dict

# calculate a ratio of intersection of two rectangle against
# reference rectangle : True Positive Rate (TPR)
# calculate a ratio of intersection of two rectangle against
# detected rectangle : Precision
# arguments: lists of format [sx, sy, ex, ey]
def calcRatioOfIntersectArea(rect1, rect2):
	# r2.sx > r1.ex or r2.ex < r1.sx : no intersection
	if (rect2[0] > rect1[2]) or (rect2[2] < rect1[0]):
		return [0.0, 0.0, 0.0, 0.0]
	# r2.sy > r1.ey or r2.ey < r1.sy : no intersection
	if (rect2[1] > rect1[3]) or (rect2[3] < rect1[1]):
		return [0.0, 0.0, 0.0, 0.0]

	sx = max([rect1[0], rect2[0]])
	ex = min([rect1[2], rect2[2]])
	sy = max([rect1[1], rect2[1]])
	ey = min([rect1[3], rect2[3]])

	area_actual = (rect1[2] - rect1[0]) * (rect1[3] - rect1[1])
	area_detected = (rect2[2] - rect2[0]) * (rect2[3] - rect2[1])
	true_positive = (ex - sx) * (ey - sy)
	false_positive = area_detected - true_positive

	precision = true_positive / area_actual
	recall = true_positive / area_detected

	area_of_union = area_actual + area_detected - true_positive

	if true_positive <= 0 :
		iou = 0
	else:
		iou = true_positive / area_of_union

	if (precision + recall) == 0 :
		f1_score = 0
	else :
		f1_score = 2*(precision*recall) / (precision + recall)

	return [precision, recall, f1_score, iou]

if __name__ == '__main__' :
	# 명령행 인자 처리
	ap = argparse.ArgumentParser()
	ap.add_argument("-r", "--reference", required=True,
		help="File on reference position of plate")
	ap.add_argument("-d", "--detect", required=True,
		help="File on detected position of plate")

	args = vars(ap.parse_args())

	# read reference data
	ref_num, ref_data = readRectData(args["reference"])
	# read detection data
	res_num, res_data = readRectData(args["detect"])

	# save accuracies to file
	file = open('accuracy.dat', 'w', encoding='UTF8')

	# find a correspoding item in reference with given detection item
	# create a list with accuracies
	accuracy = []
	idx = 0
	for key in ref_data:
		ref = ref_data.get(key)
		res = res_data.get(key)
		if(res != None):
			[pre, rec, f1, iou] = calcRatioOfIntersectArea(ref, res)
			accuracy.append([pre, rec, f1, iou])
			txt = key + ' ' + str(pre) + ' ' + str(rec) + ' ' + str(f1) +' '+ str(iou)+ '\n'
			file.write(txt)
			idx = idx + 1
		#else:
		#	accuracy.append(0.0)
		#idx = idx + 1
	#print(accuracy)

	# calculate average of accuracies
	sum_precision = 0.0
	sum_recall = 0.0
	sum_f1_score = 0.0
	sum_iou = 0.0

	for precision, recall, f1_score, iou in accuracy:
		sum_precision = sum_precision + precision
		sum_recall = sum_recall + recall
		sum_f1_score = sum_f1_score + f1_score
		sum_iou = sum_iou + iou

	avg_precision = sum_precision / idx
	avg_recall = sum_recall / idx
	avg_f1_score = sum_f1_score / idx
	avg_iou = sum_iou / idx

	file.close()
	print("Accuracy data in accuracy.dat")
	print("==============================")
	print("average Precision: ", round(avg_precision, 2))
	print("average Recall: ", round(avg_recall, 2))
	print("average F1_Score: ", round(avg_f1_score, 2))
	print("average IOU_Score: ", round(avg_iou, 2))
```
