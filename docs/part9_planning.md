## Part 9. Autoware: Planning (판단 및 경로 생성)

*Perception에서 받은 주변 정보와 Localization의 내 위치 정보를 바탕으로, 목적지까지 어떻게 갈지 결정하고 구체적인 경로(Trajectory)를 그리는 과정입니다.*

목표: 
* 다양한 센서 데이터가 어떤 인지 노드를 거쳐 객체화되는지 이해합니다.
* LiDAR와 Camera 데이터가 결합되어 신뢰도를 높이는 과정을 파악합니다.
* 인식된 객체에 ID가 부여되어 추적되는 최종 출력 형태를 확인합니다.

아래 핵심 노드 참조
### 6-1. Mission Planning (전역 경로 탐색)

> 학습 포인트: 네비게이션처럼 목적지까지의 도로 레벨 경로를 어떻게 찾는가?
> 
- **Main Node:** `mission_planner`
- **Input Topics:**
    1. `/initialpose` (현재 위치)
    2. `/planning/mission_planning/goal` (목표 위치, RViz에서 2D Nav Goal로 찍는 지점)
    3. `/map/vector_map` (Lanelet2 고정밀 지도 정보)
- **Processing:**
    - Lanelet2 지도의 토폴로지(도로 연결 관계)를 그래프로 변환.
    - 현재 위치에서 목표 지점까지의 최적 경로 탐색 (주로 A* 알고리즘 등 사용).
- **Output Topic:** `/planning/mission_planning/route` (차선 단위의 경로 리스트)

### 6-2. Behavior Planning (주행 행동 결정)

> 학습 포인트: 차선 변경, 신호등 준수, 정지선 정지 등 교통 법규와 상황에 따른 행동을 어떻게 결정하는가?
> 
- **Main Nodes:** `behavior_path_planner`, `behavior_velocity_planner`
- **Input Topics:**
    1. `/planning/mission_planning/route` (전역 경로)
    2. `/perception/object_recognition/objects` (주변 장애물 정보)
    3. `/perception/traffic_light_recognition/traffic_signals` (신호등 상태)
- **Processing:**
    - **Scene Module:** Lane Change, Intersection, Crosswalk 등 상황별 모듈 활성화.
    - **Stop Point Generation:** 신호등 빨간불이나 횡단보도 앞에 정지해야 할 경우 경로상에 속도 0인 지점 생성.
- **Output Topic:** `/planning/scenario_planning/lane_driving/behavior_planning/path` (행동 정보가 포함된 경로)

### 6-3. Motion Planning (지역 경로 및 궤적 생성)

> 학습 포인트: 장애물을 회피하고 승차감을 고려한 부드러운 곡선(Trajectory)을 어떻게 만드는가?
> 
- **Main Node:** `obstacle_avoidance_planner` (또는 `motion_velocity_smoother`)
- **Input Topics:**
    1. Behavior Path (이전 단계의 경로)
    2. `/perception/.../objects` (동적/정적 장애물)
- **Processing:**
    - **Trajectory Generation:** 시간(time), 속도(velocity), 위치(pose)가 포함된 점들의 집합 생성.
    - **Collision Check:** 생성된 궤적이 장애물과 겹치는지 확인.
    - **Smoothing:** 급격한 핸들링이나 가감속을 방지하기 위한 곡선 평활화 (Jerk 최소화).
- **Output Topic:** `/planning/scenario_planning/trajectory`
    - *이 토픽이 Control 파트로 넘어가는 최종 명령서입니다.*


