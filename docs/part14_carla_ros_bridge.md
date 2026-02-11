# carla_ros_bridge

Carla에서 수집한 정보를 바탕으로 ROS2 topic 형태로 데이터를 변환해주는 브릿지 노드임.

Carla 시뮬레이터  &amp; ROS2 연동

 ## Requirements
 기본 패키지 설치

### 1. python 3.10 version
```
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt install python3.10
```

### 2. ros2-humble
```
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

sudo apt install software-properties-common
sudo add-apt-repository universe

sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

sudo apt update && sudo apt install ros-humble-desktop
```

### 3. cyclonedds
```
sudo apt update
sudo apt install ros-humble-cyclonedds
```

 ## Installation

### 1. Carla_simulator installation (Version: 0.9.15)
아래 링크로 접속하여 Carla0.9.15.tar.gz & carla-0.9.15-cp310-cp310-linux_x86_64.whl 파일 다운 받기
https://o365knuackr.sharepoint.com/:f:/s/iSPL997/EmRHz6dycq9BjcE01ZCYmIwBxY-9NZwlMX0jTYXG8Ibqrg?e=KBgivQ

```
mkdir -p ~/Workspace/Carla
cd ~/Downloads
tar -xvzf CARLA_0.9.15.tar.gz -C ~/Workspace/Carla

mv ~/Downloads/carla-0.9.15-cp310-cp310-linux_x86_64.whl ~/Workspace/Carla/PythonAPI/carla/dist
cd ~/Workspace/Carla/PythonAPI/carla/dist
python3 -m pip install carla-0.9.15-cp310-cp310-linux_x86_64.whl

cd ~/Workspace/Carla/PythonAPI/examples
python3 -m pip install -r requirements.txt

```

- numpy 버전 오류시
```
cd ~/Workspace/Carla/PythonAPI/examples
nano requirements.txt

cd ~/Workspace/Carla/PythonAPI/examples
python3 -m pip install -r requirements.txt

```
"numpy==1.18.4 -> numpy" 로 수정 

### 2. Carla_ros_bridge installation (ros2-humble)

```
mkdir -p ~/Workspace/Carla_ros_bridge/src
cd ~/Workspace/Carla_ros_bridge/src
git clone https://github.com/KNU-iSPL/carla_ros_bridge.git
cd ..
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```

 ## Start launch command
 총 3개의 터미널 창 준비
### Carla simulator
Carla 시뮬레이터 실행 명령어
```
#첫번째 터미널 창
cd ~/Workspace/Carla/
./CarlaUE4.sh
```
| Carla simulator | 
| :---: |
| ![설명1](./docs/images/carla.png) |

### knu 맵 파일 저장하는 방법
```
cd ~/Workspace/Carla/CarlaUE4/Content/Carla/Maps
#KNU ZIP 파일을 해당 위치에 압축 해제한다.
```
Carla_ros_bridge/src/carla-ros-bridge/carla_ros_bridge/launch/carla_ros_bridge_with_example_ego_vehicle.launch.py 에 해당 하는 파일을 수정한다.
```
def generate_launch_description():
    ld = launch.LaunchDescription([
        launch.actions.DeclareLaunchArgument(
            name='host',
            default_value='localhost'
        ),
        launch.actions.DeclareLaunchArgument(
            name='port',
            default_value='2000'
        ),
        launch.actions.DeclareLaunchArgument(
            name='timeout',
            default_value='30'
        ),
        launch.actions.DeclareLaunchArgument(
            name='role_name',
            default_value='hero'
        ),
        launch.actions.DeclareLaunchArgument(
            name='vehicle_filter',
            default_value='vehicle.*'
        ),
        launch.actions.DeclareLaunchArgument(
            name='spawn_point',
            default_value='None'
        ),
# ---------------------------------------------------
# 아래 default_value='knu1' 으로 변경한다.
        launch.actions.DeclareLaunchArgument(
            name='town',
            default_value='knu1'
        ),
# ---------------------------------------------------
```

### Carla_ros_bridge

Carla_ros_bridge - ego_vehicle - controller command

(ego_vehicle 소환 및 컨트롤러 창 실행)
```
#두번째 터미널 창
cd ~/Workspace/Carla_ros_bridge
source install/setup.bash 
ros2 launch carla_ros_bridge carla_ros_bridge_with_example_ego_vehicle.launch.py 

```
Carla_ros_bridge - ego_vehicle - ackermann_control command

(목표 값, 가속도 값을 패달값으로 변환시켜주는 노드 실행)
```
#세번째 터미널 창
cd ~/Workspace/Carla_ros_bridge
source install/setup.bash 
ros2 launch carla_ackermann_control carla_ackermann_control.launch.py 

```


