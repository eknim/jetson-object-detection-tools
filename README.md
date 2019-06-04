## Jetson AGX Xavier 임베디드 딥러닝 보드
인공지능 알고리즘들은 대부분 막대한 양의 연산을 필요로 하기 때문에, 고사양 하드웨어와 대용량 스토리지, 클라우드에 의존하고 있다. 실 산업에 지능형 사물화가 적용되기 위해서는 네트워크 지연, 보안, 컴퓨팅 파워, 전력 제약과 같은 제한된 조건에서 인공지능 연산을 수행할 수 있는 임베디드 시스템이 필요하다. GPU가속화에 기반한 딥러닝 및 컴퓨터 비전에 적합한 고성능 저전력 컴퓨팅 플랫폼인 Nvidia Jetson AGX Xavier에서 yolo 영상인식 모델을 이식 및 시험해보았다.

## TensorRT로 가속된 YOLO
Nvidia가 제공하는 버젼의 YOLO를 사용하면, Nvidia TensorRT의 모델 최적화 및 DeepStream SDK의 동영상 처리 지연을 최소화를 통해 극적인 성능 향상 결과를 얻을 수 있다. 따라서 Xavier 보드에 YOLO를 이식, 최적화 하려는 목적을 위해서는 CUDA 및 cudnn 가속만을 설정할 수 있는 기존 YOLO 소스 대신, Nvidia의 아래의 github repository에 공개되어 있는 deepstream_reference_apps 프로젝트의 YOLO 프로젝트 소스를 이용하여 이식한다. (https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps) 

## 프로젝트 빌드

프로젝트를 아래에서 클론 혹은 다운로드한다. 
```
git clone https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps/tree/master/yolo
```

TensorRT YOLO 빌드를 위해서 우선 pre-requisotes와 yolo 플러그인을 설치한다. 
```
$ prebuild.sh
$ cd plugins/gst-yoloplugin-tegra
$ mkdir build && cd build
$ cmake -D DS_SDK_ROOT=<DS SDK Root> -D CMAKE_BUILD_TYPE=Release ..
$ make && sudo make install
```

Makefile에서 CUDA 버전과 Platform 정보를 아래와 같이 설정한다.
```
$ vi Makefile.config

CUDA_VER:=10.0
PLATFORM:=Tegra
```

이제 TensorRT로 최적화된 YOLO를 시험할수 있는 trt-yolo-app을 아래와 같이 빌드한다.

```
$ cd apps/trt-yolo
$ mkdir build && cd build
$ cmake -D CMAKE_BUILD_TYPE=Release .. $ make && sudo make install
$ cd ../../../
```

Nvidia TensorRT 가속에 동영상 처리 가속 기능인 Nvidia DeepStream 라이브러리 기능을 추가로 이용하려면 deepstream-yolo-app을 아래 명령어에 따라 빌드한다. 

```
$ cd apps/deepstream-yolo
$ mkdir build && cd build
$ cmake -D DS_SDK_ROOT=<DS SDK Root> -D CMAKE_BUILD_TYPE=Release ..
$ make && sudo make install
$ cd ../../../
```

## 영상인식 테스트

영상인식에 널리 쓰이는 COCO 이미지에 기학습된 YOLO 모델을 다음과 같이 다운로드 받아 이용한다. 
```
$ cd data/yolo
$ wget https://pjreddie.com/media/files/yolov3.weights 
```

TensorRT로 가속되는 trt-yolo-app를 아래 명령어로 실행한다. 테스트 하려는 이미지는 data 디렉토리의 test_images.txt 파일에 미리 명시해 둔다.
```
$ trt-yolo-app —flagfile=./config/yolo3.txt
```

비디오 시험을 위해서는 deepstream-yolo-app을 아래 명령어로 실행한다. 비디오 포맷은 h.264가 요구된다.
```
$ deepstream-yolo-app Tegra <H264 filename> <yolo-plugin config file>
```

## 결과
<p align="center">
  <img src="https://github.com/eeknim/nvidia-jetson-trt-yolo-test/blob/master/misc/yolo_output.png" alt="Example result of TRT-YOLO"/>
</p>

> 기존 YOLOv3 VS TensorRT 가속 YOLOv3 성능

처리시간 | YOLOv3 | YOLOv3
:---: | :---: | :---:
이미지 입력 | 310 ms | **61 ms**
비디오 입력 | 312 ms(3.2 FPS) | **75 ms(13 FPS)**
추론 속도가 약 5배 향상되었다. 
