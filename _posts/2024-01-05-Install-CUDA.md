---
title: Install CUDA/cudnn on Ubuntu23.10
layout: post
categories: [setup]
tags: [ubuntu23.10, cuda, cudnn]
---

# 1. Check Driver

command line 에서 아래 명령어를 입력하여 현재 설치된 그래픽 드라이버를 확인한다.

```bash
ubuntu-drivers devices
```

![terminal output for driver devices](/assets/images/post0105/Untitled.png)

소프트웨어 및 업데이트를 실행한 뒤 설정에서 추가 드라이버 탭을 눌러 현재 세팅된 드라이버를 확인한다.

![Untitled](/assets/images/post0105/Untitled 1.png)

`nvidia-driver-535`로 setting 된 것이 확인된다.

# 2. CUDA Toolkit 설치

Select the cuda toolkit version refer to the nvidia driver version. Download CUDA Toolkit 12.2. select target platform as below image

[CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)

![Untitled](/assets/images/post0105/Untitled 2.png)

enter the instruction in the terminal (shortcut → ctr+alt+T).

만약 진행 중 에러 발생 시 아래 링크 참조

[cuda 설치 시 libtinfo5 에러 발생](#troubleshooting) 

# 3. Cudnn 설치

아래 링크에서 Cudnn tar 파일을 다운로드 받는다.

[CUDNN Download Link](https://developer.nvidia.com/rdp/cudnn-download)

![Untitled](/assets/images/post0105/Untitled 3.png)

터미널로 이동하여 아래 지침을 실행한다.

1. Extract cudnn file
    ```bash
    tar -xvf cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz
    ```

1. Copy bin files into cuda directory
    ```bash
    cd cudnn-linux-x86_64-8.9.2.26_cuda12-archive
    sudo cp include/cudnn* /usr/local/cuda/include
    sudo cp lib/libcudnn* /usr/local/cuda/lib64
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
    ```

1. Link files
    <aside>
    💡 경로 및 파일 이름은 cuda 및 cudnn 버전에 따라 변경될 수 있음.
    </aside>

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
## cuda 설치 시 libtinfo5 에러 발생

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

