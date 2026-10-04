# EARTHUS 전 기능 카탈로그 (우주탐험 제외) · SISTECH EARTH 구현 분류

- 작성 2026-10-04 · 출처: earthus.net/v2 소스(menu-canon.js · phenomenon-registry.js · same-service-catalog.js) + 메뉴 버튼 직접 클릭 확인
- EARTHUS 구조: 7개 도크 · 현상 66개(+공유 19) · 레이어 109개. 우주(AETHERUS: 오로라·은하·우주사진·태양활동·태양계·우주쓰레기·로켓발사·일식 애니메이션)는 제외.
- 기호: ✅ 공개 데이터로 데모 구현 · 🔁 다른 공개 데이터로 대체 구현 · 🔑 API 키 필요(준비 중) · ⏳ 서버/프록시/자체자료 필요(준비 중)

## 지구

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 지형 | 땅의 높낮이가 실제로 얼마나 다른가 | 전지구. 실측 고도는 AWS Terrarium 을 전역 한 장(PC 는 z4 · 적도 약 9.8 km/px, 폰·태블릿은 z3 · 약 19.6 km/px) + z5~z9 지역으로 스트리밍하고, 고도 4,000km 아래로 내려가면 Esri World I | current | VISUALIZATION_ONLY | ✅ Esri World Hillshade 음영 타일 (지형 과장은 DEM 연결 후) |
| 바다 깊이 | 이 바다의 깊이는 |  | Static — no time dimension. | Mixed | ✅ Esri World Ocean Base(수심 음영) + 주요 해구 20곳 위치 핀(GEBCO 가제티어 좌표 내장) |
| 밤의 불빛 | 밤에 밝은 도시는 어디인가 | 전지구 500m 일별 야간광. NASA Black Marble VNP46A2(갭필·BRDF·달빛 보정)를 약 54시간 지연으로 받는다 — 이틀 전 밤에 실제로 찍힌 불빛이다. 그날치를 못 받으면 2016년 합성본, 다시 2012년 합성본으로 내려가고  | daily (~2일 지연), 실패 시 연도가 박힌 합성본으로 강등 | OFFICIAL_OBSERVATION | ✅ NASA VIIRS Black Marble |
| 지표온도 | 땅 표면이 얼마나 뜨거운가 | 전지구 1km 주간 관측. MODIS Terra 주간 지표온도 타일 50장을 이틀 전 날짜로 받아 얹는다(15장 미만이면 표시하지 않음). 위성이 잰 땅 표면 온도이며 기상관측소의 지상 2m 기온과 다르다. 구름에 가린 곳은 관측이 없어 비어 있다. | current (2일 위성 지연) | OFFICIAL_OBSERVATION | ✅ NASA MODIS LST (어제) |
| 일식 | 일식 자료 보기 | NASA GSFC | 원자료 기준시각 | OBSERVED | ✅ USNO 일식 API → 올해 일식 목록 카드 (지도 표시는 생략) |

## 날씨

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 기온 | 선택한 곳은 몇 도인가 | 전지구 5° 격자(위도 80°N~80°S, 극지 제외) + 한국 AWS 지점(가장 가까운 400km 이내 지점을 이름·거리와 함께) | 지금(격자 1시간 갱신). 예보 산출물은 서버에 있으나 v2 화면에는 없다. | PROVIDER_FORECAST | ✅ Open-Meteo 격자 샘플(현재 화면 범위 24×16) → 열지도 + 범례, 시간 바 연동 |
| 평년 대비 기온 | 평년보다 얼마나 덥거나 추운가 | 한국 — 기상청 실황 지점과 평년값(1991~2020) 지점이 같은 id 로 맞는 곳만 | 지금 실황 − 1991~2020 평년 | EARTHUS_ANALYSIS | 🔁 기상청 평년값 대신 Open-Meteo 과거 5년 같은 날 평균 대비 (DERIVED, 지점 카드) |
| 오늘의 극값 | 오늘 지구에서 가장 덥고 춥고 파도가 높은 곳은 어디인가 | 전지구. 5° 격자(약 550km)에서 찾은 9개 극값 — 가장 파도가 높은 바다·가장 따뜻한 바다·가장 차가운 바다·가장 더운 곳·가장 추운 곳·바람이 가장 센 곳·먼지가 가장 많은 하늘·자외선이 가장 강한 곳·가장 앞이 안 보이는 곳. 격자 해상도 | 지금 — 격자 갱신 주기를 따르고 클라이언트에서 30분 캐시. 하루 단위로 화면이 바뀐다. | PROVIDER_FORECAST | ✅ 전지구 10° 성긴 격자에서 최고·최저 기온, 최대 풍속, 최대 파고 지점 핀 |
| 강수 | 지금 내 주변에 비가 오나 | 관측은 한국뿐(기상청 HSR 레이더 합성영상 — 좌표계가 없어 지구본에 얹지 않고 원본 영상 그대로 보여 준다). 전지구 강수는 NOAA GFS 0.5° 예보 프레임의 강수율(mm/h)이다 — 5° 한 시각의 선형 램프가 아니다(2026-09-20 작 | 지금(레이더 5분) + 예보 +120h · 격자 3시간(GFS 0.5° 예보 프레임) | PROVIDER_FORECAST | ✅ RainViewer 전지구 레이더 합성(10분, 과거 2h~+30min, 시간 바 연동) + 격자 강수 열지도 |
| 바람 | 어느 방향으로 얼마나 세게 부나 | 전지구 GFS 0.5°(약 55 km) 지상 10 m 바람 — 태풍 중심의 최대풍속은 무디게 담긴다 + 전지구 5° 모델 격자 색면 | 타임라인이 가리키는 시각(GFS 3시간 프레임 사이를 보간 · 런 시각부터 5일). 5° 격자 색면(weat | PROVIDER_FORECAST | ✅ 격자 풍향 화살표 + 풍속 열지도 |
| 기압 | 고기압과 저기압은 어디인가 | 전지구 0.5°(약 55km) 격자의 해면기압. 4 hPa 등압선과 H/L 중심 기호를 그 판에서 직접 긋는다(1° 동아시아 판은 쓰지 않는다). | 지금 + 5일 예보(3시간 프레임 · 런은 6시간마다) | PROVIDER_FORECAST | ✅ 해면기압 열지도 + H/L 중심 기호(격자 극값) |
| 지상 관측 | 지금 각 관측소는 무엇을 재고 있나 | 한국 기상청 지상관측(AWS)과 전지구 GTS SYNOP. 일기(WW) 코드는 실측 확인 결과 97곳 중 3곳에서만 온다. 전선(한랭·온난·정체·폐색)은 좌표를 주는 기관이 없어 그리지 않는다. | 지금(10분~1시간) | OFFICIAL_OBSERVATION | ⏳ METAR·KMA AWS 모두 브라우저 직접 호출 불가(CORS/키) → 서버 프록시 필요 |
| 기후 시계열 | 올해가 예년과 얼마나 다른가 | 전지구·대륙·국가·한국. 네 개의 일별 시계열 — 해수면온도 60°S–60°N(OISST v2.1, 1982~), 해빙 면적 북극·남극(NSIDC G02135, 1979~ · concentration 15% 이상 격자칸을 통째로 센 extent 이며  | 1973/1979/1982년~오늘. aws/climatology 가 하루 1회, 네 빌더를 EventBrid | OFFICIAL_OBSERVATION | ✅ Open-Meteo 과거기상(ERA5) 365일 일평균 시계열 스파크라인 (지점 카드) |
| 습도 | 습도 자료 보기 | Open-Meteo 수치모델 | 원자료 기준시각 | OBSERVED | ✅ 격자 열지도 |
| 수증기 통로 | 수증기 통로 자료 보기 | NOAA GFS | 원자료 기준시각 | OBSERVED | 🔁 GFS 수증기 통로 대신 NASA MODIS 수증기 영상 |
| 내일 최고기온 | 내일 최고기온 자료 보기 | Open-Meteo 수치모델 (내일 예측) | 다음 현지 날짜 | OBSERVED | ✅ 내일 최고기온 격자(daily) |
| 내일 최저기온 | 내일 최저기온 자료 보기 | Open-Meteo 수치모델 (내일 예측) | 다음 현지 날짜 | OBSERVED | ✅ 내일 최저기온 격자 |
| 내일 바람 | 내일 바람 자료 보기 | Open-Meteo 수치모델 (내일 예측) | 다음 현지 날짜 | OBSERVED | ✅ 내일 바람 격자 |
| 시정 | 시정 자료 보기 | Open-Meteo 수치모델 | 원자료 기준시각 | OBSERVED | ✅ 시정 격자 |
| 토양 수분 | 토양 수분 자료 보기 | Open-Meteo 수치모델 | 원자료 기준시각 | OBSERVED | ✅ 토양 수분 격자 |
| 지상 관측소 | 지상 관측소 자료 보기 | METAR · GTS · KMA · JMA · CWA | 원자료 기준시각 | OBSERVED | ⏳ CORS/키 |
| 영국 예보 | 영국 예보 자료 보기 | Powered by Met Office data (내일 예측) | 다음 현지 날짜 | OBSERVED | ⏳ 영국 한정, Met Office 키 |
| 관측 공백 | 관측 공백 자료 보기 | METAR · GTS · 해양 부이 | 원자료 기준시각 | OBSERVED | ⏳ 관측망 자료 필요 |

## 위성

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 구름 | 전 세계 실제 구름 분포는 | 기본 위성 공급원 네 개: NOAA GMGSI 전지구 합성, Suomi NPP VIIRS 진색 영상, 천리안 GK2A 낮·밤 자동, 히마와리9 낮·밤 자동. 채널·관측 범위는 공급원 안에서 선택하며, 관측 공백을 구름 없음으로 바꾸지 않는다. 화면의  | 지금(GMGSI 180분 · GK2A 10분 주기). 타임스트립을 ±1시간 이상 밀면 관측 구름이 GFS 예 | OFFICIAL_OBSERVATION | ✅ 히마와리-9(10분)·Suomi NPP·MODIS |
| 안개·낮은구름 | 밤에 낮은 구름이 있는가 | 천리안(GK2A) 관측 원반 = 동아시아. 야간에만 성립한다(주간 BTD 로는 구분되지 않는다). | 지금(10분 주기) · 밤에만 | OFFICIAL_OBSERVATION | 🔑 천리안 야간 저층운 — 기상청 위성센터 키 |
| 상층 수증기 | 상층 대기의 흐름은 | 천리안(GK2A) 관측 원반 = 동아시아. 수증기 6.3µm 채널이라 구름이 없는 곳의 중·상층 습기까지 보인다. | 지금(10분 주기) | OFFICIAL_OBSERVATION | 🔁 GK2A 수증기 대신 MODIS 수증기(어제) |
| 눈 덮임 | 오늘 어디에 눈이 덮여 있나 | 전지구 500m. MODIS Terra NDSI 눈덮임 타일 50장(main.js:5383 `MODIS_Terra_NDSI_Snow_Cover/default/{date}/500m/3/{r}/{c}.png`)을 이틀 전 날짜로 받아 얹는다. 20장 미만 | current (2일 위성 지연) | OFFICIAL_OBSERVATION | ✅ MODIS NDSI |
| 해빙 | 극지의 얼음 면적이 얼마나 변했나 | 북극·남극. 지구본에는 오늘의 해빙 농도 이미지(GHRSST L4 MUR, 이틀 전)가 얹히고, 면적 변화는 NSIDC G02135 v4.0 일별 면적 계열 1979~ 이 따로 있다. 면적(extent, 농도 15% 이상 칸의 넓이 합)이며 실제 얼음 | current + 1979~ 일별 계열 | OFFICIAL_OBSERVATION | ✅ MODIS 해빙 |
| 위성 | 위성은 지금 어디에 있는가 |  | now — positions are SGP4-propagated to the current instant e | OFFICIAL_OBSERVATION | ✅ CelesTrak SGP4 28기 + 스타링크 + 현장 통과 예보 |

## 바다

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 파고와 너울 | 선택 해역의 파고와 바람은 |  | Present values, hourly field, freshness bar 180 min; one ext | PROVIDER_FORECAST | ✅ Open-Meteo Marine 격자 파고 열지도 + 지점 |
| 해수면 온도 | 바다 표면 온도는 |  | Daily observation, freshness bar 1440 min. | OFFICIAL_OBSERVATION | ✅ Marine 격자 해수면온도 |
| 평년 대비 수온 | 평년보다 바다가 얼마나 따뜻한가 |  | Daily observation against a 30-year normal, freshness bar 28 | OFFICIAL_OBSERVATION | ⏳ 평년 수온 자료(OISST) 필요 |
| 표층 해류 | 표층 물은 어디로 흐르나 |  | Present values, hourly field, freshness bar 180 min. | PROVIDER_FORECAST | ✅ Marine 해류 화살표 |
| 해수면 상승 전망 | 2100년에 해수면이 얼마나 오를 수 있나 | Two products under one question. World: IPCC AR6 projections at tide-gauge stations worldwide (medians for 2050/2100/2150 with the 17-83% ra | Long-term scenario projection to 2100 (2150 available in the | PROVIDER_FORECAST, | ⏳ IPCC/KHOA 시나리오 자료 필요 |
| 바다 실측 | 바다에서 실제로 잰 값은 | Two in-situ networks. Global: NDBC latest_obs plus NOAA OSMC GTS buoys (wave, sea temperature, pressure, wind) — coverage is uneven by publi | Present observation — Korea every 10 minutes (freshness bar  | OFFICIAL_OBSERVATION | ⏳ NDBC/기상청 부이 CORS |
| 수심별 수온 | 깊이에 따라 수온이 어떻게 변하나 |  | About a ten-day float cycle; the last 20 days of surfacings  | OFFICIAL_OBSERVATION | ⏳ Argo 자료 서버 필요 |
| 해구 | 가장 깊은 해구는 어디인가 | 전지구 해구. 위치 점·이름표와 상세 카드 10곳(챌린저 해연·호라이즌 해연·시흘 해연·뉴브리튼–부건빌·브라운슨 해연·메테오르 해연·팩토리안 심연·자바 무명 해연·도르드레흐트 해연·몰로이 홀) + 한국 바다 3장. 수심은 자료의 depthMin~dep | 정적 (카탈로그 2026-08-09 생성, GEBCO 2026 · SCUFN 가제티어) | OFFICIAL_OBSERVATION | ✅ 해구 핀 |
| 심해 | 이 바다는 얼마나 깊고, 그 아래에 무엇이 사나 | 전지구. 고른 지점의 수심 기둥을 GEBCO 0.1° 격자로 내려간다 — 셀 안 15초 원본 576개의 최솟값을 보존한 뒤 인접 셀을 보간한 정보 제품이고, 특정 좌표의 실측 수심이 아니며 항해·해상 안전용이 아니다. 육지 셀이면 육지라고 쓰고 잠수하 | 정적 (GEBCO 2026 격자) + OBIS 요약 주 1회 재생성 | EARTHUS_ANALYSIS | ⏳ EARTHUS 자체 콘텐츠 |
| 선박 | 선박이나 여객선 정보를 보려면 | Nothing renders on the globe. AIS positions are not redistributed as a matter of policy (unchanged from 1.0); route questions are answered i | None — locked, no data is fetched. | none | ⏳ AIS 키 |
| 너울 | 너울 자료 보기 | 해양 수치모델 | 원자료 기준시각 | OBSERVED | ✅ 너울 높이 격자 |
| 해양 환류 | 해양 환류 자료 보기 | 해양 환류의 정적 참고 위치 | 원자료 기준시각 | DERIVED | ✅ 5대 해양 환류 정적 위치 |

## 대기

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 대기질 | 공기가 얼마나 탁한가 | 한국 에어코리아 측정소(가장 가까운 곳을 이름·거리와 함께) + 전지구 5° 격자. 동아시아 0.5° 보강판(wind/air-ea.json)은 v1 화면만 쓴다. | 지금(관측 1시간, 격자 1시간). 24시간 예보와 채점은 LAB 보고서 경로 안에만 있다. | OFFICIAL_OBSERVATION | ✅ CAMS 격자 PM2.5 열지도 + 지점 |
| 자외선 | 자외선 수준은 어느 정도인가 | 전지구 5° 격자. 밤(0)은 칠하지 않는다. | 지금(1시간 갱신) | PROVIDER_FORECAST | ✅ UV 지수 격자 |
| 미세먼지 | 미세먼지 자료 보기 | CAMS · Open-Meteo | 원자료 기준시각 | OBSERVED | ✅ 격자 |
| 먼지 농도 | 먼지 농도 자료 보기 | CAMS · Open-Meteo | 원자료 기준시각 | OBSERVED | ✅ 격자 |
| 대기질 지수 | 대기질 지수 자료 보기 | CAMS · 유럽 대기질 지수 | 원자료 기준시각 | OBSERVED | ✅ 격자 |
| 오존 | 오존 자료 보기 | CAMS · Open-Meteo | 원자료 기준시각 | OBSERVED | ✅ 격자 |

## 재난·뉴스

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 지역 뉴스 | 이 지역에 무슨 일이 생겼나 | 아프리카·중동·남미·동남아·오세아니아 등 그 지역 매체가 직접 내는 RSS. 기사에 좌표가 없어 지역 대표점에 묶어 세우는 지역 단위 표시이며 도시·지점 단위가 아니다(live-layers.js:917-918 주석). GDELT 가 덜 잡는 비영어권  | 지금 — 최근 헤드라인만. SLA 120분(engine-bridge.js:144), 매체당 최대 12건. | VISUALIZATION_ONLY(engine-bridge.js:144). | ⏳ GDELT CORS 불가 → 프록시 |
| 기상 특보 | 내 지역의 공식 특보는 | 한국(기상청 특보 구역)과 미국(NWS)뿐. 일본판(events/jma-warn.json)은 원본이 살아 있을 때(live=true)만 목록을 내보낸다 — 2026-05-28~09-20 은 JMA 가 경로를 r8 로 옮긴 것을 따라가지 못해 멈춰 있었 | 지금 발효 중(validFromKst~validToKst 유효기간) | OFFICIAL_WARNING | 🔑 기상청 특보 API |
| 태풍 | 공식 태풍 경로는 |  | now + official forecast + ECMWF ENS 51-member spread (PROVID | OFFICIAL_FORECAST | ✅ JMA 공식 태풍 경로(실황·예보원) — 현재 Choi-wan |
| 지진 | 최근 지진은 어디서 났나 |  | now + 25-year history | OFFICIAL_OBSERVATION | ✅ USGS |
| 쓰나미 | 현재 유효한 쓰나미 정보는 |  | now (bulletins in force) + simulated arrival times per event | OFFICIAL_WARNING | ⏳ tsunami.gov CORS |
| 산불 | 지금 어디가 불타고 있나 | Two coverages. Global: NASA FIRMS VIIRS 375 m active-fire detections from the last 24 hours, neighbouring pixels clustered into one fire wit | now (24-hour hotspots) + 3-hourly risk index | OFFICIAL_OBSERVATION | ✅ NASA EONET 산불 이벤트 + (키 입력 시) FIRMS 열점 |
| 낙뢰 | 최근 주변에 낙뢰가 있었나 |  | now (last 60 minutes) | OFFICIAL_OBSERVATION | ⏳ Blitzortung 공개 API 없음 |
| 연안 침수 범위 | 어떤 가정에서 연안이 잠길 수 있나 | 국립해양조사원 inundation-extent polygons for the 70 시군구 the agency actually serves, fetched per district on demand; colour is the agency\'s predic | Static agency scenario map; refreshed only by a manual Lambd | PROVIDER_FORECAST, | ⏳ DEM 필요 → SITE 모드 침수 시뮬레이션으로 대체 예정 |
| 지각 이동 | 땅이 어느 방향으로 움직이나 |  | long-term average velocity + static boundary reference | OFFICIAL_OBSERVATION | ✅ 판 경계선(PB2002 GeoJSON) |
| 지각 이동 속도 | 땅이 어느 방향으로 움직이나 | 전지구 GNSS 상시관측점 1,352곳의 실측 속도 벡터(hazards/crustal 지도 레이어)와 한국·일본 중앙값 속도·방위 요약(lab/crust 카드). 두 화면이 같은 파일 events/crustal.json 하나를 읽는다. 잰 값이지 모델 | 연 단위 속도(mm/년). 최종 좌표 약 한 달 지연, 분석 단위는 월간 GNSS 위치 변화. | OFFICIAL_OBSERVATION | ⏳ UNR 속도장 CORS |
| 빙하호 홍수 | 빙하호 붕괴 시 영향은 | 알래스카 주노 멘덴홀 강(빙하호 Suicide Basin 아래) 한 곳 — USGS 15052500 실측·NWS MNDA2 공식 예보를 인용한다(events/glof-alaska.json). 호수가 아니라 호수 아래 강 수위다. 다른 지역은 자료가 없 | none | NONE | ⏳ 단일 지점(알래스카) 사례 |
| 각국 기관 재해 | 각국 기관 재해 자료 보기 | 각국 공식기관 | 원자료 기준시각 | OBSERVED | 🔁 각국 기관 대신 NASA EONET 종합 이벤트(폭풍·산불·화산·해빙) |
| 열돔 | 열돔 자료 보기 | Open-Meteo 상층·기온 조건 | 원자료 기준시각 | DERIVED | ✅ 격자 기온·기압 조건으로 열돔 영역 표시(DERIVED) |

## 지역

| 현상 | 질문 | EARTHUS 데이터·범위 | 시간 모드 | 근거 등급 | 데모 |
|---|---|---|---|---|---|
| 실시간 혼잡 | 서울 어느 곳이 지금 붐비나 | 서울 주요 121곳(서울시가 고른 공식 관측 장소)만. 도시 단위이며 전국·전세계가 아니다 — information-contract.js:11 이 이미 범위를 \'서울\'로 고지한다. 파생 배율(livemix)도 같은 121곳 안에서만 성립한다. | 지금 관측(SLA 30분) + 같은 파일에 실려 오는 서울시 공식 예측 구간이 하단 시간 막대에 연결된다.  | OFFICIAL_OBSERVATION | 🔑 서울 열린데이터 키 |
| 인구 | 어디에 사람이 거주하나 | 세 축척이 한 현상이다. 국가 총계는 전 세계(World Bank SP.POP.TOTL 최신 공표년) · 1km 격자는 21개국(prototype/v2-three/popgrid/index.json, WorldPop R2025A constrained U | 자료 기준일 고정 — 재생 시간축과 무관하다(information-contract.js:4 STATIC 에  | 혼합. | ✅ WorldPop 영역 인구(클릭 지점 10km 반경) |
| 오늘 갈 곳 | 오늘 어디를 가면 좋을까 | 대한민국 시군구 228곳. 좌표는 data/kr-places.json 의 시군구 중심점(경계 bbox 중점)이며 행정구역 폴리곤이 아니다. | current | EARTHUS_ANALYSIS | ⏳ EARTHUS 자체 카탈로그 |
| 목적별 관광지 | 내 조건에 맞는 관광지는 어디에 있나 | KTO 공개 관광지 정본 3종을 목적별로 거른 것. 배포본 기준 무장애 11,649건 · 웰니스 203건 · 영문 25,405건. 좌표는 시군구 중심점 최근접 배정(60km 초과 미배정: 무장애 19건, 영문 34건)이라 행정구역 포함 판정이 아니라  | registry | OFFICIAL_OBSERVATION | 🔁 Nominatim 관광지 검색(화면 범위) |
| 연관 관광지 | 이곳 다음에 어디를 가나 | 출발지 944곳, 각 상위 5개. 원본 22,234행. 배포본 기준월 2026-06. 관광지 단위이며 시군구 단위가 아니다. | history | HISTORY | ⏳ 자체 카탈로그 |
| 지역 방문자 | 어느 기간에 방문이 많았나 | 대한민국 시군구 228곳 중 방문자 자료가 있는 198곳. 배포본의 방문자수는 2026-07-06~2026-07-19 14일의 하루 평균이며 지역마다 집계 기간이 다를 수 있다. 같은 자료묶음의 집중률 예측은 187곳, 창은 2026-09-02~202 | history | HISTORY | ⏳ 자체 통계 |
| 여행지 | 여행지 정보를 찾으려면 | 전 세계 OSM 관광 장소를 목표로 하지만 오늘은 열리지 않는다. 공용 Overpass 서버가 504 로 불안정해 자체 프록시·캐시를 세운 뒤 연결한다. 그동안은 여행 장면의 travel/discover(KTO 시군구 228곳)로 안내한다 — 지금도  | 없음 — 잠금 상태라 시간축에 아무것도 싣지 않는다. | 없음 | ✅ Nominatim 검색 |
| 항공편 | 비행기가 실제 어디에 있나 | 전 세계 ADS-B 자원봉사 수신망(adsb.lol, ODbL 1.0 — 상업 사용 가능, 출처 표기 필요)을 목표로 한다. 대양·극지는 수신 공백이 있어 위성 ADS-B 와 다르다. 오늘은 잠금 — API 는 정상이나 CORS 헤더가 없어 Lambd | 없음(잠금). 임시 항로가 내는 시각은 순항 875km/h·지상 여유 30분·경유 90분 가정으로 우리가 계 | 없음 | ⏳ ADS-B API 모두 CORS 차단 → 프록시 |
| 숲 | 이 땅에 숲이 얼마나 있고, 언제 사라졌나 | 수관 비율은 한국·일본·대만 3개국(ESA WorldCover 10m v200 2021 을 0.005°≈550m 칸으로 평균; 한국 평균 64.1%·492,699칸, 일본 76.8%, 대만 75.6%). 소실 이력은 한국만 2001~2023(Hanse | 2021 관측 스냅샷 + 2001~2023 연도별 소실 이력 | OFFICIAL_OBSERVATION | ✅ GFW 산림 손실 타일 + MODIS NDVI |
| 철새 | 봄에 우리 동네 새가 어디로 갔나 | 위치추적 요약 179건 (2021~2025 CSV). 원자료는 한 줄에 출발지 하나·도착지 하나뿐이고 중간 경로가 없다 — 그린 선은 실제로 날아간 길이 아니라 \'여기서 저기로\'라는 뜻이고, 세 점(출발 20km · 가운데 260km · 도착 20 | 과거 — 연도별 출발 시기와 이동 기록. LAB 보고서는 3시간마다 세션 상태에서 다시 생성된다. | HISTORY | 🔁 eBird 키 대신 GBIF 조류 관측 기록 |
| 조류 조사 기록 | 어느 5km 칸에 기록이 있나 | 국립생태원 에코뱅크 — 자연환경조사(약 105만 건)·생태계정밀조사·백두대간정밀조사 조류 관측을 0.05°(약 5km) 칸으로 묶은 4,521칸. 새의 현재 위치도 개체수 지도도 아니다: 기록이 많은 칸은 조사 기록이 많이 쌓였다는 뜻이고, 빈 칸은  | 과거 — 조사 기록 누적 | HISTORY | ✅ GBIF 조류(Aves) 기록 — 클릭 지점 반경 |
| 바다거북 | 방류된 바다거북은 어디로 갔나 | 국립해양생물자원관이 발신기로 추적한 바다거북 45마리·28,770점(푸른바다거북·붉은바다거북·매부리바다거북). 기관 설명 그대로 \'추적이 종료된 수신기에 대해서만 조회\'하므로 실시간이 아니고, 마지막 점은 \'지금 위치\'가 아니라 \'여기서 추적 | 과거 — 종료된 추적의 지나간 경로. 수집 1일 주기 (aws/health/handler.py:153, ev | HISTORY | ✅ GBIF 바다거북과(Cheloniidae) |
| 바닷새 | 조사한 해에 어디서 몇 마리를 셌나 | 국가해양생태계종합조사 바닷새 관측 19,739건을 종·정점·조사연도로 묶은 것. 정점은 실제 37곳(같은 정점이 EB-01/EB01 두 형식으로 들어와 그대로 세면 72곳이라는 틀린 숫자가 나간다). 좌표는 조사지점 API 를 정점 번호로 이어 붙여  | 과거 — 조사연도별 집계 | HISTORY | ✅ GBIF 갈매기과(Laridae) |
| 해변과 낚시터 | 갈 해변이나 낚시 장소는 |  | Static catalogue with a fixed reference date — no observatio | HISTORY | 🔁 Nominatim 해변·낚시터 검색 |
| 서핑 | 이 해변에 너울이 들어오는가 | 오늘은 아무것도 나오지 않는다. 너울 방향·풍파·주기가 우리 격자에 없고, 그 값을 내던 길이 Open-Meteo Marine 직호출이었다. 해변의 **장소 목록**은 그대로 살아 있다 — 다른 현상(ocean.coastal_spots · 레이어 oc | 없음 — 내린 화면이라 시간축에 아무것도 싣지 않는다. | 없음 | ✅ Marine 지점 너울·주기·방향 판정 |
| 낚시 | 물이 얼마나 움직이고, 지금 나가면 위험한가 | 오늘은 아무것도 나오지 않는다. 물때(만조·간조 예측)가 우리 자료 어디에도 없고, 그 값을 내던 길이 Open-Meteo Marine 직호출이었다. 낚시터의 **장소 목록**은 그대로 살아 있다 — 다른 현상(ocean.coastal_spots ·  | 없음 — 내린 화면이라 시간축에 아무것도 싣지 않는다. | 없음 | ✅ Marine+바람 지점 판정 |
| 패러글라이딩 | 이 활공장의 바람과 구름 밑면은 | 오늘은 아무것도 나오지 않는다. 저층 운량·시정·CAPE 가 우리 프레임에 없고, 그 값을 내던 길이 Open-Meteo 직호출이었다. 활공장 26곳의 좌표(OSM sport=free_flying)는 자료 파일에 남아 있지만 지구에 그리지 않는다 —  | 없음 — 내린 화면이라 시간축에 아무것도 싣지 않는다. | 없음 | ✅ Open-Meteo 지점(풍속 10/80m·저층운·CAPE) 판정 + 활공장 검색 |
| 산 정상 날씨 | 정상은 여기보다 얼마나 추운가 | 기상청 산악예보 165곳을 3시간마다 요청하고 회차마다 124~145곳이 응답한다(몇 곳을 물었는지 함께 적어 \'빠진 게 아니라 아직 안 나온 것\'임을 밝힌다). 등산로는 OSM 104봉을 sac_scale 등급별로 그린다 — 우리가 매긴 등급이  | 가장 가까운 예보 시각 1개 (3시간 주기 갱신) 대 10분 실측. 발표시각·유효시각을 둘 다 남긴다. | OFFICIAL_FORECAST | ✅ Open-Meteo 고도 API + 기온 감률 (지점 카드) |

## 공통 UI 기능

| 기능 | EARTHUS | 데모 |
|---|---|---|
| 물어보기(⌕) | 메뉴·나라·시군구·도시·공항 검색 + 자연어 질문(AI 연결 시 계산) | ✅ 검색(Nominatim) + 규칙 기반 질문 응답(날씨·대기·지진·위성) |
| 지점 카드 | 실측/모델/분석 구분, 출처·시각·거리·등급, 최근 20일 강수, 지점 보고서(HTML/JSON/CSV) | ✅ 등급 배지 + 보고서 내보내기(JSON/CSV/인쇄) |
| 리포트 | 지역 리포트 | ✅ 현재 켜진 레이어·지점 값 종합 리포트(인쇄) |
| 시간 바 | 5일 예보 재생, 모든 레이어 공통 시간 | ✅ 격자 필드·레이더·위성 위치·지점 카드 연동 |
| 설정 | 언어, 구름 모드(없음/관측/천리안/모델/정적), 눈·얼음, 자동 회전, 실시간 태양 | ✅ 구름 모드(없음/관측=히마와리/모델=MODIS/정적), 눈·얼음, 자동 회전, 조명 |
| 내 위치 / 전지구로 / 내 지역 | | ✅ |
| 인구 조각 21개국 | 국가별 인구 격자 | 🔁 WorldPop 영역 통계 |
| LAB / Simulation | 해류 입자 실험, 데스크톱 앱 | ⏳ 범위 외 |

## 집계
- ✅ 구현 48 · 🔁 대체 구현 7 · 🔑 키 필요 3 · ⏳ 준비 중 20 (현상 78개 기준)
