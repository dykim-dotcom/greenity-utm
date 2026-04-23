# Greenity Corp UTM 생성기

마케팅팀 및 외부 알바와 공유하는 UTM 파라미터 자동 생성 도구입니다.

---

## 1. Supabase 프로젝트 생성

1. [https://supabase.com](https://supabase.com) 에서 무료 계정 생성 및 로그인
2. **New Project** 클릭 → 프로젝트명 입력 (예: `greenity-utm`) → 비밀번호 설정 → 리전 선택 (Northeast Asia 권장) → **Create new project**
3. 프로젝트 생성 완료 후 좌측 메뉴 **Settings → API** 이동
4. 아래 두 값을 복사해둡니다:
   - **Project URL** (예: `https://xxxxxxxxxxxx.supabase.co`)
   - **anon public key** (Project API keys 섹션)

---

## 2. utm_codes 테이블 생성

Supabase 대시보드 좌측 메뉴 **SQL Editor** → **New query** → 아래 SQL 붙여넣기 후 실행:

```sql
CREATE TABLE utm_codes (
  id          BIGSERIAL PRIMARY KEY,
  field_key   TEXT NOT NULL,
  label       TEXT NOT NULL,
  value       TEXT NOT NULL,
  sort_order  INTEGER DEFAULT 0,
  created_at  TIMESTAMPTZ DEFAULT NOW()
);

-- 인덱스 (검색 성능 향상)
CREATE INDEX idx_utm_codes_field_key ON utm_codes(field_key, sort_order);

-- RLS 비활성화 (팀 내부 도구이므로 공개 접근 허용)
ALTER TABLE utm_codes DISABLE ROW LEVEL SECURITY;
```

> 초기 데이터(드롭다운 항목)는 앱 첫 실행 시 자동으로 삽입됩니다.

---

## 3. index.html CONFIG 값 입력

`index.html` 파일 상단 `<script>` 블록에서 아래 부분을 찾아 값을 입력합니다:

```javascript
const CONFIG = {
  supabaseUrl: 'YOUR_SUPABASE_URL',     // Supabase Project URL로 교체
  supabaseKey: 'YOUR_SUPABASE_ANON_KEY' // anon public key로 교체
};
```

예시:
```javascript
const CONFIG = {
  supabaseUrl: 'https://abcdefghij.supabase.co',
  supabaseKey: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...'
};
```

---

## 4. GitHub 업로드

```bash
# 1. GitHub에서 새 레포지토리 생성 (예: greenity-utm)

# 2. 로컬에서 초기화 및 업로드
cd "C:\coding\12. 광고세팅"
git init
git add .
git commit -m "init: UTM 생성기"
git remote add origin https://github.com/[계정명]/greenity-utm.git
git push -u origin main
```

---

## 5. Vercel 배포

1. [https://vercel.com](https://vercel.com) 로그인 (GitHub 계정 연동 권장)
2. **Add New... → Project** 클릭
3. GitHub 레포지토리 목록에서 `greenity-utm` 선택 → **Import**
4. Framework Preset: **Other** 선택
5. Root Directory: `.` (기본값 유지)
6. **Deploy** 클릭
7. 배포 완료 후 자동 생성된 URL 확인 (예: `https://greenity-utm.vercel.app`)

> `vercel.json` 파일이 자동으로 정적 파일 배포 설정을 적용합니다.

---

## 6. 팀 공유

배포 완료 후 생성된 Vercel URL을 팀원 및 외부 마케팅 알바에게 공유하면 됩니다.

- **설정 탭**에서 드롭다운 항목을 추가/수정/삭제하면 Supabase를 통해 **모든 사용자에게 즉시 반영**됩니다.
- **히스토리**는 각 사용자의 브라우저(localStorage)에 개인 저장됩니다.
- 별도 로그인 없이 URL만으로 접근 가능합니다.

---

## UTM 네이밍 규약

| 파라미터 | 구성 |
|---|---|
| utm_source | 채널 코드 (예: meta) |
| utm_medium | 광고상품 코드 (예: conv) |
| utm_campaign | 운영주체_브랜드_채널_프로모션구분_캠페인유형_광고상품_전환목표_OS |
| utm_content | 브랜드_프로모션_지면_제품_타겟팅방식_디타겟-연령-성별_기기-지역 |
| utm_term | 브랜드_제품_프로모션_지면_소재형태_핵심USP_오브제특징_일자 |
