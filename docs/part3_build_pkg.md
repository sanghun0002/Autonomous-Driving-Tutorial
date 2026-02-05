# [🏠 메인으로 돌아가기](../README.md)

# Part 3. 빌드 시스템 및 패키지 개발 (Build & Dev)

ROS 2 개발의 핵심인 **패키지 생성, 코드 작성, 빌드(Colcon), 그리고 런치(Launch) 파일 시스템**을 실습합니다.

---

## 3-1. 워크스페이스 및 패키지 생성

**1. 빌드 도구(Colcon) 설치**
ROS 2의 빌드 시스템인 Colcon을 설치합니다.
```bash
sudo apt update
sudo apt install python3-colcon-common-extensions
```

**2. 작업 공간(Workspace) 생성**
소스 코드를 관리할 폴더(`src`)를 포함하여 워크스페이스를 만듭니다.
```bash
# 홈 디렉토리로 이동
cd ~

# ros2_ws 폴더와 그 안에 src(소스) 폴더를 동시에 생성
mkdir -p ~/ros2_ws/src

# 작업 공간으로 이동
cd ~/ros2_ws
```

**3. 패키지 만들기**
`ament_python` 빌드 타입을 사용하여 첫 번째 패키지를 생성합니다.
```bash
# 소스 폴더로 이동
cd ~/ros2_ws/src

# 패키지 생성 명령어
# 문법: ros2 pkg create --build-type [빌드타입] [패키지이름] --dependencies [의존성]
ros2 pkg create --build-type ament_python my_first_pkg --dependencies rclpy std_msgs
```

<div align="center">
  <img src="picture/3-1.png" width="600">
  <p>▲ 패키지 생성된 화면</p>
</div>

---

## 3-2. 노드(Node) 코드 작성

**1. 파이썬 파일 생성**
```bash
# 패키지 내부 코드 폴더로 이동
cd ~/ros2_ws/src/my_first_pkg/my_first_pkg

# 파일 생성 및 편집
gedit simple_node.py
```

**2. 코드 작성 (`simple_node.py`)**
아래 코드를 복사해서 붙여넣고 저장하세요.
```python
import rclpy
from rclpy.node import Node

class SimpleNode(Node):
    def __init__(self):
        super().__init__('hello_node') # 노드 이름 설정
        self.create_timer(1.0, self.timer_callback) 

    def timer_callback(self):
        self.get_logger().info('Hello, ROS 2 Humble!') # 터미널에 로그 출력

def main(args=None):
    rclpy.init(args=args)
    node = SimpleNode()
    rclpy.spin(node) # 노드가 죽지 않고 계속 실행되게 함
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

**3. 진입점(Entry Point) 설정**
`setup.py` 파일을 수정하여 실행 가능한 명령어로 등록합니다.

```bash
cd ~/ros2_ws/src/my_first_pkg
gedit setup.py
```

`console_scripts` 부분을 찾아 아래와 같이 수정합니다.
```python
    entry_points={
        'console_scripts': [
            'my_node = my_first_pkg.simple_node:main',
        ],
    },
```

---

## 3-3. 빌드 및 실행

작성한 코드를 시스템이 인식할 수 있도록 빌드합니다.

```bash
# 1. 워크스페이스 루트(최상위)로 이동 (빌드는 항상 여기서!)
cd ~/ros2_ws

# 2. 빌드 실행 (--symlink-install은 파이썬 코드를 수정해도 재빌드 안 하게 해주는 옵션)
colcon build --symlink-install

# 3. 환경 설정 반영 (빌드된 내용을 터미널이 알게 함)
source install/local_setup.bash

# 4. 실행
ros2 run my_first_pkg my_node
```

<div align="center">
  <img src="picture/3-2.png" width="600">
  <p>▲ 빌드 후 결과 출력 화면</p>
</div>


---

## 3-4. Launch 파일 제작

여러 노드를 한 번에 실행하거나 설정을 관리하기 위해 Launch 파일을 작성합니다.

**1. Launch 폴더 생성**
```bash
cd ~/ros2_ws/src/my_first_pkg
mkdir launch
```

**2. Launch 파일 작성 (`my_robot.launch.py`)**
```bash
gedit launch/my_robot.launch.py
```

아래 코드를 붙여넣으세요. (동일한 노드를 이름만 바꿔서 2개 실행하는 예제)
```python
import os
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        # 첫 번째 노드: 원래 이름으로 실행
        Node(
            package='my_first_pkg',
            executable='my_node',
            name='node_1',
            output='screen'
        ),
        
        # 두 번째 노드: 이름을 바꿔서 실행
        Node(
            package='my_first_pkg',
            executable='my_node',
            name='node_2', # 노드 이름 변경
            output='screen'
        )
    ])
```

**3. `setup.py` 설정 (중요)**
런치 파일도 빌드 시 설치되도록 `setup.py`를 다시 수정해야 합니다.

```bash
cd ~/ros2_ws/src/my_first_pkg
gedit setup.py
```

상단에 `import` 문을 추가하고, `data_files` 리스트에 한 줄을 추가합니다.

```python
from setuptools import setup
import os               # <--- 추가 1
from glob import glob   # <--- 추가 2

package_name = 'my_first_pkg'

setup(
    name=package_name,
    version='0.0.0',
    packages=[package_name],
    data_files=[
        ('share/ament_index/resource_index/packages', ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        # ▼▼▼ 아래 줄을 정확하게 추가해주세요! ▼▼▼
        ('share/' + package_name + '/launch', glob(os.path.join('launch', '*.launch.py'))),
    ],
    # ... (나머지 코드는 그대로)
```

**4. 재빌드 및 Launch 실행**
설정 파일이 변경되었으므로 다시 빌드합니다.

```bash
# 1. 워크스페이스로 이동
cd ~/ros2_ws

# 2. 재빌드
colcon build --symlink-install

# 3. 환경 설정
source install/local_setup.bash

# 4. 런치 파일 실행 (ros2 launch 명령어 사용)
ros2 launch my_first_pkg my_robot.launch.py
```

---

## 3-5. 디버깅 및 시각화 툴 (참고)

* **RQT Graph:** 현재 실행 중인 노드들의 연결 관계를 그림으로 확인합니다.
    ```bash
    rqt_graph
    ```
* **RViz2:** 센서 데이터(LiDAR, Camera) 및 TF(좌표계)를 3D로 시각화합니다.
    ```bash
    rviz2
    ```

