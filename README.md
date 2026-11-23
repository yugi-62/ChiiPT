# ChiiPT

## 📌 프로젝트 소개
> 본 저장소는 2026년 먼멋귀 팀 프로젝트로 개발된 결과물을 바탕으로, 개인 포트폴리오 및 기능 고도화를 위해 별도로 구성한 저장소입니다.

* **원본 저장소:** [Team-Repo 바로가기](https://github.com/HongJC-PortFolio/ChiiPT.git)

**Windows 데스크톱 자세 교정 및 힐링 펫**

ChiiPT는 Electron과 MediaPipe를 사용해 사용자의 자세와 손동작을 로컬에서 인식하고, 투명 오버레이 캐릭터로 반응을 보여주는 Windows 데스크톱 애플리케이션입니다. 웹캠 영상은 외부 서버로 전송하지 않고 기기에서 처리합니다.

## 현재 구현 상태

- Windows 투명·테두리 없는 Always-on-top 오버레이
- 캐릭터 영역만 마우스 입력을 받는 클릭 통과 처리
- 시스템 트레이에서 카메라, 모드, 수면·기상 제어
- 공통 카메라 스트림의 시작·중단·장치 선택·오류 처리
- MediaPipe Pose Landmarker 기반 자세 인식
- 어깨 기울기와 화면 거리 변화에 따른 캐릭터 반응
- MediaPipe Hand Landmarker 기반 손동작 인식
- 쓰다듬기와 좌우 볼 꼬집기 인식
- 캐릭터 수면·기상 애니메이션과 일정 시간 스트레칭 알림
- 좌우 이동 및 커서 추적 산책 애니메이션
- Windows NSIS 설치형과 이동형 exe 패키징

## 주요 기능

### 자세 인식

자세 모드에서 공유 웹캠 스트림을 받아 Pose Landmarker로 어깨와 상체 랜드마크를 추출합니다.

- 어깨 높이 차이를 이용해 좌우 기울기 방향과 강도를 계산합니다.
- 얼굴/상체가 화면에 가까워지면 캐릭터 크기를 조절합니다.
- 인식 결과에 따라 좌우 넘어짐·달리기 애니메이션과 복귀 동작을 적용합니다.
- 자세 기능은 짧은 변동을 완충하고 누락된 랜드마크는 미인식 상태로 처리합니다.

### 손동작 인식

힐링 모드에서 Hand Landmarker로 손가락과 손바닥 랜드마크를 추출합니다.

- 캐릭터 머리 영역 안에서 좌우로 반복되는 검지 동작을 쓰다듬기로 인식합니다.
- 엄지와 검지가 서로 잡힌 모양을 좌우 볼 꼬집기 동작으로 인식합니다.
- 쓰다듬기·꼬집기 상태에 맞는 표정과 프레임 애니메이션을 재생합니다.
- 손이 카메라 가이드 또는 캐릭터 영역을 벗어나면 상호작용을 해제합니다.

### 수면 및 기상

- 캐릭터를 약 2초 누르면 수면 상태로 전환합니다.
- 잠든 캐릭터를 더블클릭하면 기상합니다.
- 수면 시작 시 선택된 기능과 카메라 스트림을 중단합니다.
- 기상 시 수면 전 사용자가 요청한 모드와 카메라 상태를 복원합니다.
- 트레이 메뉴에서도 수면과 기상을 제어할 수 있습니다.

### 산책과 스트레칭

- 힐링 모드에서 마우스 산책을 켜면 캐릭터가 커서 방향으로 이동합니다.
- 화면 경계와 접근 불가능한 위치를 고려해 캐릭터를 화면 안에 유지합니다.
- 수면 중에는 산책과 카메라 인식을 중단합니다.
- 기상 상태가 일정 시간 지속되면 스트레칭 애니메이션을 한 번 재생합니다.

## 실행 방법

Windows x64와 Node.js 24 LTS를 기준으로 합니다. 프로젝트 루트에서 다음을 실행합니다.

```powershell
npm ci
npm run dev
```

`npm run dev`는 타입 검사와 빌드 후 Electron 앱을 실행합니다. 소스 수정 후에는 앱을 종료하고 다시 실행해야 반영됩니다.

```powershell
npm run typecheck
npm run build
npm test
npm start
npm run package:win
```

| 명령 | 설명 |
| --- | --- |
| `npm run typecheck` | TypeScript strict 타입 검사 |
| `npm run build` | 압축된 실행 코드를 `dist/`에 빌드 |
| `npm test` | 카메라, 수명주기, 자세, 힐링, 애니메이션 테스트 실행 |
| `npm start` | 빌드 후 앱 실행 |
| `npm run package:win` | Windows 설치형과 이동형 exe 생성 |

## exe 파일

`npm run package:win` 실행 결과는 `dist_electron/`에 저장됩니다.

- `ChiiPT-0.1.0-x64-Setup.exe`
  - Windows에 설치하고 바탕 화면·시작 메뉴 바로 가기를 생성합니다.
- `ChiiPT-0.1.0-x64-Portable.exe`
  - 설치 없이 바로 실행하며 임시 폴더나 USB 등에서 사용할 수 있습니다.

현재 패키지는 코드 서명 인증서로 서명되지 않았으며 기본 Electron 아이콘을 사용합니다.

## 프로젝트 구조

```text
ChiiPT/
├── src/
│   ├── main/                 # Electron 창, 트레이, 권한, IPC
│   ├── preload/              # 제한된 Renderer 브리지
│   ├── renderer/
│   │   ├── features/
│   │   │   ├── posture/      # 자세 추론 및 판정
│   │   │   └── healing/      # 손 인식 및 상호작용
│   │   ├── character/        # 상태와 애니메이션 렌더링
│   │   └── shared/           # 카메라, 상태, 공통 타입
│   └── assets/
│       ├── characters/       # 캐릭터 PNG 프레임
│       ├── mediapipe/        # MediaPipe WASM 런타임
│       └── models/           # Pose/Hand Landmarker 모델
├── scripts/                  # 빌드 및 실행 스크립트
├── tests/                    # Node 테스트
├── docs/                     # 기획 및 개발 문서
├── dist/                     # 빌드 산출물
├── dist_electron/            # Windows exe 패키지
└── package.json
```

## 기술 스택

| 구분 | 기술 |
| --- | --- |
| Desktop | Electron 45 |
| Language | TypeScript, Node.js |
| Bundling | esbuild |
| Vision | MediaPipe Tasks Vision |
| Animation | PNG 프레임과 TypeScript 렌더러 |
| Packaging | electron-builder / NSIS |

## 보안 및 개인정보

- 사전 로드 스크립트를 제외한 Renderer에서 Node integration을 사용하지 않습니다.
- `contextIsolation`, `sandbox`, `webSecurity`를 활성화합니다.
- Main 프로세스는 IPC 발신 프레임과 페이로드를 검증합니다.
- 웹캠 권한은 앱의 최상위 프레임에서 요청한 video에만 허용합니다.
- 오디오 권한을 요청하지 않고 카메라 트랙만 관리합니다.
- 웹캠 영상과 인식 결과를 로컬 파일로 저장하거나 외부로 전송하지 않습니다.

## 검증 현황

- TypeScript 타입 검사 통과
- esbuild 빌드 통과
- 카메라·수명주기·자세·힐링·애니메이션 관련 자동 테스트 46개 통과
- Windows에서 이동형 exe 시작 및 Electron 프로세스 실행 점검 완료
- 카메라 권한 대화상자와 실제 장치 동작은 실제 Windows 환경에서 수동 확인이 필요합니다.
- 투명도, 마우스 클릭 통과, Always-on-top와 물리 카메라 동작은 수동 검증 항목으로 남아 있습니다.

자세한 빌드와 수동 테스트 절차는 [개발 안내서](docs/development.md)를 참고하세요.

## 관련 문서

- [프로젝트 기획서](docs/project_proposal.md)
- [기능 명세 및 구현 설계서](docs/functional_design.md)
- [개발 환경과 검증 안내](docs/development.md)
- [캐릭터 자산 안내](src/assets/README.md)

---

**ChiiPT — Making everyday PC use healthier and more delightful.**
