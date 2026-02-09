## Part 7. Autoware: Control (차량 제어)

*Planning에서 만든 가상의 궤적(Trajectory)을 실제 차량이 따라가도록 악셀, 브레이크, 핸들을 조작하는 과정입니다.*

목표:

* 계획된 궤적과 현재 차량 위치 사이의 오차를 계산하고 보정하는 원리를 이해합니다.

* 고도화된 제어 알고리즘인 **MPC(Model Predictive Control)**의 역할을 파악합니다.

* ROS 메시지가 실제 차량 하드웨어 신호(CAN)로 변환되는 인터페이스 구조를 이해합니다.

아래 중요 노드 참조
### 7-1. Trajectory Follower (경로 추종)

> 학습 포인트: 계획된 선을 벗어나지 않고 정확하게 따라가기 위한 알고리즘(PID, MPC)의 이해.
> 
- **Main Node:** `trajectory_follower_node` (주로 MPC: Model Predictive Control 사용)
- **Input Topics:**
    1. `/planning/scenario_planning/trajectory` (따라가야 할 목표 궤적)
    2. `/localization/kinematic_state` (현재 차량의 속도 및 위치)
- **Processing:**
    - **Lateral Control (조향):** 차량이 궤적 중심선에서 얼마나 벗어났는지(Lateral Error), 각도가 얼마나 틀어졌는지(Heading Error) 계산하여 타이어 조향각 산출.
    - **Longitudinal Control (속도):** 목표 속도와 현재 속도의 차이를 줄이기 위한 가속도 산출.
    - **MPC:** 가까운 미래의 차량 거동을 예측하여 최적의 제어값 계산 (연산량 높음, 정확도 높음).
- **Output Topic:** `/control/command/control_cmd`
    - 내용: `steering_angle` (조향각), `speed` (속도), `acceleration` (가속도) 등.

### 7-2. Vehicle Interface (차량 통신)

> 학습 포인트: 소프트웨어 명령어(ROS Message)를 실제 자동차 하드웨어(CAN 통신)로 변환하는 과정.
> 
- **Main Node:** `vehicle_cmd_gate`, `vehicle_interface`
- **Input Topics:**
    1. `/control/command/control_cmd` (자율주행 제어 명령)
    2. `/system/emergency/stop` (비상 정지 신호)
- **Processing:**
    - **Gatekeeper:** 비상 상황 발생 시 제어 명령 차단 및 급정거 수행.
    - **Command Conversion:** ROS 메시지를 차량 제조사별 CAN 프로토콜(dbw_cmd)로 변환.
- **Output:**
    - **To Hardware:** CAN Bus Signal (Throttle, Brake, Steer motor)
    - **To ROS (Feedback):** `/vehicle/status/velocity`, `/vehicle/status/steering_status` (실제 차량의 현재 상태 피드백)

