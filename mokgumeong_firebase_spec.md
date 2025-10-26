# 목구멍(韓式 BBQ) 브랜드 웹페이지 Firebase AI 개발 명세서

## 🔧 프로젝트 개요
- **Framework:** Next.js (React + TypeScript)
- **Styling:** TailwindCSS
- **Backend:** Firebase (Hosting, Firestore, Functions, Auth, Storage)
- **AI Chatbot:** Placeholder at `/api/chat` (custom API to be connected later)
- **Language:** Korean (ko)
- **Responsive:** true

---

## 📂 프로젝트 구조
```plaintext
root/
 ├─ src/
 │   ├─ pages/
 │   │   ├─ index.tsx (Home)
 │   │   ├─ brand.tsx
 │   │   ├─ menu.tsx
 │   │   ├─ locations.tsx
 │   │   ├─ franchise.tsx
 │   │   └─ support/
 │   │       └─ faq.tsx
 │   ├─ components/
 │   │   ├─ Header.tsx
 │   │   ├─ Footer.tsx
 │   │   ├─ ChatbotWidget.tsx
 │   │   └─ Card.tsx
 │   └─ styles/
 │       └─ globals.css
 ├─ functions/
 │   ├─ index.js
 │   └─ api/
 │       └─ chat.js (AI API proxy)
 ├─ firestore.rules
 ├─ firebase.json
 ├─ .firebaserc
 ├─ package.json
 └─ README.md
```

---

## 🗂️ Firestore 컬렉션 구조
```yaml
collections:
  branches:
    fields: [name, region, address, geo(lat,lng), phone, hours, facilities, parking, featured]
  menuItems:
    fields: [nameKR, nameEN, category, priceKRW, desc, origin, allergy[], spicyLevel, photos[], isSeasonal, isBest]
  events:
    fields: [title, type, period, branches[], content, banner]
  news:
    fields: [title, summary, body, publishedAt, ogImage]
  franchiseApplications:
    fields: [name, phone, email, capitalRange, preferredRegion, experience, agreePrivacy, status, createdAt]
  faqs:
    fields: [category, q, a, weight]
  seo:
    fields: [route, title, desc, keywords, og]
```

---

## 🔐 Firestore 보안 규칙 (예시)
```js
match /databases/{database}/documents {
  match /{document=**} {
    allow read: if true;
    allow write: if request.auth != null && request.auth.token.role in ['admin', 'editor'];
  }
  match /franchiseApplications/{appId} {
    allow create: if request.resource.data.agreePrivacy == true;
  }
}
```

---

## ⚙️ Firebase Functions
```js
exports.api = functions.https.onRequest(app);

exports.onFranchiseCreate = functions.firestore
  .document('franchiseApplications/{appId}')
  .onCreate((snap, context) => {
    const data = snap.data();
    // TODO: Send Slack or email alert
  });

exports.chatProxy = functions.https.onRequest(async (req, res) => {
  const { message } = req.body;
  // Placeholder - connect your AI API here
  res.json({ reply: `Echo: ${message}` });
});
```

---

## 🌐 Firebase Hosting 설정
```json
{
  "hosting": {
    "public": "dist",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [{ "source": "**", "destination": "/index.html" }]
  }
}
```

---

## 🧩 챗봇 위젯 Placeholder
```html
<div id="mgm-chatbot" data-api-base="/api/chat" data-brand="목구멍"></div>
<script>
  window.MGM_CHATBOT_CONFIG = {
    apiBase: "/api/chat",
    brand: "목구멍",
    privacyNotice: true,
    leadCapture: true,
  };
</script>
```

---

## 📱 반응형 브레이크포인트
```yaml
mobile: max-width 640px
tablet: 641px - 1024px
desktop: 1025px - 1440px
wide: min-width 1441px
```

---

## 🧭 페이지 및 섹션 정의
```yaml
pages:
  home:
    sections:
      - hero: {title: "불의 깊이를 담다, 목구멍의 BBQ", cta: ["매장찾기", "가맹문의"]}
      - menuHighlight: 6개 대표 메뉴
      - brandTeaser
      - mapSnippet
      - snsFeed
      - chatbotButton
  brand:
    sections: [미션, 비전, 철학, 연혁, 수상]
  menu:
    sections: [카테고리별 메뉴, 원산지/알레르기 표기]
  locations:
    sections: [지도, 검색 필터, 상세정보]
  franchise:
    sections: [비용 구조표, 지원 항목, 신청 폼]
  support/faq:
    sections: [FAQ 리스트, 문의 폼]
```

---

## 🚀 Firebase CLI 명령어
```bash
firebase login
firebase init
firebase deploy --only hosting
firebase deploy --only functions
firebase deploy --only firestore
firebase emulators:start
```

---

## 📊 GA4 이벤트 표준
```yaml
events:
  - view_item
  - select_location
  - call_store
  - lead_submit
  - chatbot_message
  - reservation_submit
```

---

## ✅ 배포 단계 요약
1. Firestore 구조 생성
2. Functions (chatProxy, franchise trigger) 배포
3. Hosting 빌드 & 배포
4. 챗봇 API 연결
5. GA4 / Search Console 등록
