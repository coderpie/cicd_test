# 🚀 GitHub Actions CI/CD 파이프라인 가이드

Node.js 프로젝트를 위한 완전한 CI/CD 파이프라인입니다!

## 📋 파이프라인 구성

### 1️⃣ **린트 검사 (Lint)**
- ESLint로 코드 품질 검사
- 코드 스타일 규칙 준수 확인

### 2️⃣ **테스트 (Test)**
- Node.js 18, 20 버전에서 동시 테스트
- Jest로 유닛/통합 테스트 실행
- 코드 커버리지 리포트 생성

### 3️⃣ **빌드 (Build)**
- 린트와 테스트 통과 후 실행
- 프로젝트 빌드 및 결과물 저장
- 7일간 artifact 보관

### 4️⃣ **배포 (Deploy)**
- `main` 브랜치 푸시시에만 실행
- 빌드 완료 후 자동 배포

## 🛠️ 설정 방법

### Step 1: 워크플로우 파일 추가
```bash
# 프로젝트 루트에서
mkdir -p .github/workflows
# ci-cd.yml 파일을 .github/workflows/ 폴더에 복사
```

### Step 2: package.json 스크립트 확인
프로젝트에 다음 스크립트들이 있는지 확인하세요:

```json
{
  "scripts": {
    "lint": "eslint src --ext .js",
    "test": "jest",
    "test:coverage": "jest --coverage",
    "build": "babel src -d dist"  // 또는 다른 빌드 명령
  }
}
```

### Step 3: ESLint 설정
`.eslintrc.js` 또는 `.eslintrc.json` 파일이 필요합니다:

```javascript
module.exports = {
  env: {
    node: true,
    es2021: true,
  },
  extends: 'eslint:recommended',
  parserOptions: {
    ecmaVersion: 'latest',
  },
};
```

### Step 4: Jest 설정 (선택사항)
`jest.config.js`:

```javascript
module.exports = {
  testEnvironment: 'node',
  coverageDirectory: 'coverage',
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/**/*.test.js',
  ],
};
```

## 🎯 배포 설정 추가하기

### AWS S3 배포 예시
```yaml
- name: AWS 배포
  uses: jakejarvis/s3-sync-action@master
  with:
    args: --follow-symlinks --delete
  env:
    AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
    AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
    AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

### Vercel 배포 예시
```yaml
- name: Vercel 배포
  uses: amondnet/vercel-action@v25
  with:
    vercel-token: ${{ secrets.VERCEL_TOKEN }}
    vercel-org-id: ${{ secrets.ORG_ID }}
    vercel-project-id: ${{ secrets.PROJECT_ID }}
```

### Heroku 배포 예시
```yaml
- name: Heroku 배포
  uses: akhileshns/heroku-deploy@v3.12.14
  with:
    heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
    heroku_app_name: "your-app-name"
    heroku_email: "your-email@example.com"
```

### Docker Hub 배포 예시
```yaml
- name: Docker 빌드 및 푸시
  uses: docker/build-push-action@v5
  with:
    push: true
    tags: user/app:latest
```

## 🔐 GitHub Secrets 설정

1. GitHub 저장소 → **Settings** → **Secrets and variables** → **Actions**
2. **New repository secret** 클릭
3. 필요한 시크릿 추가:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `VERCEL_TOKEN`
   - `HEROKU_API_KEY`
   - 기타 배포에 필요한 인증 정보

## ✅ 체크리스트

- [ ] `.github/workflows/ci-cd.yml` 파일 추가
- [ ] `package.json`에 필요한 스크립트 추가
- [ ] ESLint 설정 파일 생성
- [ ] Jest 테스트 설정 (테스트가 있다면)
- [ ] 배포 스텝 추가 (필요시)
- [ ] GitHub Secrets 설정
- [ ] 코드 푸시하고 Actions 탭에서 확인!

## 🔍 파이프라인 동작 방식

```
┌─────────────┐
│  Push/PR    │
└──────┬──────┘
       │
       ├──────────────┐
       │              │
   ┌───▼────┐    ┌───▼────┐
   │  Lint  │    │  Test  │
   └───┬────┘    └───┬────┘
       │              │
       └──────┬───────┘
              │
         ┌────▼────┐
         │  Build  │
         └────┬────┘
              │
         ┌────▼────┐
         │ Deploy  │ (main 브랜치만)
         └─────────┘
```

## 💡 팁

1. **브랜치 전략**: `develop` 브랜치에서 개발, `main`에서 배포
2. **PR 리뷰**: Pull Request마다 자동으로 린트/테스트 실행
3. **캐싱**: `npm ci`와 함께 캐싱으로 빌드 속도 향상
4. **멀티 버전**: Node.js 여러 버전에서 테스트로 호환성 보장
5. **Artifact**: 빌드 결과물을 저장해서 나중에 다운로드 가능

## 📊 모니터링

GitHub Actions 탭에서 모든 워크플로우 실행 결과를 확인할 수 있습니다:
- ✅ 성공한 작업
- ❌ 실패한 작업
- ⏱️ 실행 시간
- 📝 상세 로그

## 🆘 문제 해결

**린트 실패**: `npm run lint:fix`로 자동 수정
**테스트 실패**: 로컬에서 `npm test` 실행해서 확인
**빌드 실패**: `npm run build` 로컬 테스트
**배포 실패**: Secrets 설정과 권한 확인

---

궁금한 점이 있으면 언제든 물어보세요! 🚀
