# GRAY-is-all-I-need
물론입니다! 아래는 제공하신 Python 비디오 레코더 코드를 위한 **README.md** 파일입니다. 이 README는 프로젝트의 개요, 기능, 설치 방법, 사용법, 코드 설명, 문제 해결 방법 및 추가 고려 사항을 포함하고 있습니다.

---

# 비디오 레코더 (흑백 효과 적용)

## 목차

1. [개요](#개요)
2. [기능](#기능)
3. [필수 사항](#필수-사항)
4. [설치 방법](#설치-방법)
5. [사용법](#사용법)
6. [코드 설명](#코드-설명)
    - [1. 라이브러리 임포트](#1-라이브러리-임포트)
    - [2. 메인 함수 정의](#2-메인-함수-정의)
    - [3. RTSP 스트림 설정 및 캡처 객체 초기화](#3-rtsp-스트림-설정-및-캡처-객체-초기화)
    - [4. 비디오 코덱 설정 및 VideoWriter 초기화](#4-비디오-코덱-설정-및-videowriter-초기화)
    - [5. 녹화 모드 변수 초기화](#5-녹화-모드-변수-초기화)
    - [6. 사용자 안내 메시지 출력](#6-사용자-안내-메시지-출력)
    - [7. 메인 루프](#7-메인-루프)
        - [7.1 프레임 캡처](#71-프레임-캡처)
        - [7.2 그레이스케일 변환](#72-그레이스케일-변환)
        - [7.3 녹화 모드 처리](#73-녹화-모드-처리)
        - [7.4 녹화 상태 표시](#74-녹화-상태-표시)
        - [7.5 프레임 화면 표시](#75-프레임-화면-표시)
        - [7.6 키 입력 처리](#76-키-입력-처리)
    - [8. 자원 해제 및 종료](#8-자원-해제-및-종료)
7. [문제 해결](#문제-해결)
8. [추가 고려 사항](#추가-고려-사항)
9. [라이선스](#라이선스)
10. [문의](#문의)

---

## 개요

이 Python 프로젝트는 **OpenCV**를 활용하여 RTSP 스트리밍 주소로부터 비디오를 캡처하고, 실시간으로 그레이스케일(흑백) 효과를 적용하여 화면에 표시하며, 녹화 모드일 때는 녹화 상태를 나타내는 빨간색 원을 화면에 표시하고 비디오 파일로 저장합니다.

## 기능

- **실시간 비디오 표시**: RTSP 스트림에서 실시간으로 비디오를 캡처하고 그레이스케일로 표시합니다.
- **녹화 기능**: **스페이스(Space)** 키를 눌러 녹화 모드와 프리뷰 모드를 전환할 수 있습니다.
- **녹화 상태 표시**: 녹화 중일 때 화면에 빨간색 원과 'REC' 텍스트를 표시하여 녹화 상태를 시각적으로 알립니다.
- **비디오 파일 저장**: 녹화 중인 비디오는 타임스탬프가 포함된 파일명으로 저장됩니다.
- **종료 기능**: **ESC** 키를 눌러 프로그램을 종료할 수 있습니다.

## 필수 사항

- **Python 3.x**: Python 3 이상이 필요합니다.
- **OpenCV**: 컴퓨터 비전 라이브러리.
- **RTSP 스트림**: RTSP 호환 비디오 스트림에 접근할 수 있어야 합니다.

## 설치 방법

1. **프로젝트 클론**

    ```bash
    git clone https://github.com/yourusername/video-recorder-grayscale.git
    cd video-recorder-grayscale
    ```

2. **가상 환경 설정 (선택 사항)**

    가상 환경을 사용하면 프로젝트 종속성을 관리하기 용이합니다.

    ```bash
    python3 -m venv venv
    source venv/bin/activate  # Windows에서는 venv\Scripts\activate
    ```

3. **필요한 Python 패키지 설치**

    ```bash
    pip install opencv-python
    ```

    > **참고**: Qt 플러그인 관련 오류가 발생할 경우, [문제 해결](#문제-해결) 섹션을 참고하세요.

## 사용법

1. **RTSP 스트림 URL 설정**

    Python 스크립트(`video_recorder.py`)를 열고 RTSP 스트림 URL을 설정합니다:

    ```python
    rtsp_url = 'rtsp://210.99.70.120:1935/live/cctv001.stream'
    ```

2. **스크립트 실행**

    ```bash
    python video_recorder.py
    ```

3. **조작 방법**

    - **스페이스(Space) 키**: 녹화 모드와 프리뷰 모드를 전환합니다.
    - **ESC 키**: 프로그램을 종료합니다.

4. **녹화된 파일 확인**

    녹화된 비디오 파일은 스크립트가 실행된 디렉토리에 `record_<타임스탬프>.avi` 형식으로 저장됩니다.

## 코드 설명

### 1. 라이브러리 임포트

```python
import cv2 as cv
import time
import os
```

- **cv2 (OpenCV)**: 비디오 캡처, 처리, 표시 등을 위한 라이브러리.
- **time**: 타임스탬프 생성 및 시간 관련 기능을 위해 사용.
- **os**: 운영 체제와의 상호작용을 위해 사용. 현재 코드에서는 환경 변수 설정에 사용될 수 있습니다.

### 2. 메인 함수 정의

```python
def main():
    ...
```

- `main()` 함수는 프로그램의 주요 로직을 포함하며, 스크립트가 직접 실행될 때 호출됩니다.

### 3. RTSP 스트림 설정 및 캡처 객체 초기화

```python
# RTSP 스트리밍 주소 설정
rtsp_url = 'rtsp://210.99.70.120:1935/live/cctv001.stream'
cap = cv.VideoCapture(rtsp_url)

if not cap.isOpened():
    print("RTSP 스트림을 열 수 없습니다.")
    return
```

- **rtsp_url**: 실시간 스트리밍 프로토콜(RTSP) 주소를 설정합니다. 이 주소는 CCTV나 IP 카메라의 스트림을 가리킵니다.
- **cv.VideoCapture**: RTSP 스트림을 캡처하기 위한 객체를 생성합니다.
- **스트림 열기 확인**: 스트림이 성공적으로 열렸는지 확인하고, 열리지 않으면 오류 메시지를 출력하고 프로그램을 종료합니다.

### 4. 비디오 코덱 설정 및 VideoWriter 초기화

```python
# 비디오 코덱 설정 (예: 'XVID', 'MJPG', 'X264', 'MP4V' 등)
fourcc = cv.VideoWriter_fourcc(*'XVID')
# 비디오 파일 저장을 위한 VideoWriter 객체 초기화 (초기에는 None)
out = None
```

- **fourcc**: 비디오 코덱을 설정합니다. 여기서는 `'XVID'` 코덱을 사용합니다. 다른 코덱으로 변경할 수 있으며, 이는 녹화된 파일의 형식과 품질에 영향을 미칩니다.
- **VideoWriter 객체 초기화**: 녹화 기능을 사용하지 않을 때는 `out`을 `None`으로 설정합니다. 녹화 모드로 전환될 때 `VideoWriter` 객체가 초기화됩니다.

### 5. 녹화 모드 변수 초기화

```python
# 초기 모드: Preview 모드
is_recording = False
```

- **is_recording**: Boolean 플래그로, 프로그램 시작 시 **Preview 모드**임을 설정합니다. **녹화 모드**로 전환될 때 이 변수가 `True`로 변경됩니다.

### 6. 사용자 안내 메시지 출력

```python
print("프로그램이 시작되었습니다.")
print("Space 키: Record/Preview 모드 전환")
print("ESC 키: 프로그램 종료")
```

- **사용자 안내**: 프로그램이 시작되었음을 알리고, **Space 키**로 녹화 모드를 전환할 수 있으며, **ESC 키**로 프로그램을 종료할 수 있음을 안내합니다.

### 7. 메인 루프

```python
while True:
    ret, frame = cap.read()
    if not ret:
        print("프레임을 가져올 수 없습니다.")
        break

    # 프레임을 그레이스케일로 변환
    gray_frame = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)

    # 그레이스케일 프레임을 다시 BGR로 변환 (VideoWriter가 컬러를 기대하기 때문에)
    gray_bgr_frame = cv.cvtColor(gray_frame, cv.COLOR_GRAY2BGR)

    # 녹화 모드일 때만 VideoWriter에 프레임 쓰기
    if is_recording:
        if out is None:
            # 동영상 파일 이름과 설정
            filename = f'record_{int(time.time())}.avi'
            frame_height, frame_width = gray_bgr_frame.shape[:2]
            out = cv.VideoWriter(filename, fourcc, 20.0, (frame_width, frame_height))
            print(f"녹화를 시작합니다: {filename}")
        out.write(gray_bgr_frame)
        # 녹화 상태 표시 (빨간색 원과 'REC' 텍스트)
        cv.circle(gray_bgr_frame, (30, 30), 10, (0, 0, 255), 10)  # 빨간색 원

    # 프레임을 화면에 표시 (gray_bgr_frame으로 변경)
    cv.imshow('Video Recorder (Grayscale)', gray_bgr_frame)

    # 키 입력 대기 (1ms)
    key = cv.waitKey(1) & 0xFF

    if key == 27:  # ESC 키
        print("프로그램을 종료합니다.")
        break
    elif key == 32:  # Space 키
        is_recording = not is_recording
        if is_recording:
            print("Record 모드로 전환되었습니다.")
        else:
            print("Preview 모드로 전환되었습니다.")
            if out is not None:
                out.release()
                out = None
                print("녹화를 중지했습니다.")
```

#### 7.1 프레임 캡처

```python
ret, frame = cap.read()
if not ret:
    print("프레임을 가져올 수 없습니다.")
    break
```

- **cap.read()**: RTSP 스트림에서 현재 프레임을 캡처합니다.
- **오류 처리**: 프레임 캡처가 실패하면 오류 메시지를 출력하고 루프를 종료합니다.

#### 7.2 그레이스케일 변환

```python
# 프레임을 그레이스케일로 변환
gray_frame = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
```

- **cv.cvtColor**: 컬러 프레임을 그레이스케일(흑백)으로 변환합니다. 색상 정보를 제거하고 밝기 정보만 남깁니다.

#### 7.3 그레이스케일 프레임을 다시 BGR로 변환

```python
# 그레이스케일 프레임을 다시 BGR로 변환 (VideoWriter가 컬러를 기대하기 때문에)
gray_bgr_frame = cv.cvtColor(gray_frame, cv.COLOR_GRAY2BGR)
```

- **BGR 변환**: `VideoWriter`는 기본적으로 3채널 컬러 프레임을 기대하므로, 그레이스케일 프레임을 다시 BGR(컬러) 형식으로 변환합니다. 이는 녹화된 파일이 정상적으로 저장되도록 하기 위함입니다.

#### 7.4 녹화 모드 처리

```python
# 녹화 모드일 때만 VideoWriter에 프레임 쓰기
if is_recording:
    if out is None:
        # 동영상 파일 이름과 설정
        filename = f'record_{int(time.time())}.avi'
        frame_height, frame_width = gray_bgr_frame.shape[:2]
        out = cv.VideoWriter(filename, fourcc, 20.0, (frame_width, frame_height))
        print(f"녹화를 시작합니다: {filename}")
    out.write(gray_bgr_frame)
    # 녹화 상태 표시 (빨간색 원과 'REC' 텍스트)
    cv.circle(gray_bgr_frame, (30, 30), 10, (0, 0, 255), 10)  # 빨간색 원
```

- **녹화 시작**: `is_recording`이 `True`일 때, `VideoWriter` 객체가 초기화되지 않았다면 새로운 녹화 파일을 생성합니다.
    - **파일 이름**: 현재 타임스탬프를 포함한 `record_<타임스탬프>.avi` 형식으로 파일명을 생성합니다.
    - **해상도**: 캡처된 프레임의 해상도를 가져옵니다.
    - **VideoWriter 객체 생성**: `cv.VideoWriter`를 사용하여 녹화 파일을 초기화합니다.
- **프레임 저장**: 현재 프레임을 녹화 파일에 저장합니다.
- **녹화 상태 표시**: 현재 프레임에 빨간색 원을 그려 녹화 중임을 시각적으로 표시합니다.

#### 7.5 프레임 화면 표시

```python
# 프레임을 화면에 표시 (gray_bgr_frame으로 변경)
cv.imshow('Video Recorder (Grayscale)', gray_bgr_frame)
```

- **cv.imshow**: 변환된 프레임(`gray_bgr_frame`)을 창에 표시합니다. 창의 제목은 `'Video Recorder (Grayscale)'`입니다.
- **녹화 상태 표시 반영**: 이전 단계에서 `gray_bgr_frame`에 그린 빨간색 원이 이 화면에 표시됩니다.

#### 7.6 키 입력 처리

```python
# 키 입력 대기 (1ms)
key = cv.waitKey(1) & 0xFF

if key == 27:  # ESC 키
    print("프로그램을 종료합니다.")
    break
elif key == 32:  # Space 키
    is_recording = not is_recording
    if is_recording:
        print("Record 모드로 전환되었습니다.")
    else:
        print("Preview 모드로 전환되었습니다.")
        if out is not None:
            out.release()
            out = None
            print("녹화를 중지했습니다.")
```

- **cv.waitKey(1)**: 1밀리초 동안 키 입력을 대기합니다. 반환된 값은 비트 연산자를 사용하여 하위 8비트만 추출합니다.
- **ESC 키 (27)**: 프로그램을 종료합니다. 루프를 탈출하고 자원을 해제합니다.
- **Space 키 (32)**: 녹화 모드를 전환합니다.
    - **녹화 모드 활성화**: `is_recording`을 `True`로 설정하고 녹화를 시작합니다.
    - **녹화 모드 비활성화**: `is_recording`을 `False`로 설정하고 녹화를 중지하며 `VideoWriter` 객체를 해제합니다.

### 8. 자원 해제 및 종료

```python
# 자원 해제
cap.release()
if out is not None:
    out.release()
cv.destroyAllWindows()
```

- **캡처 객체 해제**: `cap.release()`를 호출하여 비디오 캡처 객체를 해제합니다.
- **VideoWriter 객체 해제**: `out`이 `None`이 아닐 경우 `out.release()`를 호출하여 비디오 파일 저장을 종료합니다.
- **모든 OpenCV 창 닫기**: `cv.destroyAllWindows()`를 호출하여 모든 OpenCV 창을 닫습니다.

### 9. 스크립트 실행 지점

```python
if __name__ == "__main__":
    # 환경 변수 설정 (필요 시)
    # os.environ['QT_QPA_PLATFORM_PLUGIN_PATH'] = '/usr/local/lib/python3.8/dist-packages/cv2/qt/plugins/platforms'
    main()
```

- **Entry Point**: 스크립트가 직접 실행될 경우(`__name__ == "__main__"`), `main()` 함수를 호출합니다.
- **환경 변수 설정**: 주석 처리된 라인은 Qt 플랫폼 플러그인의 경로를 설정하는 부분입니다. 필요 시 주석을 해제하고 경로를 올바르게 설정할 수 있습니다.

## 문제 해결

### Qt 플랫폼 플러그인 "xcb" 로드 오류

프로그램 실행 시 다음과 같은 오류가 발생할 경우:

```
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "/usr/local/lib/python3.8/dist-packages/cv2/qt/plugins" even though it was found.
This application failed to start because no Qt platform plugin could be initialized.

Available platform plugins are: xcb.

Aborted (core dumped)
```

**해결 방법**:

1. **필요한 시스템 라이브러리 설치**

    Debian/Ubuntu 기반 시스템에서 다음 명령어를 실행하여 필요한 라이브러리를 설치합니다:

    ```bash
    sudo apt-get update
    sudo apt-get install -y libxcb-xinerama0 libxcb1 libx11-xcb1 libxcb-icccm4 libxcb-image0 \
    libxcb-keysyms1 libxcb-render0 libxcb-shm0 libxcb-util1 libxcb-xfixes0 libxkbcommon-x11-0 \
    libxkbcommon0 libxrender1 libxext6 libxi6 libxkbcommon-x11-0
    ```

2. **Qt 플랫폼 플러그인 경로 설정**

    스크립트 내에서 Qt 플랫폼 플러그인의 경로를 명시적으로 설정합니다. `main()` 함수 위에 다음 코드를 추가합니다:

    ```python
    os.environ['QT_QPA_PLATFORM_PLUGIN_PATH'] = '/usr/local/lib/python3.8/dist-packages/cv2/qt/plugins/platforms'
    ```

    > **주의**: 경로는 실제 플러그인이 위치한 경로로 변경해야 합니다. 경로가 맞는지 확인하려면 다음 명령어로 플러그인의 위치를 확인할 수 있습니다:

    ```bash
    find /usr/local/lib/python3.8/dist-packages/cv2/qt/plugins -name "libqxcb.so"
    ```

3. **가상 프레임 버퍼(Xvfb) 사용 (헤드리스 환경)**

    Docker 컨테이너나 GUI가 없는 서버 환경에서는 가상 프레임 버퍼를 설정하여 GUI 창 없이도 프로그램을 실행할 수 있습니다.

    **Dockerfile 예제**:

    ```dockerfile
    FROM python:3.8-slim

    # 필수 패키지 설치
    RUN apt-get update && apt-get install -y \
        libxcb-xinerama0 \
        libxcb1 \
        libx11-xcb1 \
        libxcb-icccm4 \
        libxcb-image0 \
        libxcb-keysyms1 \
        libxcb-render0 \
        libxcb-shm0 \
        libxcb-util1 \
        libxcb-xfixes0 \
        libxkbcommon-x11-0 \
        libxkbcommon0 \
        libxrender1 \
        libxext6 \
        libxi6 \
        xvfb \
        && rm -rf /var/lib/apt/lists/*

    # Python 패키지 설치
    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt

    # 애플리케이션 코드 복사
    COPY . /app
    WORKDIR /app

    # 가상 프레임 버퍼 실행과 함께 애플리케이션 실행
    CMD ["sh", "-c", "Xvfb :99 -screen 0 1280x720x24 & export DISPLAY=:99 && python video_recorder.py"]
    ```

    **Docker 컨테이너 내에서 직접 설정할 경우**:

    ```bash
    # 실행 중인 컨테이너에 접속
    docker exec -it <컨테이너_이름 또는 ID> /bin/bash

    # 컨테이너 내에서 필요한 패키지 설치
    apt-get update && apt-get install -y \
        libxcb-xinerama0 \
        libxcb1 \
        libx11-xcb1 \
        libxcb-icccm4 \
        libxcb-image0 \
        libxcb-keysyms1 \
        libxcb-render0 \
        libxcb-shm0 \
        libxcb-util1 \
        libxcb-xfixes0 \
        libxkbcommon-x11-0 \
        libxkbcommon0 \
        libxrender1 \
        libxext6 \
        libxi6 \
        xvfb \
        && rm -rf /var/lib/apt/lists/*

    # Xvfb 실행 및 DISPLAY 설정
    Xvfb :99 -screen 0 1280x720x24 &
    export DISPLAY=:99

    # Python 스크립트 실행
    python video_recorder.py
    ```

    > **주의**: 컨테이너가 재시작되면 위의 설정이 사라질 수 있습니다. 가능한 경우 Dockerfile을 수정하여 이러한 패키지를 설치하고, 가상 디스플레이를 설정하는 것이 더 안정적입니다.

## 추가 고려 사항

1. **녹화 상태 표시 위치 및 크기 조정**

    현재 코드에서는 화면의 좌측 상단에 빨간색 원이 표시됩니다. 위치와 크기를 변경하고 싶다면 `cv.circle` 함수의 좌표와 반지름 값을 조정할 수 있습니다.

    예시: 화면의 우측 상단에 표시하려면 화면의 너비를 가져와 좌표를 조정합니다.

    ```python
    height, width = gray_bgr_frame.shape[:2]
    cv.circle(gray_bgr_frame, (width - 30, 30), 10, (0, 0, 255), 10)  # 우측 상단
    cv.putText(gray_bgr_frame, 'REC', (width - 80, 35), cv.FONT_HERSHEY_SIMPLEX, 0.7, (0, 0, 255), 2)
    ```

2. **비디오 코덱 및 파일 형식 변경**

    다른 코덱이나 파일 형식을 사용하여 녹화된 파일의 품질과 크기를 조정할 수 있습니다.

    예시: MP4 형식으로 저장하려면 `'mp4v'` 코덱을 사용하고 파일 확장자를 변경합니다.

    ```python
    fourcc = cv.VideoWriter_fourcc(*'mp4v')
    filename = f'record_{int(time.time())}.mp4'
    out = cv.VideoWriter(filename, fourcc, 20.0, (frame_width, frame_height))
    ```

3. **프레임 레이트 조정**

    녹화 품질을 조정하기 위해 프레임 레이트를 변경할 수 있습니다. 현재는 `20.0` FPS로 설정되어 있습니다.

    예시: 30 FPS로 변경하려면:

    ```python
    out = cv.VideoWriter(filename, fourcc, 30.0, (frame_width, frame_height))
    ```

4. **오류 핸들링 강화**

    네트워크 스트림이 끊겼을 때 자동으로 재연결을 시도하거나, 예외 처리를 추가하여 프로그램의 안정성을 높일 수 있습니다.

    예시: 스트림이 끊겼을 때 재시도 로직 추가.

    ```python
    while True:
        ret, frame = cap.read()
        if not ret:
            print("프레임을 가져올 수 없습니다. 재연결을 시도합니다...")
            cap.release()
            time.sleep(5)  # 5초 대기 후 재연결 시도
            cap = cv.VideoCapture(rtsp_url)
            if not cap.isOpened():
                print("RTSP 스트림을 열 수 없습니다.")
                continue
        # 나머지 코드...
    ```

5. **환경 변수 설정 확인**

    Qt 관련 오류가 발생할 경우, `QT_QPA_PLATFORM_PLUGIN_PATH` 환경 변수를 설정하여 문제를 해결할 수 있습니다. 스크립트 내에서 다음과 같이 설정할 수 있습니다:

    ```python
    os.environ['QT_QPA_PLATFORM_PLUGIN_PATH'] = '/usr/local/lib/python3.8/dist-packages/cv2/qt/plugins/platforms'
    ```

6. **헤드리스(Headless) 환경에서 실행**

    Docker 컨테이너나 GUI가 없는 환경에서 실행할 경우, `Xvfb`와 같은 가상 프레임 버퍼를 설정하여 GUI 창 없이도 프로그램을 실행할 수 있습니다.

    **Dockerfile 예제**:

    ```dockerfile
    FROM python:3.8-slim

    # 필수 패키지 설치
    RUN apt-get update && apt-get install -y \
        libxcb-xinerama0 \
        libxcb1 \
        libx11-xcb1 \
        libxcb-icccm4 \
        libxcb-image0 \
        libxcb-keysyms1 \
        libxcb-render0 \
        libxcb-shm0 \
        libxcb-util1 \
        libxcb-xfixes0 \
        libxkbcommon-x11-0 \
        libxkbcommon0 \
        libxrender1 \
        libxext6 \
        libxi6 \
        xvfb \
        && rm -rf /var/lib/apt/lists/*

    # 환경 변수 설정
    ENV QT_QPA_PLATFORM_PLUGIN_PATH=/usr/local/lib/python3.8/dist-packages/cv2/qt/plugins/platforms

    # Python 패키지 설치
    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt

    # 애플리케이션 코드 복사
    COPY . /app
    WORKDIR /app

    # 가상 프레임 버퍼 실행과 함께 애플리케이션 실행
    CMD ["sh", "-c", "Xvfb :99 -screen 0 1280x720x24 & export DISPLAY=:99 && python video_recorder.py"]
    ```

## 라이선스

이 프로젝트는 [MIT 라이선스](LICENSE)를 따릅니다.

## 문의

질문이나 제안 사항이 있으시면 [your-email@example.com](mailto:your-email@example.com)으로 연락해 주세요.

---

**참고 사항**:

- **환경 변수 설정**: Qt 관련 오류가 발생할 경우, 환경 변수를 적절히 설정하여 문제를 해결할 수 있습니다.
- **Docker 사용**: Docker를 사용하여 헤드리스 환경에서 프로그램을 실행할 때는 Dockerfile을 적절히 수정하여 필요한 라이브러리와 가상 디스플레이를 설정해야 합니다.
- **추가 기능 구현**: 필요에 따라 녹화 상태 표시를 개선하거나, 녹화된 비디오에 추가적인 오버레이를 추가할 수 있습니다.

---

위의 README 파일을 프로젝트의 루트 디렉토리에 `README.md` 파일로 저장하시면 됩니다. 프로젝트의 구체적인 요구사항에 맞게 내용을 추가하거나 수정하실 수 있습니다. 추가적인 도움이 필요하시면 언제든지 문의해 주세요!
