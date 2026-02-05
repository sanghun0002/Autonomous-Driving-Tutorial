# [🏠 메인으로 돌아가기](../README.md)

# Part 4. 좌표계 변환 (TF2)

로봇 공학, 특히 자율주행에서 가장 중요한 **좌표 변환 트리(Coordinate Transform Tree)**를 이해하고, ROS 2의 TF2 라이브러리를 통해 시각화하는 실습을 진행합니다.

---

## 4-1. 핵심 개념 이해

### 1. 표준 좌표계 계층 구조 (REP-105)
로봇 시스템은 부모(Parent)와 자식(Child) 관계의 좌표계들로 연결되어 있습니다.

* **`map` (World Frame)**
    * 절대 변하지 않는 전역 좌표계입니다. (예: GPS 기준점, 건물 도면)
    * 경로 계획(Navigation)의 기준이 됩니다.
* **`odom` (Odometry Frame)**
    * 로봇의 시작점을 기준으로 하는 지역 좌표계입니다.
    * 시간이 지나면 오차가 발생하지만, 연속성은 보장됩니다.
* **`base_link` (Robot Body Frame)**
    * 로봇의 몸체 중심(보통 뒷바퀴 축)입니다.
    * `odom` 위에서 움직입니다.
* **`sensor_frame` (LiDAR, Camera 등)**
    * 로봇 몸체(`base_link`)에 부착된 센서의 위치입니다.

### 2. Static TF vs Dynamic TF

| 구분 | Static TF (정적 변환) | Dynamic TF (동적 변환) |
| :--- | :--- | :--- |
| **의미** | 위치 관계가 고정됨 | 위치 관계가 계속 변함 |
| **예시** | 차체(`base_link`)와 지붕 위 LiDAR | 지도(`map`)와 주행 중인 차(`base_link`) |
| **특징** | 한 번만 방송하면 됨 | 지속적으로(High Frequency) 방송해야 함 |

---

## 4-2. 실습: 가상 센서 부착 및 시각화

가상의 거북이 로봇(`turtle1`)에 LiDAR 센서(`lidar_link`)를 부착하고, 좌표계가 어떻게 따라다니는지 확인합니다.

### Step 1. TF 관련 툴 설치
```bash
sudo apt update
sudo apt install ros-humble-tf2-tools ros-humble-tf-transformations
```

### Step 2. 가상 로봇 실행 (Dynamic TF 생성)
거북이가 움직이면 `world` 좌표계 대비 `turtle1` 좌표계가 실시간으로 변합니다.

**[터미널 1] 거북이 시뮬레이터 실행**
```bash
ros2 run turtlesim turtlesim_node
```

**[터미널 2] 키보드 조종 실행**
(거북이를 움직여보세요)
```bash
ros2 run turtlesim turtle_teleop_key
```

### Step 3. 가상 센서 부착 (Static TF 발행)
거북이 등 위에 1m 높이로 센서가 달려있다고 가정하고, 고정된 관계를 방송합니다.

**[터미널 3] Static TF 발행**
* **설정:** `turtle1`(부모) → `lidar_link`(자식) / Z축으로 1m 이동
* **명령어:**
```bash
# 문법: ros2 run tf2_ros static_transform_publisher --x [x] --y [y] --z [z] --yaw [yaw] --pitch [pitch] --roll [roll] --frame-id [부모] --child-frame-id [자식]

ros2 run tf2_ros static_transform_publisher --x 0 --y 0 --z 1 --yaw 0 --pitch 0 --roll 0 --frame-id turtle1 --child-frame-id lidar_link
```
> **Note:** 이 터미널은 끄지 말고 켜두어야 TF가 유지됩니다.

### Step 4. TF 트리 확인 (View Frames)
현재 연결된 좌표계들의 족보(Tree)를 PDF 파일로 그려서 확인합니다.

**[터미널 4] TF 트리 PDF 생성**
```bash
# 5초 동안 데이터를 수집해서 frames.pdf 파일을 생성함
ros2 run tf2_tools view_frames
```
* 생성된 `frames.pdf` 파일을 열어보면 `world` -> `turtle1` -> `lidar_link` 구조를 볼 수 있습니다.

### Step 5. Rviz2에서 3D로 확인

**[터미널 4] Rviz2 실행**
```bash
ros2 run rviz2 rviz2
```

**Rviz 설정 방법:**
1.  좌측 하단 **[Add]** 버튼 클릭
2.  **[TF]** 선택 후 OK
3.  좌측 상단 **Fixed Frame**을 `world`로 변경
4.  키보드([터미널 2])로 거북이를 움직이면, `turtle1`과 `lidar_link` 축이 함께 움직이는 것을 볼 수 있습니다.
