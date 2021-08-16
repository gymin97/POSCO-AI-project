## Night Auto-Driving with DonkeyCar | 동키카 야간 자율주행 프로젝트
- 동키카 4.0과 Object Detection (SSD Mobilenet V2)을 이용하여 야간 자율주행 프로젝트를 진행하였습니다.
  - 소켓통신이아닌 google coral edge tpu에 Object detection 모델을 업로드하여 객체탐지를 진행하였습니다.      
- 프로젝트를 진행하며 YOLOv5, YOLOv4, SSD MobileNetv2 transfer learning 모델을 실험하였고 해당 레포지토리에서는 3가지 모델을 transfer learning하는 가이드라인을 제시합니다.
- Image Labeling Tool은 [Roboflow](https://app.roboflow.com/)을 이용하였습니다. <br/><br/>


### Coral USB Accelator (Edge TPU)에 딥러닝 모델을 사용하고자하는 경우
Coral USB Accelator = USB 포트에 연결하기만하면 광범위한 시스템에서 고속머신러닝이 가능하도록 하는 장치
1. Edge TPU를 사용하는경우 tflite(weights file)을 Edge TPU model로 컴파일하는 과정이 필요합니다 <br/> 
![image](https://user-images.githubusercontent.com/76406136/129430909-c4721ff7-0847-43d2-816c-8a4b18f47195.png)

2. Edge TPU model로 컴파일하기 위해서는 몇가지 조건이 있습니다. 그중 유의깊게 봐야할 조건으로는 <br/> 
    **① TensorFlow parameters는 모두 양자화(8비트 고정-소수점)하여 학습 시키거나, 학습 이후 모델을 양자화 해야한다.** <br/> 
    양자화된 모델을 만들기 위해서는 TensorFlow 1.15이상을 사용해야 하며, **입/출력을 모두 uint8**로 지정해야 한다. <br/> 
    최신 버전의 **TensorFlow 2.X는 floor만 지원**하기 때문에 TensorFlow 2.0과 Keras API를 사용하면 모델을 구축하고 러닝이 가능하다.
    단, 모델을 TensorFlow Lite 형식으로 변환해야 하며, <br/>
    **TensorFlow 2.0의 TFLite Converter가 양자화 과정 중 uint8 입/출력을 지원하지 않고 있기 때문에 TensorFlow 2.0 자체는 Edge TPU를 지원하지 않고 있다.** <br/> 
[[Edge TPU 모델 생성 나머지 조건 확인하기](https://grip.news/archives/1800)]         [[참고하면 좋을 Edge TPU FAQ](https://grip.news/archives/1805)] <br/>

3. Transfet learning <br/>
  [google 공식 문서](https://coral.ai/docs/edgetpu/retrain-detection/#requirements) 참고
  
  
