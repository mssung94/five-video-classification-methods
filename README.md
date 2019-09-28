# Five Video Classification Methods

> 해당 레포지토리는 원글을 번역 및 의역하였습니다.  
The repository has translated and paraphrased the original. 

> 오타 혹은 잘못된 내용은 `issue`를 통해 피드백 부탁합니다.  
Please report any errors through the issue tabs.  

> 더욱 자세한 내용에 대한 문의는 원작자에게 문의하시길 바랍니다.  
Contact the author for further information. Here is [Original GitHub](https://github.com/harvitronix/five-video-classification-methods).

## 요약 
아래와 같은 방법들을 이용하면 딥러닝을 통해서 비디오를 분류할 수 있습니다.

### Method #0 : 무작위 추측 
무작위로 추측하여 항상 가장 일반적인 것을 선택하여 분류하기

### Method #1 : CNN
CNN을 사용하여 한 번에 한 프레임씩 분류하기

### Method #2 : CNN + RNN
CNN을 사용하여 각 프레임에서 특징을 추출하여 별도의 네트워크에서 시퀀스를 RNN으로 전달하여 분류하기

### Method #3 : Time Distributed CNN
시간적으로 분산된 CNN을 사용하여 특징을 RNN에 전달. 2번과 유사하지만 모두 하나의 네트워크에 있음. 또한 이걸 해당 코드에선 `lrcn`으로 구현해뒀음

### Method #4 : CNN + MLP
CNN을 사용하여 각 프레임에서 특징을 추출하고 시퀀스를 MLP로 전달하여 분류하기

### Method #5 : 3D CNN
3D CNN 사용하여 분류하기 (2 가지 버전의 3D Convolution Layer 중에서 선택 가능)

위 내용은 [블로그](https://medium.com/@harvitronix/five-video-classification-methods-implemented-in-keras-and-tensorflow-99cad29cc0b5)를 통해서 더 자세한 내용을 확인할 수 있습니다. 

---

## 요구사항
해당 코드는 `Keras` 와 `Tensorflow`를 통해서 작성되어 있습니다.

![](https://www.dlology.com/static/media/uploads/estimator/keras-tf.jpg)

`requirements.txt`를 통해서 확인할 수 있습니다. 아래 명령어를 실행시킵니다.

```
pip install -r requirements.txt
```

You must also have `ffmpeg` installed in order to extract the video files. If `ffmpeg` isn't in your system path (ie. `which ffmpeg` doesn't return its path, or you're on an OS other than *nix), you'll need to update the path to `ffmpeg` in `data/2_extract_files.py`.

---

## 데이터 가져오기

일단 `data`폴더에 UCF 데이터셋을 다운로드 받습니다.
```
cd data && wget http://crcv.ucf.edu/data/UCF101/UCF101.rar
```

다운로드 받은 파일을 압축파일을 풉니다.
```
unrar e UCF101.rar
```

그렇게 한 뒤에 `data` 폴더 아래 `train`,`test`,`sequences`,`checkpoints` 폴더들을 생성합니다.
```
mkdir train && mkdir test && mkdir sequences && mkdir checkpoints
```

이제 `data`폴더에서 스크립트를 실행하여 비디오를 적절한 위치로 이동합니다. 프레임을 추출하고 CSV 파일을 나머지 코드 참조로 만들 수 있습니다. 아래 명령어를 순서대로 실행해야합니다. 
```
python 1_move_files.py
python 2_extract_files.py
```

---

## 특징 추출하기

일단 `lstm` 및 `mlp` 모델을 학습하기 전에 한단계 전처리 과정이 필요합니다. CNN을 사용하여 이미지에서 특징을 추출해야 하는데, 아래 명령어를 통해서 가능합니다.
```
python extract_features.py
```
이는 `GeFore 960m GPU`가 장착 된 Dell에서는 약 8시간이 걸립니다. N개의 클래스로만 제한하려는 경우, 해당 파일에서 관련된 옵션을 설정할 수 있습니다.

---

## 모델 학습하기

### 1번 모델 학습하기
CNN만을 사용하는 방법 (method #1)는 아래 명령어를 통해 학습합니다.
```
python train_cnn.py
```

### 2~5번 모델 학습하기
나머지 모델(method #2~5)은 아래 명령어를 통해서 학습을 진행합니다. 학습하려는 모델을 선택하기 위해 해당 파일에 관련 옵션을 설정할 수 있습니다.
```
python train.py
```

해당 모델은 모두 `models.py`에 정의되어 있습니다. `train.py`에서 실행할 수있는 모델을 보려면 해당 파일을 참조하십시오.


### Tensorboard를 통해 진행사항 확인하기
학습 로그는 CSV 및 TensorBoard 파일에 저장됩니다. 훈련하는 동안 진행 상황을 보려면 프로젝트 루트 폴더에서 아래 명령어를 실행하십시오.
```
tensorboard --logdir = data / logs
```

---

## Demo/Using models

임의의 비디오 파일을 예측을 얻을 수 있는 데모는 아직 구현하지 않았습니다. 도움을 주고 싶으면 언제든지 `pull request` 해주시길 바랍니다.

## 해야할 일

- [ ] 오버피팅을 피하기 위해 데이터 augmentation 기능 추가하기
- [x] 더욱 빠른 학습을 위하여 데이터 generator에서 다수의 작업자들을 지원하기
- [ ] 학습된 모델을 통한 Demo 스크립트 추가하기
- [ ] 다른 데이터셋도 학습할 수 있게 지원하기
- [ ] optical flow을 구현하기
- [ ] optical flow과 CNN을 혼합한 네트워크와 같이 복잡한 아키텍처 구현하기

## 참고문헌
- UCF101 Citation : Khurram Soomro, Amir Roshan Zamir and Mubarak Shah, UCF101: A Dataset of 101 Human Action Classes From Videos in The Wild., CRCV-TR-12-01, November, 2012. 