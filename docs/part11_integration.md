## Part 10. 시스템 전체 통합 및 디버깅 (System Integration)

*앞서 배운 모든 노드가 유기적으로 연결되었는지 확인하는 최종 단계입니다.*

목표:

- Autoware 전체 파이프라인의 데이터 흐름을 한눈에 파악합니다.
- 노드 간 통신 단절 구간을 찾아내어 시스템 결함을 진단하는 능력을 기릅니다.

### 8-1. RQT Graph 전체 분석

- Sensing -> Perception -> Planning -> Control 로 이어지는 거대한 노드 그래프 실습.
- Topic이 끊긴 구간(Subscription 없는 구간) 찾기 트레이닝.

