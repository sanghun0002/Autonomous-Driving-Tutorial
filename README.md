# 📘 신입생 온보딩 매뉴얼: 자율주행 기초 (Draft)

본 매뉴얼은 자율주행 연구를 위한 기초적인 개발 환경 구축부터 ROS 2 핵심 개념(통신, 빌드, 좌표계) 및 기초 논문 학습을 위해 작성되었습니다.

---

## 📚 목차 (Table of Contents)

1. [Part 1. 개발 환경 구축 (Environment Setup)](#part-1-개발-환경-구축-environment-setup)
2. [Part 2. ROS 2 핵심 개념 및 실습](#part-2-ros-2-핵심-개념)
3. [Part 2.3. 좌표계 변환 (TF2)](#part-23-좌표계-변환-tf2)
4. [Part 2.5. 자율주행 관련 논문 리뷰](#part-25-자율-주행-관련-논문-리뷰)

---

## Part 1. 개발 환경 구축 (Environment Setup)

자율주행 연구를 위한 가장 기초적인 OS 및 미들웨어 설치 과정입니다.

### 1-1. Ubuntu 설치 및 세팅

* **목표:** Ubuntu 22.04 LTS 설치 (ROS 2 Humble 호환성 고려)
* **권장 사양:** 멀티 부팅 시 최소 **200GB ~ 300GB** (500GB 권장) 확보
* **설치 방법:**
    1.  [Ubuntu 22.04 LTS 다운로드](https://releases.ubuntu.com/jammy/)
    2.  **Rufus** 등을 이용하여 부팅 디스크 제작
    3.  BIOS 설정 후 설치 진행 (Dual Boot 또는 단독 설치)
* **필수 확인:**
    * 기본 터미널 명령어 숙지 (`ls`, `cd`, `mkdir`, `sudo`, `chmod`)
    * NVIDIA 그래픽 드라이버 설치 확인 (`nvidia-smi`)

### 1-2. Python3 & ROS 2 (Humble) 설치

Python 3.10 환경과 ROS 2 Humble 미들웨어를 설치합니다.

**Python 3.10 설치**
```bash
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt install python3.10
