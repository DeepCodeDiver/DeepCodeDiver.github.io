---
title: Create Container in Portainer
layout: post
categories: [setup]
tags: [ubuntu23.10, container, portainer]
---

Docker 와 Portainer 가 설치된 환경에서 원하는 개발 환경을 구축하기 위해서는 새로운 container 를 먼저 생성해야 한다. 보통은 CLI에서 docker 명령어를 사용하여 container 를 만들지만 많은 argument를 넣어야 하고 설정해주어야 할것도 많기 때문에 쉽지 않다. 이걸 쉽게 하기 위해서 portainer 를 설치했으니 본 포스트에서는 쉽게 docker 컨테이너를 생성하는 방법에 대해 알아보자.

# 1. Image Download

AI/ML 개발에서 가장 많이 사용되는 프레임워크 중 하나인 pytorch 개발환경을 구축해보자.  먼저 pytorch 공식 이미지를 pull 하여 새로운 image 를 다운 받는다. 아래 local의 dashboard에서 images 를 클릭하거나 사이드바에서 images를 클릭하여 Image list가 표시된 페이지에서 pytorch 공식 최신 이미지를 받기 위해 `pytorch/pytorch:latest` 를 입력한다.

그 다음 pull image 버튼을 클릭하면 다운로드가 아래와 같이 진행된다. 용량이 꽤 크기 때문에 (약 7.2GB) 시간이 좀 오래 소요될 수 있다.

![Untitled](/assets/images/post0110/Untitled.png)


다운로드가 완료 되면 새로운 이미지가 아래와 같이 생긴다!

![Untitled](/assets/images/post0110/Untitled 1.png)


# 2. Create Volume

다음으로 pytorch에서 사용할 볼륨을 생성해보자. 컨테이너를 실수로 삭제하거나 장애로 유실된 경우에 데이터를 보존할 수 있으며 bind를 사용하면 공통적으로 사용하는 데이터의 공유가 가능하다. 

1. volumes로 이동하여 add volume을 클릭

    ![Untitled](/assets/images/post0110/Untitled 2.png)

2. Name에는 volume이름을 입력 하고 (LLM 프로파일러 데이터를 저장할 예정이므로 llmprofiler_data로 설정하였음.)  나머지는 그대로 두고 create the volume을 클릭

    ![Untitled](/assets/images/post0110/Untitled 3.png)

3. 아래와 같이 새 볼륨이 생성된 것을 확인할 수 있다.

    ![Untitled](/assets/images/post0110/Untitled 4.png)

# 3. Create Container

대시보드 좌측에서 local에 container 버튼을 클릭한다. 현재 local에 존재하는 container 리스트가 나오고 실행 중인지 exited 인지 상태를 알 수 있다.

1. 우측 상단의 Add container 버튼을 클릭하여  새로운 container 를 생성할 수 있다.

    ![Untitled](/assets/images/post0110/Untitled 5.png)

2. 기본 세팅값 설정
    1. 컨테이너의 이름을 입력
    2. image*에는 아까 다운로드 받은  pytorch/pytorch:latest 입력
    3. port mapping을 위해 Manual network port publishing에서 +publish a new network port 를 클릭하여 포트 설정
    * 외부 서비스를 위해 ssh와 http 서비스가 가능한 22번 포트와 80/443 포트를 매핑
        
        ![Untitled](/assets/images/post0110/Untitled 6.png)


3. Advanced container setting에서는 볼륨과 네트워크 그래픽 가속 장치 등의 설정 수행이 가능하다.
    1. command&logging에서는 docker 컨테이너 접속시 bash 터미널을 띄워줄 수 있게 해주기 위해 interactive & TTY 를 선택한다. (docker에서 -it 옵션)
        
        ![Untitled](/assets/images/post0110/Untitled 7.png)

        
    2. 먼저 앞서 만들어준 볼륨 mapping을 한다. (여기서 Bind를 선택하면 기존 host의 location을 container 의 location에 bind하여 데이터의 공유가 가능하다)
        
        ![Untitled](/assets/images/post0110/Untitled 8.png)

        
    3. 재시작 정책 : always (PC의 재시작 등으로 docker가 다시 시작되었을때 컨테이너도 함께 시작됨)
        
        ![Untitled](/assets/images/post0110/Untitled 9.png)

        
    4. Runtime & Resources : GPU를 설정해준다 All GPU를 사용하도록함.
        
        ![Untitled](/assets/images/post0110/Untitled 10.png)

        
    5. 마지막으로 deploy the container 버튼을 눌러 컨테이너를 생성하면 최종적으로 사용할 준비가 완료된다 👏
        
        ![Untitled](/assets/images/post0110/Untitled 11.png)

        

# Troubleshooting

## A. GPU 인식 및 추가가 안될 경우

Container 생성 시 아래와 같이 GPU 버튼이 동작 되지 않는 경우가 있다. Portainer 에서 GPU 디바이스가 보이도록 아래와 같이 설정해준다!

![Untitled](/assets/images/post0110/Untitled 12.png)


1. GPU UUID check
현재 디바이스의 GPU UUID정보를 얻기 위해 아래 명령어로 gpu디바이스가 존재하는 폴더로 이동한다.

    
    ```bash
    cd /proc/driver/nvidia/gpus/
    ls
    0000:17:00.0  0000:b3:00.0
    
    cd 0000\:17\:00.0/
    cat information
    
    Model:           NVIDIA RTX A6000
    IRQ:             94
    GPU UUID:        GPU-f9ccbb10-ef84-d80d-1e85-4aac94cf4fdf
    Video BIOS:      94.02.5c.00.02
    Bus Type:        PCIe
    DMA Size:        47 bits
    DMA Mask:        0x7fffffffffff
    Bus Location:    0000:17:00.0
    Device Minor:    0
    GPU Excluded:    No
    ```
    

1. 아래와 같이 Portainer 에서 GPU 정보 설정
    1. Host - Setup 으로 이동하여 Docker Feature Configuration 에서 GPU정보를 입력
    2. Show GPU in the UI를 활성화
    3. Add GPU버튼을 클릭하여 GPU 이름과 UUID를 기록
    4. Save configuration을 클릭하여 업데이트
    
    ![Untitled](/assets/images/post0110/Untitled 13.png)

    

1. 다시 containers로 이동하여 runtime & Resources를 클릭하여 GPU로 이동하면 enable GPU 버튼이 동작됨을 확인!
    
    ![Untitled](/assets/images/post0110/Untitled 14.png)

    

## B. Container 생성 이후 start가 안될 때

Container Toolkit이 설치되어야 하는데 Ubuntu23.10에서는 현재 설치가 안됨, 아래 방법으로 설치가 가능하며 docker 에서 실행 가능함을 확인!

1. Configure the production repository:
    
    ```
    curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg\&& curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list |\
        sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' |\
        sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
    ```
    
2. Optionally, configure the repository to use experimental packages:
    
    ```bash
    sudo sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
    ```
    
3. Update the packages list from the repository:
    
    ```bash
    sudo apt-get update
    ```
    
4. 아래 명령어를 통해 nvidia-container-toolkit을 설치
    
    ```bash
    sudo apt-get install -y nvidia-docker2
    ```
    
5. 정상 설치되었는지 테스트
    
    ```bash
    sudo docker run --rm --gpus all nvidia/cuda:11.6.2-base-ubuntu20.04 nvidia-smi
    ```
    
    ![Untitled](/assets/images/post0110/Untitled 15.png)

    

# References

- [https://help.iwinv.kr/manual/read.html?idx=548](https://help.iwinv.kr/manual/read.html?idx=548)
- [https://wandb.ai/wandb_fc/tips/reports/Setting-Up-TensorFlow-And-PyTorch-Using-GPU-On-Docker--VmlldzoxNjU5Mzky](https://wandb.ai/wandb_fc/tips/reports/Setting-Up-TensorFlow-And-PyTorch-Using-GPU-On-Docker--VmlldzoxNjU5Mzky)
- [https://www.sebae.net/videos/how-to-enable-your-gpu-on-portainer-docker/](https://www.sebae.net/videos/how-to-enable-your-gpu-on-portainer-docker/)
- [https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installing-with-apt](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installing-with-apt)
- [https://ahnbk.dev/?p=125](https://ahnbk.dev/?p=125)