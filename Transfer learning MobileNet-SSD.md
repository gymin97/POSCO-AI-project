TensorFlow Object Detection API with Coral Edge TPU
-- 

0. Object Detection API 다운로드 [Link](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#tensorflow-object-detection-api-installation)<br/>
    --> Test your Installation까지 실행된다면 성공 <br/><br/>
1. git clone [git](https://github.com/goruck/edge-tpu-train)
```
$ git clone https://github.com/goruck/models/tree/edge-tpu-train
```

2. DataSet 준비 <br/>
  ** class당 200개 data면 객체 탐지에 충분하다고 기술되어있으니 참고  <br/><br/>
  2-1) roboflow 사용시 - 데이터를 Pascal VOC 형식으로 다운로드 후 <br/>
      **image**는 images/[dataset명]/<br/>
      **annotation과 xml파일, label_map.pbtxt**는 annotation/[dataset명]/ 아래에 위치 <br/>
      ![image](https://user-images.githubusercontent.com/76406136/129432513-65f7bdfd-b012-4b54-9f84-4bb5cfd8b3d6.png)
        - label_map.pbtxt 구성 예시 <br/> <br/>
  2-2) record 파일 생성  <br/>
  tf_record/[DataSet명] 폴더에 .record 파일이 생성됨 (train:val = 8:2)
    ```
    $ python3 ./create_tf_record.py --dataset_name [DataSet명]
    ```
  
3. Pre-trained medel [다운로드](http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_quantized_300x300_coco_2019_01_03.tar.gz)<br/>
   압축해제
   ```
   $ tar -xzvf ssd_mobilenet_v2_quantized_300x300_coco_2019_01_03.tar.gz 
   ```
4. config 수정 <br/>
   configs/radar-ml 안의 pipeline_mobilenet_v2_ssd_retrain_last_few_layers.config 수정<br/>
   파일 내부 세가지 항목을 데이터에 맞게 수정
   ```
   num_classes
   input_path
   label_map_path 
   ```
   이후 수정한 파일을 configs/ 아래에 위치 시킴
5. Re-train model 
  ```
  $ train.sh \
  --pipeline_config_path ./configs/pipeline_mobilenet_v2_ssd_retrain_last_few_layers.config \
  --num_training_steps 1400
  ```
  
6. Compile model (tflite, Edge TPU)
```
$ convert_checkpoint_to_edgetpu.sh \
--pipeline_config_path ./configs/pipeline_mobilenet_v2_ssd_retrain_last_few_layers.config \
--checkpoint_num 1400
```
  ** 학습하면서 저장된 다른 ckpt파을을 사용하고싶다면 checkpoint_num 숫자 수정 

7. test 결과
  6단계에서 생성된 complie 파일을 라즈베리 파일에 옮겨 실험해보았다 
  - STOP sign <br/>
  ![image](https://user-images.githubusercontent.com/76406136/129432754-16674f69-84af-4d1e-99bf-024f741ca9cd.png)
  - 60 limit sign <br/>
  ![image](https://user-images.githubusercontent.com/76406136/129432768-f4026499-f04f-4563-81cc-9dd5346bedef.png)
  - SLOW sign <br/>
  ![image](https://user-images.githubusercontent.com/76406136/129432777-fe0fb1a5-63e0-464b-a961-a9c238001551.png)
