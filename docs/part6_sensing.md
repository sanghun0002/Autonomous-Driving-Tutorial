# [🏠 메인으로 돌아가기](../README.md)

# Part 6. Autoware: Sensing (전처리 과정)

자율주행의 시작은 센서 데이터를 받아오는 것입니다. LiDAR와 Camera로부터 들어오는 Raw Data가 어떻게 병합되고, 연산 효율을 위해 어떻게 압축(Downsampling)되는지 실습합니다.

---

## 6-1. 핵심 개념 이해

### 1. LiDAR Sensing Pipeline
LiDAR 센서는 초당 수만~수십만 개의 점(Point) 데이터를 쏟아냅니다. 이를 그대로 쓰면 컴퓨터가 과부하 되므로 전처리가 필수입니다.

* **Raw Data Input (`driver`)**
  * 하드웨어(UDP 패킷) 신호를 ROS 메시지(`PointCloud2`)로 변환합니다.
* **Crop Box Filter**
  * 차량 본체(`Ego Vehicle`)에 부딪혀서 반사된 불필요한 점들을 제거합니다. (내 차 엉덩이나 지붕이 장애물로 인식되면 안 되니까요!)
* **Voxel Grid Filter**
  * 빽빽한 점구름을 격자(Voxel) 단위로 나누고, 격자 하나당 점 하나만 남겨 데이터 양을 줄입니다. (예: 0.1m 간격)

### 2. Camera Pipeline
* **Calibration**
  * 카메라 렌즈의 왜곡을 펴고, 3차원 공간상의 위치를 알기 위해 내부(Intrinsic)/외부(Extrinsic) 파라미터를 적용합니다.

---

## 6-2. 실습: 데이터 흐름 확인

가상의 데이터(rosbag)를 재생하거나 시뮬레이터를 켜고, 전처리 전/후의 데이터 양을 비교해봅니다.

### Step 1. 토픽 리스트 확인
LiDAR 드라이버와 전처리 노드가 켜져 있다면 아래 토픽들이 보여야 합니다.

**[터미널 1] 토픽 조회**
```bash
ros2 topic list | grep lidar
```

* `/lidar/points_raw`: 센서에서 바로 나온 날 것의 데이터
* `/lidar/concatenated/pointcloud`: 전처리가 끝난 데이터 (주로 이 데이터를 씀)

### Step 2. 데이터 Hz 및 대역폭 확인
전처리 전후의 데이터 무거움 정도를 `bw`(bandwidth) 명령어로 비교해봅니다.

**[터미널 2] Raw 데이터 크기 확인**
```bash
ros2 topic bw /lidar/points_raw
```

> **Note:** 평균 20~30MB/s가 나올 수 있습니다.

**[터미널 3] 전처리 후 데이터 크기 확인**
```bash
ros2 topic bw /lidar/concatenated/pointcloud
```

**2. RViz2 시각화 실행 (터미널 4)**

### Step 3. RViz2 시각화
**[터미널 4] Rviz2 실행**
```bash
ros2 run rviz2 rviz2
```

**3. RViz 설정 가이드**

**Rviz 설정:**
1. **Global Options** -> Fixed Frame을 `map` 또는 `base_link`로 설정.
2. **Add** -> `PointCloud2` 추가 -> Topic을 `/lidar/concatenated/pointcloud`로 선택.
3. 차량 주변의 점구름이 선명하게 보이는지 확인합니다.
