Autoware 설치 가이드
Autoware는 자율주행을 위한 오픈소스 소프트웨어 스택입니다. 이 가이드는 Ubuntu 22.04와 ROS 2 Humble 환경을 기준으로 작성되었습니다.

1. 필수 요구 사양
설치를 시작하기 전에 시스템이 다음 요구 사항을 충족하는지 확인하세요.

OS: Ubuntu 22.04

CPU: Intel Core i7 이상 (8코어 권장)

GPU: NVIDIA GPU (CUDA 지원 필수)

RAM: 16GB 이상 (32GB 권장)

Storage: 50GB 이상의 여유 공간

2. 사전 준비 (Prerequisites)
먼저 시스템 패키지를 업데이트하고 필요한 도구들을 설치합니다.

Bash
sudo apt update && sudo apt upgrade -y
sudo apt install git python3-pip -y
NVIDIA 드라이버 및 CUDA 설치
Autoware의 인지 알고리즘은 GPU를 활용하므로 NVIDIA 드라이버와 CUDA Toolkit이 필요합니다.

3. Autoware 저장소 복제
공식 Autoware Foundation의 저장소를 로컬 환경에 가져옵니다.

Bash
git clone https://github.com/autowarefoundation/autoware.git
cd autoware
4. 의존성 설치 (Ansible 사용)
Autoware는 시스템 설정을 자동화하기 위해 Ansible 스크립트를 제공합니다. 이 과정에서 ROS 2, CUDA, 관련 라이브러리가 모두 설치됩니다.

Bash
# 설정 스크립트 실행
./setup-dev-env.sh
참고: 설치 중 재부팅이 필요할 수 있습니다. 스크립트 안내에 따라 진행하세요.

5. 작업 공간 구성 및 빌드
이제 필요한 소스 코드를 다운로드하고 빌드를 진행합니다.

저장소 체크아웃
Bash
mkdir src
vcs import src < autoware.repos
의존성 확인 (rosdep)
Bash
source /opt/ros/humble/setup.bash
rosdep update
rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO
빌드 실행
빌드 과정은 시스템 성능에 따라 30분에서 1시간 이상 소요될 수 있습니다.

Bash
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
6. 실행 테스트
빌드가 완료되면 환경 변수를 설정하고 샘플 지도를 사용하여 실행 여부를 확인합니다.

Bash
source install/setup.bash
ros2 launch autoware_launch planning_simulator.launch.xml map_path:=$HOME/autoware_map/sample-map-planning vehicle_model:=sample_vehicle sensor_model:=sample_sensor_kit
