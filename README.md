# Deferred Rendering을 이용한 렌더링 최적화

GDC 2025 발표 자료 저장소입니다. Forward Rendering의 비효율을 Deferred Rendering으로 해결하는 과정과, Deferred Rendering의 한계 및 해결 방안을 다룹니다.

- 발표자: 안지우 (부경대학교 컴퓨터공학과 4학년)
- Git: https://github.com/powercrabman
- Blog: https://powerclabman.tistory.com/

## 파일

| 파일 | 설명 |
| --- | --- |
| `Deferred Rendering을 이용한 렌더링 최적화.pptx` | 발표 슬라이드 (37장) |

## 발표 내용

### 1. 배경지식
- 렌더링 기초: 렌더링은 픽셀의 위치와 색을 결정하는 행위
- 3D 렌더링: 입체감을 위해 빛과 재질(Metallic, Roughness, Emission), 법선 벡터, 깊이가 필요
- 1280 x 720 해상도, 60fps 기준 초당 최소 55,296,000번의 색상 계산 필요

### 2. Forward Rendering
- 모델마다 렌더링 파이프라인을 수행 (모델 N개 → 파이프라인 N회)
- 가려지는 픽셀에도 색상 계산이 수행되어 낭비 발생

### 3. Deferred Rendering
- 1단계: 모든 모델의 기하 정보(위치, 법선, 깊이, 재질 등)를 G-buffer에 렌더링
- 2단계: G-buffer를 이용해 색상 계산을 딱 한 번만 수행
- 최적화 기법
  - G-buffer(스크린) 개수 최소화
  - 깊이 값에서 위치를 복원하여 위치 버퍼 제거
  - 값 범위에 맞는 포맷 선택 (`R8G8B8A8_UNORM` / `R16G16B16A16_FLOAT`)

### 4. 성능 비교 (스트레스 테스트)
- 환경: AMD Ryzen 9 7945HX, NVIDIA GeForce RTX 4060
- 조건: 구 100,000개 (총 버텍스 약 6.43억 개)

| 방식 | FPS |
| --- | --- |
| Forward Rendering | 40 |
| Deferred Rendering | 68 (약 70% 향상) |

### 5. 한계점과 해결 방안
- **안티앨리어싱 불가**: 하드웨어 MSAA 사용 불가 → FXAA, TAA 등 후처리 방식 사용
- **반투명 물체 처리 불가**
  1. 반투명 물체만 z-sort 후 Forward Rendering
  2. 디더링으로 근사적인 반투명 구현
  3. Order-Independent Transparency (OIT)

## 참고 자료
- [Real Shading in Unreal Engine 4 (SIGGRAPH 2013)](https://cdn2.unrealengine.com/Resources/files/2013SiggraphPresentationsNotes-26915738.pdf)
- [FXAA White Paper (NVIDIA)](https://developer.download.nvidia.com/assets/gamedev/files/sdk/11/FXAA_WhitePaper.pdf)
- [OIT Approximation with Pixel Synchronization (Intel)](https://www.intel.com/content/www/us/en/developer/articles/technical/oit-approximation-with-pixel-synchronization.html)
