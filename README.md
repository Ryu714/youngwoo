# 영우회 계모임 공유 페이지

계모임 참가자에게 **일정 · 숙소 · 참가현황 · 정산내역**을 카톡으로 공유하기 위한 모바일 우선 정적 페이지입니다.

- 참가자는 **열람만** 합니다. (편집/로그인/댓글 없음)
- 총무는 **구글 시트만 수정**하면 페이지가 자동 갱신됩니다. (재배포 불필요)
- 카카오톡 인앱 브라우저에서 잘 보이도록 만들어졌습니다.

## 구조

```
index.html      ← 페이지 본체 (CSS/JS 인라인, 프레임워크·빌드도구 없음)
SHEET_SETUP.md  ← 구글 시트 탭/열 구조 + 웹게시 CSV URL 얻는 법
README.md       ← 이 문서 (배포/설정)
og.png          ← (직접 추가) 카톡 썸네일 이미지 1200×630
```

**데이터와 뷰 분리:**

```
구글 시트(명단/지출 탭)  ──웹게시 CSV──▶  index.html 이 fetch  ──▶  화면 렌더링
```

CSV URL이 비어있거나 fetch에 실패하면 `index.html` 안의 **기본(fallback) 데이터**로 조용히 렌더링되므로 화면이 깨지지 않습니다.

---

## 1. 구글 시트 연결

`SHEET_SETUP.md` 를 따라 시트 2개 탭(`명단`, `지출`)을 만들고 웹게시 CSV URL 2개를 얻으세요.
그다음 `index.html` 상단 `CONFIG`에 넣습니다.

### CSV URL 교체 위치

`index.html` 의 `<script>` 상단 `CONFIG`:

```js
const CONFIG = {
  ROSTER_CSV_URL:  "",   // ← 여기에 '명단' 탭 CSV URL
  EXPENSE_CSV_URL: "",   // ← 여기에 '지출' 탭 CSV URL
  ...
};
```

> 비워두면 fallback 데이터로 동작합니다. 시트 없이 바로 테스트해봐도 됩니다.

### 정산 섹션 토글

같은 `CONFIG` 안:

```js
SHOW_SETTLEMENT: false,   // 모임 전: 정산 섹션 전체 숨김
                          // 모임 후: true 로 바꾸면 정산 섹션 노출
```

기타 바꿀 수 있는 값: `EVENT_START`(D-day 기준일), `LODGING_NAME`, `LODGING_ADDRESS`(지도 정확도용, 알게 되면 채우기), `AIRBNB_URL`.

---

## 2. GitHub Pages 배포

1. GitHub에서 새 저장소(repo)를 만듭니다. (예: `youngwoo-meetup`)
2. `index.html`, `SHEET_SETUP.md`, `README.md`, (있으면) `og.png` 를 저장소에 올립니다(push).
3. 저장소 **Settings ▸ Pages** 이동
4. **Source**: `Deploy from a branch`
5. **Branch**: `main` / 폴더 `/ (root)` 선택 → **Save**
6. 잠시 후 `https://<사용자명>.github.io/<저장소명>/` 주소가 생성됩니다.

배포 후 `index.html`의 OG `og:url` 을 실제 주소로 바꿔주면 카톡 미리보기가 더 정확해집니다.

### 카톡 썸네일 (og.png)

- 카톡에 링크를 붙였을 때 뜨는 이미지입니다. **1200×630** 크기 권장.
- `index.html` 과 같은 폴더에 `og.png` 로 올리면 자동 참조됩니다.
- 없어도 페이지 기능엔 지장 없습니다(썸네일만 안 뜸).
- 카톡은 미리보기를 캐시합니다. 바꿔도 즉시 반영 안 되면 URL 끝에 `?v=2` 등을 붙여 공유해보세요.

---

## 3. 운영 팁

- **시트 수정 → 페이지 반영까지 최대 5분** (웹게시 CSV 캐시). 페이지 하단에도 안내돼 있습니다.
- 지출 행은 얼마든지 추가하세요. 합계·환급 목록은 자동 계산됩니다.
- 환급이 끝나면 `지출` 탭 `상태` 칸에 `완료` 를 적으면 "환급 완료" 뱃지가 뜹니다.
- 로컬에서 미리보기: `index.html` 을 브라우저로 그냥 열면 됩니다. (fallback 데이터로 표시)
  - 단, 실제 CSV fetch는 `file://` 에서 CORS로 막힐 수 있으니 URL 연결 테스트는 배포 후 확인하세요.
