---
title: Install CUDA/cudnn on Ubuntu23.10
layout: post
categories: [setup]
tags: [ubuntu23.10, cuda, cudnn]
---

Dell Workstation을 Server 용으로 사용하기 위해서 필요한 SW들을 설치할 예정인데요 오늘은 Ubuntu23.10 을 새롭게 설치한 뒤 딥러닝 개발을 위해 필수적인 NVIDIA의 CUDA와 Cudnn을 설치하는 방법을 간략히 살펴보겠습니다.

# 1. Check Driver

먼저 기본적으로 설치되어 있는 그래픽 드라이버를 확인해야 합니다. command line 에서 아래 명령어를 입력하여 현재 설치된 그래픽 드라이버를 확인할 수 있습니다.

```bash
ubuntu-drivers devices
```

![terminal output for driver devices](/assets/images/post0105/Untitled.png)

다음으로 UI화면에서 소프트웨어 및 업데이트를 실행한 뒤 설정에서 추가 드라이버 탭을 눌러 현재 세팅된 드라이버가 무엇인지 확인할 수 있습니다. 현재 이 컴퓨터에는 **nvidia-driver-535**로 setting 된 것이 확인되고 있습니다. 

![Untitled](/assets/images/post0105/Untitled 1.png)

# 2. CUDA Toolkit 설치

이제 CUDA toolkit을 설치해야 합니다. 아래 링크에서 아까 확인한 nvidia 드라이버 버전과 매치되는 CUDA Toolkit을 찾아서 다운을 받으면 되는데요 여기서는 12.2 버전을 설치하려고 합니다.

[CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)

![Untitled](/assets/images/post0105/Untitled 2.png)

다운로드시 base installer에 installation instruction이 잘 나와있어서 순차적으로 실행하면 CUDA Toolkit은 매우 쉽게 설치가 됩니다.

현재 Ubuntu 23.10에서는 libtinfo5에러가 발생할 수 있는데 이것을 해결하기 위해서는 아래 링크를 참고하시면 됩니다.

[libtinfo5 에러 발생 대책](#troubleshooting) 

# 3. Cudnn 설치

다음으로 Cudnn 을 설치해야 합니다. 마찬가지로 아래 링크에서 CUDA toolkit 버전에 맞는 cudnn을 설치하면 됩니다. 여기서는 8.9.7의 tar ball을 다운로드 받아서 설치하도록 하겠습니다.

[CUDNN Download Link](https://developer.nvidia.com/rdp/cudnn-download)

![Untitled](/assets/images/post0105/Untitled 3.png)

cudnn은 CUDA Toolkit 과는 달리 아래과 같이 압축을 풀고 빌드된 파일을 CUDA toolkit이 설치된 장소에 복사 해야 합니다. 그 뒤 파일의 링크를 걸어 문제없이 사용할 수 있도록 설정해야 합니다.

터미널로 이동하여 아래 지침을 실행하세요.

1. **Extract cudnn file**

```bash
tar -xvf cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz
```

1. **Copy bin files into cuda directory**

```bash
cd cudnn-linux-x86_64-8.9.2.26_cuda12-archive
sudo cp include/cudnn* /usr/local/cuda/include
sudo cp lib/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

1. **Link files (***경로 및 파일 이름은 cuda 및 cudnn 버전에 따라 변경될 수 있습니다.***)**

```bash
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_adv_train.so.8.9.7 /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_adv_train.so.8
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8.9.7  /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8.9.7  /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8.9.7  /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_ops_train.so.8.9.7  /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_ops_train.so.8
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8.9.7 /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8
sudo ln -sf /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn.so.8.9.7 /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcudnn.so.8
```


# Troubleshooting

## A. Cuda 설치 시 libtinfo5 에러 발생 대책

The `libtinfo5` package isn't available in Ubuntu 23.10's default repositories yet. We can install it by adding the `universe` repo for Ubuntu 23.04 (Lunar Lobster).

![Untitled](/assets/images/post0105/Untitled 4.png)

1. Open a terminal window and run:
    ```bash
    sudo nano /etc/apt/sources.list
    ```

1. Add this line (adds the Ubuntu 23.04 aka "Lunar Lobster" universe repository to `apt`):
    ```bash
    deb http://archive.ubuntu.com/ubuntu/ lunar universe
    ```

1. Save and exit, then run:
    ```bash
    sudo apt update
    ```

1. Re-install 
    ```bash
    sudo apt-get -y install cuda
    ```

1. Install Sucess and check nvidia-smi
    ![Untitled](/assets/images/post0105/Untitled 5.png)

# Reference

- [https://hjh1023.tistory.com/58](https://hjh1023.tistory.com/58)
- [https://hjh1023.tistory.com/59](https://hjh1023.tistory.com/59)
- [https://gist.github.com/eliazonta/e98a470c09655d7f276da258984e3e5a](https://gist.github.com/eliazonta/e98a470c09655d7f276da258984e3e5a)
- [https://askubuntu.com/questions/1491254/installing-cuda-on-ubuntu-23-10-libt5info-not-installable](https://askubuntu.com/questions/1491254/installing-cuda-on-ubuntu-23-10-libt5info-not-installable)

