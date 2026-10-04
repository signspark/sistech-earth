# SISTECH EARTH 데모 · 사용 안내

작성 2026-10-04

## 파일
| 파일 | 내용 |
|---|---|
| `SISTECH_EARTH_demo.html` | 데모 본체 (단일 HTML, CesiumJS 1.126 CDN 사용) |
| `SISTECH_EARTH_demo_실행.bat` | 더블클릭하면 로컬 서버(localhost:8765)를 띄우고 브라우저를 엽니다 (Node.js 또는 Python 필요, 이 PC에는 Node v26 설치됨) |
| `SISTECH_EARTH_메뉴제안.md` | 진청+연두 디자인 토큰, EARTH/SITE 메뉴 트리, 지점 카드 규칙, 구현 우선순위 |
| `EARTHUS_벤치마크_분석_SISTECH3D연계.md` | 벤치마크 원본 분석 |

## 실행 방법
1. **권장:** `SISTECH_EARTH_demo_실행.bat` 더블클릭 → 브라우저가 자동으로 열립니다.
2. **임시 온라인 주소 (2026-10-07 까지, 72시간 후 자동 삭제):** https://litter.catbox.moe/ry72wn.html
   - 휴대폰에서도 열립니다.
3. HTML 파일을 그냥 더블클릭(file://)하면 지구 지형 계산용 워커가 차단되어 **지구가 검게 보일 수 있습니다.** 그 경우 1번을 쓰세요.

## 조작
- 왼쪽 메뉴: 카테고리 → 레이어. 연두색 점 = 켜짐. "준비 중" = 데이터 연동 예정, "SITE 전용" = 3D 모델 위에서만 제공.
- 지도 클릭 → 오른쪽 지점 카드 (날씨·대기 레이어가 켜져 있을 때 값 표시).
- 하단 시간 바: -24h ~ +120h. 움직이면 지점 카드 값이 그 시각 예보로 바뀜. ▶ 는 5일 자동 재생.
- 상단 EARTH / SITE: 모드 전환. SITE는 가장 가까운 프로젝트로 이동.
- 측정 › 거리/면적: 클릭으로 점 찍기, 더블클릭으로 끝. Esc 종료.
- 검색(⌕): 도시·주소(OpenStreetMap) 또는 프로젝트 이름.
- 주소창 해시(#at=...)에 위치·레이어가 저장되어 링크 공유 가능.

## 실제로 연결된 데이터
- **SISTECH 3D-View 타일셋 (인천-1·부산-1·분당-1·고양-1)**: 현장 근처(60 km)로 가면 자동 로드. 모델 위 클릭·높이 측정 가능
- JMA Himawari-9 (NASA GIBS, 10분 간격), NASA MODIS·VIIRS·GPM 위성 영상
- CelesTrak 궤도요소 + satellite.js SGP4: 한국 위성·지구관측 위성·ISS 29기, 스타링크, 현장 통과 예보
- Launch Library 2 로켓 발사 일정
- Esri World Imagery (영상), Esri Reference (지명)
- USGS 지진 피드 (M2.5+, 7일)
- NASA GIBS: VIIRS Black Marble(밤의 불빛), MODIS Terra(어제 구름)
- Open-Meteo: 지점 기상(GFS·ECMWF), 대기질(CAMS)
- OpenStreetMap Nominatim: 역지오코딩·검색

## 예시(가짜) 데이터
- 상단 "인천 강풍주의보" 배너
- 프로젝트 촬영일·GSD·정확도 ("예시" 표기)
- 다르항-올 좌표(도시 중심 대략값)
- 비행 가능 판정 기준(풍속 8, 돌풍 12, 강수 0, 시정 3 km) - 실제 운용 기준으로 교체 필요

## 통합 시 다음 단계
1. (완료) 3D-View 타일셋 자동 로드 → 다음: 타일셋 접근 제어(토큰) 검토
2. SITE 메뉴에 기존 측정·화질·저장 기능 연결 (이미 시범 구현된 코드 재사용)
3. 기상청 API허브(AWS·특보·레이더), 에어코리아 연동
4. Cesium World Terrain 또는 자체 DEM 연결 (과장 배율 활성화)
5. 영구 호스팅 (GitHub Pages / Cloudflare Pages / Railway)
