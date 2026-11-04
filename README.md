<div align="center">

# 🌋 가고시마 여행 플래너

**트리플이 사라진 뒤, 우리 둘이 쓰려고 만든 여행 앱**
일정 · 지도 · 가계부 · 항공 · 메모 · 체크리스트를 한 파일에, 실시간 공유까지

![Single File](https://img.shields.io/badge/single-index.html-2f6fed)
![No Build](https://img.shields.io/badge/build-none-brightgreen)
![Vanilla JS](https://img.shields.io/badge/JS-vanilla-f7df1e)
![Firebase](https://img.shields.io/badge/sync-Firebase%20RTDB-ffca28)
![Leaflet](https://img.shields.io/badge/map-Leaflet-199900)

📅 **2027.02.06 (토) – 02.09 (화)** · 📍 일본 가고시마

</div>

---

## ✨ 주요 기능

| 탭            | 기능                                                                                                                                |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| 🗺 **일정**   | 구글맵 링크·주소·장소명 입력 → 지도에 번호 마커와 점선 경로 표시. ☰ 드래그 또는 ▲▼로 순서 변경, 날짜(Day1~4)별 관리, 직선거리 합계 |
| ✈ **항공**    | 편명·공항코드·출발/도착 시간·기종 저장. 공항코드 입력 시 공항명 자동 표시                                                           |
| 💰 **가계부** | JPY/KRW 입력, 100엔당 환율로 원화 환산, 카테고리별 합계, 총 예산 대비 남은 금액                                                     |
| ✅ **체크**   | 준비물 체크리스트 (여권, VJW, 어댑터, 환전, eSIM 등 기본 포함)                                                                      |
| 📝 **메모**   | 제목 없이 간단히 작성, 수정·삭제 가능                                                                                               |
| 👥 **공유**   | Firebase Realtime Database로 일행과 실시간 동기화, 링크 하나로 접속                                                                 |

그 밖에 JSON 백업/불러오기, 모바일 화면 대응, 홈 화면 추가(앱처럼 사용)를 지원합니다.

---

## 🧱 구조

```
index.html   ← 모든 것이 이 파일 하나 (HTML + CSS + JS)
```

- 빌드 도구 없음. 파일을 고치고 다시 올리면 끝.
- 지도: [Leaflet](https://leafletjs.com) + Esri World Street Map (API 키 불필요, OSM·위성 전환 가능)
- 장소 검색: OpenStreetMap Nominatim 지오코딩
- 저장: 브라우저 `localStorage` + (선택) Firebase 동기화

---

## 🚀 시작하기

### 1. 날짜 바꾸기

`index.html` 스크립트 맨 위에서 수정합니다.

```js
const START = "2027-02-06",
  NDAYS = 4;
```

### 2. Firebase 설정 (일행과 공유할 때)

1. [Firebase 콘솔](https://console.firebase.google.com)에서 프로젝트 생성 (Analytics는 꺼도 됨)
2. **Build → Realtime Database → 데이터베이스 만들기** (테스트 모드)
3. **규칙** 탭을 아래로 바꾸고 **게시** — 테스트 모드는 약 30일 뒤 만료되므로 꼭 필요합니다.

```json
{
  "rules": {
    "rooms": {
      "$room": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

4. 앱의 **공유** 탭에 데이터베이스 URL을 붙여넣고 **연결**
5. 생성된 **공유 링크**를 일행에게 전달 — 링크를 열기만 하면 같은 방에 자동 접속

> 방 코드를 아는 사람은 누구나 수정할 수 있습니다. 링크는 일행에게만 공유하세요.

### 3. 배포 (Netlify Drop)

1. [app.netlify.com/drop](https://app.netlify.com/drop)에 `index.html` 드래그
2. **Claim this site**로 무료 계정에 연결 (안 하면 1시간 뒤 삭제)
3. 수정 후 재배포할 때는 **기존 프로젝트의 Deploys / "Already built it?" 영역**에 파일을 다시 드롭 → 주소 유지, 데이터 유지

> ⚠️ 드롭 첫 화면(app.netlify.com/drop)에 올리면 **새 프로젝트와 새 주소**가 생깁니다.

### 4. 폰에 앱처럼 설치

- **iPhone (Safari)**: 공유 → 홈 화면에 추가
- **Android (Chrome)**: ⋮ → 홈 화면에 추가 / 앱 설치

---

## 🔄 동기화 방식

- 변경할 때마다 전체 상태를 `rooms/{방코드}.json`에 저장 (PUT)
- 4초마다 서버를 확인해 더 최신(`ts`)이면 화면 갱신
- 입력 중인 칸이 있으면 덮어쓰지 않음
- **동시에 수정하면 나중에 저장한 쪽이 이깁니다** (항목 단위 병합은 아님)

---

## 💡 사용 팁

- 좌표가 포함된 **긴 구글맵 링크**가 가장 정확합니다. `maps.app.goo.gl` 단축링크는 좌표를 읽지 못하니 주소나 장소명으로 입력하세요.
- 항공편은 예약 내역의 공항코드(ICN, KOJ 등)와 현지 시각을 그대로 입력하세요. 오는 편은 한 번 더 저장합니다.
- 환율은 가계부 탭에서 직접 입력합니다 (예: 100엔 = 920원).
- 여행 직전에 **JSON 백업**을 한 번 받아 두면 안심입니다.

---

## 🛠 알려진 한계

- 항공편 자동 조회(API)는 현재 버전에서 제외 — 수동 입력 방식
- 지도 경로는 직선 연결이며 실제 도보·대중교통 경로가 아님
- Firebase 규칙이 공개 상태라 보안이 강하지 않음 (여행 후 프로젝트 삭제 권장)
- 동시 편집 충돌 시 마지막 저장본이 우선

---

## 🗂 라이선스·출처

개인 여행용 프로젝트입니다. 지도 타일 © Esri, 지도 데이터 © OpenStreetMap contributors, 지도 라이브러리 © Leaflet.

<div align="center">

**즐거운 가고시마 여행 🌸♨️🌋**

</div>
