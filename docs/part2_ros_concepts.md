# [🏠 메인으로 돌아가기](../README.md)

# Part 2. ROS 2 핵심 개념 (Core Concepts)

Autoware를 분석하기 위해 필수적으로 알아야 할 ROS 2의 핵심 기능인 **노드(Node)**와 **3대 통신 방식(Topic, Service, Action)**을 실습합니다.

---

## 2-1. 노드(Node)와 통신

* **개념:** 실행의 최소 단위(Node)와 노드 간의 데이터 송수신 방법.
* **실습 도구:** Turtlesim (ROS 2 기본 학습용 거북이 시뮬레이터)

### 0. 실습 준비 (Turtlesim 설치)
먼저 실습에 필요한 패키지를 설치합니다.

```bash
sudo apt update
sudo apt install ros-humble-turtlesim
```
### 1. 노드 (Node) 구조 이해
노드를 실행하고, 시스템에서 어떻게 인식되는지 확인합니다.

**[터미널 1] 시뮬레이터 노드 실행**
파란색 화면의 거북이 시뮬레이터가 켜집니다.

```bash
ros2 run turtlesim turtlesim_node
```

**[터미널 2] 실행 중인 노드 목록 확인**
현재 `turtlesim` 노드가 실행 중인지 리스트를 조회합니다.

```bash
ros2 node list
```

### 2. Topic (비동기 단방향 통신)
Publisher(송신)와 Subscriber(수신) 구조를 이해합니다.

**[터미널 2] 키보드 제어 노드 (Publisher)**
이 터미널이 활성화된 상태에서 키보드 방향키를 누르면 거북이가 움직입니다.

```bash
ros2 run turtlesim turtle_teleop_key
```

**[터미널 3] 토픽 데이터 확인 (Subscriber)**
실제로 어떤 데이터(`linear`, `angular` 속도)가 오가는지 눈으로 확인합니다.

```bash
# 현재 활성화된 토픽 목록 확인
ros2 topic list

# 특정 토픽(/turtle1/cmd_vel)의 데이터 도청(Echo)
ros2 topic echo /turtle1/cmd_vel
```

### 3. Service (동기 양방향 통신)
요청(Request)과 응답(Response) 구조를 이해합니다. 거북이를 새로 소환하는 서비스(`spawn`)를 호출해 봅니다.

**[터미널 3] 서비스 요청 보내기**

* **문법:** `ros2 service call [서비스명] [타입] [데이터]`
* **실습:** x=5.0, y=5.0 위치에 'my_turtle'이라는 이름으로 새로운 거북이 소환.

```bash
ros2 service call /spawn turtlesim/srv/Spawn "{x: 5.0, y: 5.0, theta: 0.0, name: 'my_turtle'}"
```

> **Tip:** `x`나 `y` 좌표를 다른 값으로 변경해서 테스트해 보세요.

### 4. Action (비동기 양방향 통신 + Feedback)
긴 작업을 처리하며 중간 상황(Feedback)을 보고받는 구조를 이해합니다.

**[터미널 3] 액션 명령 보내기**

* **문법:** `ros2 action send_goal [액션명] [타입] [데이터]`
* **실습:** 거북이를 1.57라디안(90도)까지 회전시키고, 회전하는 동안의 각도 변화를 피드백으로 받음.

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}" --feedback
```

> **Tip:** `--feedback` 옵션을 빼면 중간 과정 없이 결과만 출력됩니다. 차이를 비교해 보세요.
