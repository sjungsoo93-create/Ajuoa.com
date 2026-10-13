# 아주오에이랜드 홈페이지 — 설치사례·관리자·검색 개선 V4

2026-10-13 수정본입니다. 설치사례 게시판, 관리자 로그인·게시판별 글쓰기·사진 업로드, 검색 노출 구조를 개선한 전체 프로젝트입니다. 승인된 히어로는 그대로 보존합니다. 이전 ZIP도 별도로 보관합니다.

- 소스 커밋: `b8895a0b26beebbd91a836382eaba28dbe4d51cc`
- 홈페이지: https://ajuoa-land.calmkiwi4.chatgpt.site
- 관리자 로그인: https://ajuoa-land.calmkiwi4.chatgpt.site/login
- 관리자: https://ajuoa-land.calmkiwi4.chatgpt.site/admin
- 설치사례: https://ajuoa-land.calmkiwi4.chatgpt.site/cases
- 관리자 사용법: `docs/ADMIN_GUIDE.md`
- 실제 도메인 연결·검색 검토: `docs/DOMAIN_AND_SEO.md`
- 변경 기준·참조 출처: `docs/IMAGE_GUIDELINES.md`, `image-prompts.json`

## 포함 내용

앱 전체 소스, 관리자·문의 기능, DB 스키마와 마이그레이션, 초기 제품·자료 데이터, 공식 PDF 5종, 표지 썸네일, 웹 이미지, AI 이미지 원본 7장(1차 4장, 2차 2장, 이번 서비스 수정 1장), 공식 제품 참조 사진, 배포 파일, 설치·검증 설정을 포함합니다.

`node_modules`, 캐시, 로그, `.git`, 인증 토큰, 운영 비밀값은 제외합니다. 운영 중 접수된 고객 문의와 관리자 추가 업로드 파일은 포함하지 않습니다. DB **구조와 초기 게시글**이 포함된 코드 저장소용 패키지입니다.

## 저장소 업로드

압축을 풀고 `ajuoa-land-v4-cases-admin` 폴더 내부 전체를 저장소 루트에 넣으세요. `.gitignore`, `.openai` 등 점으로 시작하는 프로젝트 설정도 필요합니다.

```sh
git init
git add .
git commit -m "아주오에이랜드 홈페이지 - 설치사례 및 관리자 기능"
git branch -M main
git remote add origin <내-저장소-주소>
git push -u origin main
```

`<내-저장소-주소>`는 실제 Git 저장소 주소로 바꾸세요.

## 로컬 실행

Node.js 24 계열, pnpm 11.25.0 기준입니다.

```sh
npm install -g pnpm@11.25.0
pnpm install --frozen-lockfile
```

`.dev.vars.example`을 `.dev.vars`로 복사한 후 아래 명령을 실행하세요. 첫 SQL 명령은 새 로컬 DB에 한 번만 적용합니다.

```sh
pnpm build
node --import ./scripts/sites-env.mjs ./node_modules/wrangler/bin/wrangler.js d1 execute DB --local --config dist/server/wrangler.json --persist-to .wrangler/state --file drizzle/0000_organic_lightspeed.sql
pnpm dev
```

로컬 주소: http://localhost:5173

관리자 테스트 로그인: http://localhost:5173/signin-with-chatgpt?return_to=%2Fadmin

로컬 테스트 계정 `seedy@sites.test`는 예시 설정과 일치합니다. 개발용 로그인이며 운영 인증을 대체하지 않습니다. 깨끗한 복사본은 portable 실행 모드를 사용합니다. 원래 서버의 `.sites-runtime` 폴더를 복사하지 마세요.

## 검증과 배포

```sh
pnpm exec tsc --noEmit
node tests/api.test.mjs
pnpm build
```

이번 기능 수정은 타입 검사·프로덕션 빌드 및 API·권한·갤러리·검색 관련 61개 검증을 거쳤습니다. 브라우저에서의 시각·모바일 조작 검수는 수행하지 않았습니다. 기존 API 테스트 내용은 `README.md`에 있습니다. 내보내기는 Git 원본 일치, ZIP CRC 및 파일별 SHA-256을 확인합니다.

운영은 Vinext/React와 Cloudflare Workers, D1 `DB`, R2 `BUCKET`을 사용합니다. `ADMIN_EMAILS`, `SITE_URL`, `PUBLIC_INDEXING`과 플랫폼이 검증한 인증이 필요합니다. GitHub 업로드만으로 운영 서버·DB·인증이 자동 설치되지는 않습니다. 다른 호스팅에 옮길 때는 해당 환경의 저장소와 인증을 함께 구성하세요. 요청자가 보낸 인증 헤더를 무조건 신뢰하지 마세요.

`.openai/hosting.json`은 현재 Site를 가리킵니다. 동일 Site 관리 시 유지하고, 새 Site라면 정식 생성 절차에서 받은 식별 정보로 설정해야 합니다. `release/`는 이번 배포 빌드의 보관용 묶음이며 다른 호스팅에서는 대상 환경에 맞춰 소스를 다시 빌드하세요.

제품소개·자료실의 공식 PDF와 썸네일은 제조사 자료입니다. 서비스 이미지는 실제 설치·수리 사례가 아닌 AI 연출 이미지입니다. 공식 참조 사진·상표·PDF의 권리는 해당 권리자에게 있습니다.
