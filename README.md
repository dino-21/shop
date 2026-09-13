# 🍎 과일농장 쇼핑몰 (shop)

React + Redux Toolkit + React Router 기반으로 제작된 **과일/채소 쇼핑몰** 예제 프로젝트입니다.
`Create React App`으로 빌드되어 GitHub Pages(`/shop` 서브 경로)에 배포되었습니다.

> 원본 저장소: https://github.com/sinaboro/shop

---

## 1. 기술 스택

| 구분 | 사용 기술 |
|---|---|
| UI 라이브러리 | React 18 (`react-dom/client`, `createRoot`) |
| 라우팅 | React Router v6 (`BrowserRouter`, `Routes`, `Route`, `Outlet`) |
| 상태관리 | Redux Toolkit (`configureStore`, `createSlice`) + `react-redux` |
| UI 컴포넌트 | React-Bootstrap + Bootstrap 5.1.3 (CDN) |
| 통신 | axios (외부 JSON 데이터 비동기 로딩) |
| 빌드 도구 | Create React App (Webpack) |

---

## 2. 폴더 구조 (소스맵으로 복원한 실제 구조)

> ⚠️ 이 저장소에는 빌드 결과물(`static/`, `index.html`)만 올라와 있고 `src` 폴더와 `package.json`은 포함되어 있지 않습니다.
> 아래 구조는 `static/js/*.js.map`에 포함된 원본 소스(`sourcesContent`)를 추출하여 복원한 내용입니다.

```
src/
├── index.js              # 앱 진입점 (Redux Provider + BrowserRouter 설정)
├── App.js                # 최상위 컴포넌트, 라우팅 및 네비게이션 바
├── store.js              # Redux Toolkit 스토어 (user, cart 슬라이스)
├── db/
│   ├── fruit.js          # 과일 상품 목업 데이터 (9종)
│   └── veggie.js         # 채소 상품 목업 데이터 (3종, 기본 노출분)
└── components/
    ├── Title.js          # 메인 페이지 - 과일 섹션 타이틀
    ├── Title2.js         # 메인 페이지 - 채소 섹션 타이틀
    ├── Products.js       # 과일 상품 카드 (상세페이지 이동)
    ├── ComVeggie.js       # 채소 상품 카드
    ├── Detail.js         # 상품 상세 페이지 (탭 UI + 장바구니 담기)
    ├── Cart.js           # 장바구니 페이지 (수량 증감/삭제/정렬)
    ├── About.js           # 회사소개 (중첩 라우트 Outlet)
    ├── Member.js          # 회사소개 하위 - 멤버 페이지
    ├── Loacation.js       # 회사소개 하위 - 위치 페이지 (파일명 오타: Location)
    ├── NotFound.js         # 404 페이지
    └── Footer.js           # 공통 푸터
```

---

## 3. 라우팅 구조

`App.js`에 정의된 라우트는 다음과 같습니다.

| 경로 | 컴포넌트 | 설명 |
|---|---|---|
| `/` | 메인(인라인 JSX) | 슬라이더, 과일/채소 상품 목록, 정렬 버튼 |
| `/detail/:paramId` | `Detail` | 선택한 상품의 상세 정보 + 장바구니 담기 |
| `/cart` | `Cart` | 장바구니 목록, 수량 변경, 이름순 정렬 |
| `/about` | `About` | 회사소개 (하위 라우트 포함) |
| `/about/member` | `Member` | 회사 구성원 소개 (중첩 라우트) |
| `/about/location` | `Location` | 오시는 길 (중첩 라우트) |
| `*` | `NotFound` | 존재하지 않는 경로 처리 (404) |

> `BrowserRouter`에 `basename="/shop"`이 지정되어 있어, GitHub Pages 배포 경로(`/shop/...`)에 맞춰 라우팅됩니다.

---

## 4. 주요 기능

### 🏠 메인 페이지 (`/`)
- 상단 배너(슬라이더 이미지) 및 타이틀 영역
- **과일 정렬 기능**: 이름순 / 낮은가격순 / 높은가격순 정렬 버튼 (`Array.sort` 활용)
- **채소 더보기 기능**: `+ 3개 상품 더 보기` 버튼 클릭 시 `axios.get()`으로 외부 JSON
  (`https://sinaboro.github.io/react_data/veggie2.json`, `veggie3.json`)을 순차적으로 불러와
  기존 목록에 이어붙이는 **페이지네이션(더보기) 패턴** 구현
  - `count` 상태값으로 몇 번째 "더보기"인지 관리하고, 3번째부터는 `alert()`로 안내

### 🔍 상세 페이지 (`/detail/:paramId`)
- URL 파라미터(`useParams`)로 상품 ID를 받아 해당 상품 정보를 조회
- 존재하지 않는 상품일 경우 방어 처리 (`해당 상품이 존재하지 않습니다.`)
- `useEffect`를 이용한 이미지 **페이드인 애니메이션** (`fade2` 상태)
- 탭(버튼0/1/2) 전환 시에도 `useEffect`로 페이드인 효과 재적용
- **주문하기** 버튼 클릭 시 Redux `addItem` 액션을 `dispatch`하여 장바구니에 상품 추가

### 🛒 장바구니 페이지 (`/cart`)
- `useSelector`로 Redux 스토어의 `cart` 상태를 구독
- 상품별 **수량 증가(`addCount`) / 감소(`decreaseCount`) / 삭제(`deleteItem`)** 기능
- **이름순 정렬(`sortName`)** 기능
- 상품 이미지를 클릭하면 해당 상품의 상세 페이지로 재이동

### 🏢 회사소개 페이지 (`/about`)
- `Outlet`을 이용한 **중첩 라우팅(Nested Route)** 예제 (`/about/member`, `/about/location`)

---

## 5. 상태관리 (Redux Toolkit)

`store.js`는 2개의 슬라이스로 구성되어 있습니다.

```js
configureStore({
  reducer: { user: user.reducer, cart: cart.reducer },
});
```

- **`user` 슬라이스**: 사용자 이름/나이 상태 (`changeName`, `increase` 액션 정의)
- **`cart` 슬라이스**: 장바구니 배열 상태
  - `addCount`: 수량 +1
  - `decreaseCount`: 수량 -1 (0 이하일 경우 `alert` 처리 — 단, 현재 export 목록에는 누락되어 있어 import 시 확인 필요)
  - `addItem`: 이미 담긴 상품이면 수량 +1, 없으면 새로 추가
  - `deleteItem`: 상품 삭제
  - `sortName`: 이름순 정렬

> 💡 **학습 포인트**: `createSlice`는 immer를 내장하고 있어 `state.push()`, `state[num].count++`처럼
> 불변성을 직접 신경 쓰지 않고도 상태를 "직접 수정하는 것처럼" 작성할 수 있습니다.

---

## 6. 데이터 구조

**`db/fruit.js`** — 과일 9종 (수박, 참외, 사과, 바나나, 딸기, 오렌지, 토마토, 포도, 망고)
**`db/veggie.js`** — 채소 3종 (당근, 옥수수, 감자), 이후 외부 JSON으로 추가 로딩

각 상품 객체 공통 스키마:
```js
{ id: 1, title: "수박", imgUrl: "img/fruit1.jpg", content: "설명 텍스트", price: 29000 }
```

---

## 7. 실행 방법 (원본 소스 기준 참고)

> 이 저장소에는 빌드 산출물만 있으므로, 아래는 일반적인 CRA 프로젝트 기준 실행 방법입니다.

```bash
npm install
npm start          # 개발 서버 실행 (http://localhost:3000)
npm run build       # 배포용 빌드 생성 (build/ 폴더)
```

필요 패키지 (추정): `react`, `react-dom`, `react-router-dom`, `react-redux`, `@reduxjs/toolkit`, `react-bootstrap`, `bootstrap`, `axios`

---

## 8. 알아두면 좋은 코드 상의 이슈 (수업용 참고)

- `Loacation.js` 파일명에 오타가 있습니다 (정상 표기는 `Location`).
- `Cart.js`에서 `decreaseCount`를 import 하지만, `store.js`의 export 구문(`addCount, decreaseCount, addItem, deleteItem, sortName`)에는 있지만 실제 `reducers` 객체 정의에서는 주석 안에 문자열로 포함되어 있어 정상 동작 여부를 확인해볼 필요가 있습니다 (학생들이 직접 디버깅해보기 좋은 포인트).
- 이미지 경로 참조 시 `process.env.PUBLIC_URL`을 사용하는 곳과 `"/shop/" + imgUrl`처럼 하드코딩한 곳이 혼재되어 있어, 배포 경로 변경 시 일관성 있게 리팩터링해보는 실습이 가능합니다.

---

## 9. 배포 환경

- GitHub Pages 배포 (base path: `/shop`)
- `favicon.ico`, `manifest.json`, `robots.txt` 등 CRA 기본 PWA 관련 파일 포함
