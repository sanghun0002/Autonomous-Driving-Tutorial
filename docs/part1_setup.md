# [🏠 메인으로 돌아가기](../README.md)

# Part 1. 개발 환경 구축 (Environment Setup)

자율주행 연구를 위한 가장 기초적인 OS 및 미들웨어 설치 과정입니다.

---

## 1-1. Ubuntu 설치 및 세팅

**목표:** 듀얼 부팅 또는 단독 설치를 완료하여 Linux 환경 구축.

### 📋 주요 내용
* Ubuntu 22.04 LTS 설치 (ROS 2 Humble 호환성 고려)
* 기본 터미널 명령어 익히기 (`ls`, `cd`, `mkdir`, `sudo`, `chmod` 등)
* 그래픽 드라이버(NVIDIA) 설치 및 확인

### ⚙️ 설치 방법

1.  **Ubuntu 22.04 LTS ISO 파일 다운로드**
    * [Ubuntu Releases(Jammy)](https://releases.ubuntu.com/jammy/) 접속 후 Desktop image 다운로드.
2.  **부팅 디스크 제작**
    * **Rufus** 등의 도구를 사용하여 다운로드한 ISO 파일로 부팅 USB 제작.
3.  **Ubuntu 설치**
    * 멀티 부팅(Windows와 함께) 또는 단독으로 설치를 완료합니다.
    * **파티션 용량:** 연구용 데이터(Dataset)와 시뮬레이터 용량을 고려하여 **최소 200GB ~ 300GB** (권장 500GB) 이상 확보.
4.  **그래픽 드라이버 확인**
    * 설치 완료 후 터미널에서 아래 명령어로 GPU 인식 여부 확인.
    ```bash
    nvidia-smi
    ```

---

## 1-2. Python3 & ROS 2 (Humble) 설치

**목표:** Python 3.10과 로봇 미들웨어인 ROS 2 Humble을 설치하고 환경 변수를 설정.

### 📋 주요 내용
* Python 3.10 설치
* Locale 설정 및 Source 추가
* ROS 2 Humble 패키지 설치
* `.bashrc` 환경 변수 설정
* `talker/listener` 데모 테스트

### ⚙️ 설치 방법

#### 1. Python 3.10 설치
```bash
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt install python3.10
```

#### 2. ROS 2 Humble 설치
##### Step 1: 로케일(Locale) 설정
```bash
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```
##### Step 2: 소스 설정 (Repositories)
```bash
# Ubuntu Universe 저장소 활성화
sudo apt install software-properties-common
sudo add-apt-repository universe

# GPG 키 추가 (보안 인증)
sudo apt update && sudo apt install curl -y
sudo curl -sSL [https://raw.githubusercontent.com/ros/rosdistro/master/ros.key](https://raw.githubusercontent.com/ros/rosdistro/master/ros.key) -o /usr/share/keyrings/ros-archive-keyring.gpg

# ROS 2 저장소 목록 추가
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] [http://packages.ros.org/ros2/ubuntu](http://packages.ros.org/ros2/ubuntu) $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```
##### Step 3: ROS 2 패키지 설치 (Desktop 버전)
```bash
sudo apt update && sudo apt install ros-humble-desktop
```
##### Step 4: 환경 변수 설정 (필수) 터미널을 실행할 때마다 ROS 2 명령어를 인식하도록 설정
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
##### Step 5: 설치 확인 (Talker/Listener 테스트)
###### 터미널1
```bash
ros2 run demo_nodes_cpp talker
```
###### 터미널2
```bash
ros2 run demo_nodes_py listener
```
