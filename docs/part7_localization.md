## Part 7. Autoware: Localization (위치 추정)

*센서 데이터가 지도(Map) 위에서 차량의 현재 위치(Pose)로 변환되는 과정입니다.*

목표: 
* 센서에서 어떤 형태로 데이터를 받아서 어떤 노드로 들어가서 처리되는가?
* 최종 위치 추정되는 토픽은 어떤 형태로 나오는가? 

아래 핵심 노드 참조 

<div align="center">
  <img src="picture/localization.PNG" width="600">
  <p>▲ localization 정리 예시 </p>
</div>

### 4-1. NDT Matching (LiDAR 기반 측위)

> 학습 포인트: 로컬 센서 데이터가 전역 지도(Global Map)와 어떻게 매칭되는가?
> 
- **Main Node:** `ndt_scan_matcher`
- **Input Topics:**
    1. `/lidar/concatenated/pointcloud` (현재 LiDAR 스캔)
    2. `/map/pointcloud_map` (PCD 기반 전역 지도)
    3. `/sensing/imu/imu_data` (초기 위치 추정 보정용)
- **Processing:**
    - 현재 스캔된 점구름을 지도 데이터에 정합(Registration)하여 오차를 최소화하는 변환 행렬 계산.
- **Output Topic:** `/localization/pose_estimator/pose_with_covariance`
- **TF Output:** `map` -> `base_link` 좌표 변환 발행

### 4-2. EKF (Extended Kalman Filter)

- **Main Node:** `ekf_localizer`
- **Process:** NDT(느림, 정확) + IMU/Odometry(빠름, 누적오차)를 융합하여 고주파수 Pose 출력.
- **Output:** `/localization/kinematic_state` (최종적으로 Planning이 사용하는 차량 위치)



