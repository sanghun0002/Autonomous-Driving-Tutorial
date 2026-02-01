# Autonomous-Driving-Tutorial
ROS 설치 및 공부 & 자율주행 튜토리얼

 ## ROS에 관해
 ROS 2는 다양한 버전이 있지만, Humble은 LTS(Long Term Support) 버전으로 안정성이 높고, 많은 실무와 연구 환경에서 사용 됨.
 Ubuntu 22.04 (Jammy Jellyfish) 환경에서 설치 해야 함.
 
 ## Requirements
 기본 패키지 설치

### 1. ROS2 apt repository
```
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install curl

sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

### 2. Install ROS 2
```
sudo apt update
sudo apt install ros-humble-desktop
sudo apt install ros-dev-tools
```

### 3. 패키지 설치
```
sudo apt update && sudo apt install -y build-essential cmake git libbullet-dev python3-colcon-common-extensions python3-flake8 python3-pip python3-pytest-cov python3-rosdep python3-setuptools python3-vcstool wget python3-argcomplete

python3 -m pip install -U flake8-blind-except flake8-builtins flake8-class-newline flake8-comprehensions flake8-deprecated flake8-docstrings flake8-import-order flake8-quotes pytest-repeat pytest-rerunfailures pytest

sudo apt install --no-install-recommends -y libasio-dev libtinyxml2-dev libcunit1-dev
```

