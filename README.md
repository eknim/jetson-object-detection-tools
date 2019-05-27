## NVIDIA Jetson AGX Xavier
인공지능 알고리즘들은 대부분 막대한 양의 연산을 필요로 하기 때문에, 고사양 하드웨어와 대용량 스토리지, 클라우드에 의존하고 있다. 실 산업에 지능형 사물화가 적용되기 위해서는 네트워크 지연, 보안, 컴퓨팅 파워, 전력 제약과 같은 제한된 조건에서 인공지능 연산을 수행할 수 있는 임베디드 시스템이 필요하다. GPU가속화에 기반한 딥러닝 및 컴퓨터 비전에 적합한 고성능 저전력 컴퓨팅 플랫폼인 Nvidia Jetson AGX Xavier에서 yolo 영상인식 모델을 이식 및 시험해보았다.

## trt yolo
Nvidia가 제공하는 버젼의 YOLO를 사용하면, Nvidia TensorRT의 모델 최적화 및 DeepStream SDK의 동영상 처리 지연을 최소화를 통해 극적인 성능 향상 결과를 얻을 수 있다. 따라서 Xavier 보드에 YOLO를 이식, 최적화 하려는 목적을 위해서는 CUDA 및 cudnn 가속만을 설정할 수 있는 기존 YOLO 소스 대신, Nvidia의 아래의 github repository에 공개되어 있는 deepstream_reference_apps 프로젝트의 YOLO 프로젝트 소스를 이용하여 이식한다. (https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps) 

## 프로젝트 빌드

## 영상인식 테스트

## 결과