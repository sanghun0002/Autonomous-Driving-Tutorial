## Part 8. Autoware: Perception (인지)

*주변 환경의 객체(Object)와 신호등을 인식하는 과정입니다.*

### 5-1. Object Detection (Lidar-CenterPoint)

> 학습 포인트: 점구름에서 어떻게 3D Bounding Box를 뽑아내는가?
> 
- **Main Node:** `lidar_centerpoint` (또는 `apollo_lidar_segmentation`)
- **Input Topic:** `/lidar/concatenated/pointcloud`
- **Processing:**
    - Deep Learning 모델(Onnx/TensorRT)을 통과하여 객체 분류 및 위치 추정.
- **Output Topic:** `/perception/object_recognition/detection/objects` (DetectedObjects)

### 5-2. Sensor Fusion (Multi-Modal)

- **Main Node:** `roi_cluster_fusion` (LiDAR + Camera)
- **Input Topics:**
    1. `/perception/.../detection/objects` (LiDAR 결과)
    2. `/perception/.../rois` (카메라 2D Detection 결과)
- **Processing:**
    - 3D Lidar Box를 2D 이미지 평면에 투영(Projection)하여 IOU 계산 후 클래스(Class) 정보 보정.
- **Output Topic:** `/perception/object_recognition/objects` (최종 인식된 객체 리스트)

### 5-3. Tracking (추적)

- **Main Node:** `multi_object_tracker`
- **Process:** 매 프레임 검출된 객체에 ID를 부여하고 속도/경로 예측 (Kalman Filter).
- **Output Topic:** `/perception/object_recognition/tracking/objects` (Planning 노드로 전달됨)
