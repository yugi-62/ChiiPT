## ChiiPT 런타임 자산

이 폴더에는 애플리케이션에 포함되는 캐릭터 이미지와 MediaPipe 런타임을 보관합니다.

```text
assets/
├── characters/       # 수면, 기상, 산책, 쓰다듬기, 볼 꼬집기 PNG 프레임
├── mediapipe/        # Pose 및 Hand 인식을 위한 WASM 런타임
└── models/           # Pose Lite와 Hand Landmarker 모델
```

- `characters/`: 캐릭터 상태와 상호작용 애니메이션의 PNG 프레임입니다.
- `mediapipe/wasm/`: 브라우저 Renderer와 Pose Worker가 사용하는 MediaPipe WASM 파일입니다.
- `models/pose_landmarker_lite.task`: 자세 인식 모델입니다.
- `models/hand_landmarker.task`: 손동작 인식 모델입니다.

빌드 시 이 디렉터리 전체가 `dist/renderer/assets/`로 복사됩니다. MediaPipe 패키지의 WASM 파일은 설치된 패키지 버전에 맞게 빌드 스크립트에서 다시 동기화합니다.
