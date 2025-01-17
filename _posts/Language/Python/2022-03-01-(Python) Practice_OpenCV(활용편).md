---
layout: single
title:  "[Python] Practice_OpenCV(활용편)"
categories: PY
tag: [python, opencv예제]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

<head>
  <style>
    table.dataframe {
      white-space: normal;
      width: 100%;
      height: 240px;
      display: block;
      overflow: auto;
      font-family: Arial, sans-serif;
      font-size: 0.9rem;
      line-height: 20px;
      text-align: center;
      border: 0px !important;
    }

    table.dataframe th {
      text-align: center;
      font-weight: bold;
      padding: 8px;
    }
    
    table.dataframe td {
      text-align: center;
      padding: 8px;
    }
    
    table.dataframe tr:hover {
      background: #b8d1f3; 
    }
    
    .output_prompt {
      overflow: auto;
      font-size: 0.9rem;
      line-height: 1.45;
      border-radius: 0.3rem;
      -webkit-overflow-scrolling: touch;
      padding: 0.8rem;
      margin-top: 0;
      margin-bottom: 15px;
      font: 1rem Consolas, "Liberation Mono", Menlo, Courier, monospace;
      color: $code-text-color;
      border: solid 1px $border-color;
      border-radius: 0.3rem;
      word-break: normal;
      white-space: pre;
    }

  .dataframe tbody tr th:only-of-type {
      vertical-align: middle;
  }

  .dataframe tbody tr th {
      vertical-align: top;
  }

  .dataframe thead th {
      text-align: center !important;
      padding: 8px;
  }

  .page__content p {
      margin: 0 0 0px !important;
  }

  .page__content p > strong {
    font-size: 0.8rem !important;
  }

  </style>
</head>


# 프로젝트 : 얼굴을 인식하여 캐릭터 씌우기

* MediaPipe 검색 및 홈페이지 접근 후 Face Detection 이부분의 예제 코드를 사용


## Face Detection vs Face Recognition


패키지 설치

> pip install mediapipe


예제 동영상 : https://www.pexels.com/video/3256542/  

크기 : Full HD (1920 x 1080)  

파일명 : face_video.mp4



캐릭터 이미지 : https://www.freepik.com/free-vector/cute-animal-masks-video-chat-application-effect-filters-set_6380101.htm  

파일명 : right_eye.png (100 x 100), left_eye.png (100 x 100), nose.png (300 x 100)



무료 이미지 편집 도구 : https://pixlr.com/kr/  

(Pixlr E -Advanced Editor)



```python
import cv2
import mediapipe as mp

def overlay(image, x, y, w, h, overlay_image): # 대상 이미지 (3채널), x, y 좌표, width, height, 덮어씌울 이미지 (4채널:투명도를 가짐)
    alpha = overlay_image[:, :, 3] # BGRA
    mask_image = alpha / 255 # 0 ~ 255 -> 255 로 나누면 0 ~ 1 사이의 값 (1: 불투명, 0: 완전투명)
    # (255, 255)  ->  (1, 1)
    # (255, 0)        (1, 0)
    
    # 1 - mask_image ? 위와 서로 반전된 값
    # (0, 0)
    # (0, 1)
    
    # 즉, 완전 투명(0)은 1-mask_image 곱해서 쓰고, 불투명(1)은 overlay_image를 가져다 쓴다라고 이해
    # 불투명(1)이면 image부분은 곱하면 0이 될테니까!!
    for c in range(0, 3): # channel BGR
        image[y-h:y+h, x-w:x+w, c] = (overlay_image[:, :, c] * mask_image) + (image[y-h:y+h, x-w:x+w, c] * (1 - mask_image))

# 얼굴을 찾고, 찾은 얼굴에 표시를 해주기 위한 변수 정의
mp_face_detection = mp.solutions.face_detection # 얼굴 검출을 위한 face_detection 모듈을 사용
mp_drawing = mp.solutions.drawing_utils # 얼굴의 특징을 그리기 위한 drawing_utils 모듈을 사용

# 동영상 파일 열기
cap = cv2.VideoCapture('face_video.mp4')

# 이미지 불러오기(참고 : 그림판으로 만든 이미지로 먼저 하고나서, 삭제하고 캐릭터 사진 다운받은걸로 함.(기존꺼 삭제한거라 제목 동일))
image_right_eye = cv2.imread('right_eye.png', cv2.IMREAD_UNCHANGED) # 100 x 100
image_left_eye = cv2.imread('left_eye.png', cv2.IMREAD_UNCHANGED) # 100 x 100
image_nose = cv2.imread('nose.png', cv2.IMREAD_UNCHANGED) # 300 x 100 (가로, 세로)

# model_selection은 근거리는 0, 조금 먼거리는 1로 설정
# min_detection_confidence는 얼굴 인식 신뢰도 설정(현재 70%)
with mp_face_detection.FaceDetection(model_selection=0, min_detection_confidence=0.7) as face_detection:
    while cap.isOpened():
        success, image = cap.read()
        if not success: # 영상 끝나면
            break # 탈출

        # To improve performance, optionally mark the image as not writeable to
        # pass by reference.
        image.flags.writeable = False
        image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB) # BGR을 RGB로 바꾸는 작업
        results = face_detection.process(image)

        # Draw the face detection annotations on the image.
        image.flags.writeable = True
        image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR) # RGB를 BGR로 바꾸는 작업
        
        if results.detections:
            # 6개 특징 : 오른쪽 눈, 왼쪽 눈, 코 끝부분, 입 중심, 오른쪽 귀, 왼쪽 귀 (귀구슬점, 이주)
            for detection in results.detections:
                # mp_drawing.draw_detection(image, detection) # 얼굴에 네모난 영역 그려줌
                # print(detection) # 6개의 위치를 좌표로 뽑아줌
                
                # 특정 위치 가져오기
                keypoints = detection.location_data.relative_keypoints # detection출력했을때 나오는 dict의 키값으로 접근하는것
                right_eye = keypoints[0] # 오른쪽 눈
                left_eye = keypoints[1] # 왼쪽 눈
                nose_tip = keypoints[2] # 코 끝부분
                
                # detection출력했을때 좌표가 x:0.39~~, y:0.35~~ 이런식으로 나오기 때문에(비율)
                # image에서의 실제 좌표를 구하는 것을 해야한다.
                h, w, _ = image.shape # height, width, channel : 이미지로부터 세로, 가로 크기 가져옴
                right_eye = (int(right_eye.x * w) - 20, int(right_eye.y * h) - 100) # 이미지 내에서 실제 좌표 (x, y) : 튜플로 저장
                left_eye = (int(left_eye.x * w) + 20, int(left_eye.y * h) - 100)
                nose_tip = (int(nose_tip.x * w), int(nose_tip.y * h))
                
                # 양 눈에 동그라미 그리기
                # cv2.circle(image, right_eye, 50, (255, 0, 0), 10, cv2.LINE_AA) # 파란색
                # cv2.circle(image, left_eye, 50, (0, 255, 0), 10, cv2.LINE_AA) # 초록색                
                # 코에 동그라미 그리기
                # cv2.circle(image, nose_tip, 75, (0, 255, 255), 10, cv2.LINE_AA) # 노란색
                
                # 각 특징에다가 이미지 그리기(직접 그림판의 그린 그림 넣는중)                
                #image[right_eye[1]-50:right_eye[1]+50, right_eye[0]-50:right_eye[0]+50] = image_right_eye
                #image[left_eye[1]-50:left_eye[1]+50, left_eye[0]-50:left_eye[0]+50] = image_left_eye
                #image[nose_tip[1]-50:nose_tip[1]+50, nose_tip[0]-150:nose_tip[0]+150] = image_nose
                
                # image, x, y, w, h, overlay_image(캐릭터 그림 넣는중, 영상의 image는 채널:4라서 3과 안맞아서 overlay함수 따로 만들어서 사용)
                overlay(image, *right_eye, 50, 50, image_right_eye)
                overlay(image, *left_eye, 50, 50, image_left_eye)
                overlay(image, *nose_tip, 150, 50, image_nose)
                
        # Flip the image horizontally for a selfie-view display.
        cv2.imshow('MediaPipe Face Detection', cv2.resize(image, None, fx=0.5, fy=0.5))
        
        if cv2.waitKey(1) == ord('q'):
            break
            
cap.release()
cv2.destroyAllWindows()
```
