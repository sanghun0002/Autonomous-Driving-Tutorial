# 📘 신입생 온보딩 매뉴얼: 자율주행 기초

* 본 매뉴얼은 자율주행 연구를 위한 기초적인 개발 환경 구축부터 ROS 2의 핵심 개념 및 기초 논문 학습을 위해 작성되었습니다.

* 아래 목차를 클릭하면 해당 학습 자료로 이동합니다.

---

## 📚 목차 (Table of Contents)

## [Phase 1. 개발환경 & ROS2]

### [📂 Part 1. 개발 환경 구축 (Environment Setup)](./docs/part1_setup.md)
* Ubuntu 22.04 LTS 설치 및 세팅
* Python 3.10 & ROS 2 Humble 설치

### [📂 Part 2. ROS 2 핵심 개념 (Core Concepts)](./docs/part2_ros_concepts.md)
* 노드(Node)와 통신 (Topic, Service, Action)
* Turtlesim 실습 가이드

### [📂 Part 3. 빌드 시스템 및 패키지 개발 (Build & Dev)](./docs/part3_build_pkg.md)
* 워크스페이스 생성 및 Colcon 빌드
* 나만의 패키지 및 런치 파일 작성

### [📂 Part 4. 좌표계 변환 (TF2)](./docs/part4_tf2.md)
* TF Tree 이해 (Map, Odom, Base_link)
* Static TF와 Dynamic TF 실습
  
---
> 💡 **Note: ROS에 대한 기초 지식이 충분한 학생은 아래 단계부터 진행하시기 바랍니다.**
---

### [📂 Part 5. 자율주행 관련 논문 리뷰](./docs/part5_papers_review.md)
* 자율주행 시스템 및 최신 기술 동향 Survey 논문

---

## [Phase 2. Autoware 아키텍처 심화 (Sensing ~ Control)]
* 해당 파트 과정은 절대 모든 노드를 외우는 데 목적을 두는 것이 아님.
* 자율주행이 어떻게 동작하는지에 대한 흐름을 이해하는 것으로 충분함.
*  https://autowarefoundation.github.io/autoware_universe/main/

### [📂 Autoware: 다운로드 (설치 가이드)](./docs/autoware_download.md)
* Autoware 다운로드 및 동작 가이드.

### [📂 Part 6. Autoware: Sensing (전처리)](./docs/part6_sensing.md)
* LiDAR/Camera 드라이버 구동 및 PointCloud 노이즈 제거/다운샘플링(Voxel Grid) 파이프라인.

### [📂 Part 7. Autoware: Localization (위치 추정)](./docs/part7_localization.md)
* NDT Matching(Lidar+Map)과 EKF를 융합하여 차량의 정밀 위치(Pose)를 실시간 추정.

### [📂 Part 8. Autoware: Perception (인지)](./docs/part8_perception.md)
* Lidar-CenterPoint 및 Multi-modal Sensor Fusion을 활용한 객체 인식(Detection)과 추적(Tracking).

### [📂 Part 9. Autoware: Planning (판단 및 경로 생성)](./docs/part9_planning.md)
* 전역 경로(Mission), 주행 행동 결정(Behavior), 장애물 회피 궤적(Motion)을 생성하는 계층적 계획 수립.

### [📂 Part 10. Autoware: Control (제어)](./docs/part10_control.md)
* 생성된 궤적을 오차 없이 추종하는 알고리즘(MPC) 및 ROS 명령을 CAN 신호로 변환하는 차량 인터페이스.

### [📂 Part 11. 시스템 통합 및 디버깅 (System Integration)](./docs/part11_integration.md)
* RQT Graph를 통한 전체 노드 연결 상태 점검 및 CARLA 시뮬레이터 연동 루프 검증.

---

## [Phase 3. Carla 시뮬레이터]
* 시뮬레이터 내에서 차량의 자율주행 동작을 확인하고 이해하는것을 목표로 함.
* 자율 주행중에 데이터가 수집되는 과정을 실시간으로 확인하고 phase 2에서 학습한 내용을 시뮬레이터 환경에서 복습하는 과정임.

### [📂 Part 12. Carla simulator 설치](./docs/part12_install_carla.md)
* Carla 시뮬레이터 설치 과정 익히기.

### [📂 Part 13. Carla pythonAPI 사용](./docs/part13_carla_pythonAPI.md)
* PythonAPI 설정 및 예제 코드 사용법 익히기.

### [📂 Part 14. Carla_ros_bridge 연동 ](./docs/part14_carla_ros_bridge.md)
* Carla와 carla_ros_bridge를 연동하여 시뮬레이터 내의 정보를 ros_topic으로 확인하기.
