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

 ## Installation

### citac_avante_sim installation

```
mkdir -p ~/Workspace/citac_sim_ws/src
cd ~/Workspace/citac_sim_ws/src
git clone --recursive https://github.com/KNU-iSPL/citac_avante_sim.git
sudo apt install -y ros-humble-can-msgs \         
                    ros-humble-socketcan-interface \
                    ros-humble-socketcan-bridge \
                    can-utils
source /opt/ros/humble/setup.bash
cd ..
colcon build --symlink-install --packages-select citac_avante_ros2_socketcan
sudo apt update                            
sudo apt install -y \
  ros-humble-pcl-ros \
  ros-humble-pcl-conversions \
  libpcap-dev
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
source install/setup.bash
```

 ## Start launch command
 
### Map Generation Commands
맵 생성 명령어
```
cd ~/Workspace/citac_sim_ws
source install/setup.bash
cd ~/Workspace/citac_sim_ws/src/citac_avante_sim/citac_common/config/map/Carla
ros2 run citac_planning sim_path_logger.py . map_path
```
| Carla controller | map_genration launch |
| :---: | :---: |
| ![설명1](./docs/images/map_generation1.png) | ![설명2](/docs/images/map_generation2.png) |

맵 생성하고 싶은 경로를 수동으로 주행 후 ctrl+C 로 종료 -> 자동 맵 저장
### Citac_avante_autonomous command

| Citac_avante_autonomous launch |
| :---: |
| ![설명1](./docs/images/citac_avante_autonomous.png) |

시뮬레이터 연동 런치 실행 명령어
```
cd ~/Workspace/citac_sim_ws
source install/setup.bash 
ros2 launch citac_common sim_citac_autonomous.launch.py
```


