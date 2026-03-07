# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Repository Structure

This is a **parent (wrapper) repository** containing one Git submodule:

```
TimeFlow/
├── timeflow/          ← Next.js app (the actual codebase — submodule)
│   └── CLAUDE.md      ← Detailed architecture guide for the app
├── TimeFlow_ClaudeCode_Prompts.md  ← Original 5-step build prompts (history only)
└── *.png              ← UI reference screenshots
```

**All development happens inside `timeflow/`.** Commands like `npm run dev`, `npm run build`, `npx vercel --prod` must be run from there.

---

## Commands

```bash
cd timeflow

npm run dev          # Start dev server (localhost:3000)
npm run build        # Production build — must pass before deploying
npm run typecheck    # TypeScript check without emit
npx vercel --prod    # Deploy to production (https://timeflow-nine-mu.vercel.app)
```

---

## Git Workflow (Submodule)

Changes to `timeflow/` require **two commits**: one inside the submodule, one in the parent.

```bash
# 1. Commit inside the submodule
cd timeflow
git add <files>
git commit -m "feature: ..."
git push origin master

# 2. Update parent repo's submodule pointer
cd ..
git add timeflow
git commit -m "TimeFlow submodule 최신화 (...)"
git push origin master
```

The parent repo tracks the submodule's commit SHA. If you only push inside `timeflow/` without updating the parent, the parent will point to an outdated commit.

---

## Detailed Architecture

See `timeflow/CLAUDE.md` for the full architecture reference including auth flow, Firestore data model, React Query patterns, timetable grid internals, drag & resize system, i18n, and timezone rules.

---

## 코딩 원칙 (General Coding Principles)

- **주석은 한국어로** 작성한다. 코드를 처음 보는 사람도 이해할 수 있도록 설명한다.
- 함수와 변수 이름은 **기능을 명확히 설명**하는 영어 이름을 사용한다.
- 한 함수는 **하나의 역할만** 수행한다 (Single Responsibility).
- 불필요한 `console.log`, 주석 처리된 코드(dead code), 사용하지 않는 import는 **즉시 제거**한다.
- 에러 처리는 반드시 포함한다 (`try/catch` 또는 `.catch()`).

---

## Refactoring 가이드라인

Claude Code로 리팩토링 작업을 할 때 아래 원칙을 따른다.

### 1. 불필요한 코드 제거 체크리스트

리팩토링 전, 아래 항목을 파일별로 확인한다:

- [ ] 사용되지 않는 `import` 문
- [ ] 호출되지 않는 함수 또는 변수
- [ ] 주석 처리된 오래된 코드 블록
- [ ] 중복된 로직 (같은 기능을 하는 함수가 2개 이상)
- [ ] 하드코딩된 값 (마법 숫자/문자열) → 상수로 분리
- [ ] `TODO`, `FIXME` 주석이 달린 미완성 코드

### 2. 코드 흐름 개선 원칙

- **컴포넌트 분리**: 100줄 이상의 컴포넌트는 더 작은 단위로 분리한다.
- **훅(Hook) 추출**: 반복되는 상태 로직은 커스텀 훅으로 추출한다.
- **타입 정의 통합**: 중복된 TypeScript 타입/인터페이스는 `types/` 폴더에 통합한다.
- **유틸 함수 분리**: 컴포넌트 안의 순수 함수는 `utils/` 또는 `lib/` 폴더로 이동한다.
- **상수 파일 관리**: 반복 사용되는 값은 `constants/` 파일로 분리한다.

### 3. 리팩토링 순서 (안전한 작업 흐름)

```
1단계: 현재 코드 파악  → 파일 구조 및 의존성 확인
2단계: 불필요한 코드 제거 → 빌드 테스트 (npm run build)
3단계: 로직 분리/정리   → 다시 빌드 테스트
4단계: 타입 정리        → TypeScript 체크 (npm run typecheck)
5단계: 최종 확인        → npm run dev 로 동작 확인
```

> ⚠️ 각 단계마다 반드시 빌드/타입체크를 통과한 뒤 다음 단계로 넘어간다.
> 한 번에 너무 많은 파일을 수정하지 않는다.

### 4. 리팩토링 금지 사항

- 동작 중인 기능의 **외부 동작(UI/UX)을 바꾸지 않는다** (내부 구현만 개선).
- 리팩토링과 **새 기능 추가를 동시에 하지 않는다**.
- 테스트 없이 **핵심 데이터 흐름(auth, Firestore 쿼리)을 변경하지 않는다**.

---

## Claude Code 전용 프롬프트 모음

아래 프롬프트를 Claude Code 터미널에서 복사해서 사용한다.

---

### 🔍 [분석] 불필요한 코드 탐색

```
timeflow/ 폴더 전체를 분석해줘.
다음 항목을 파일별로 정리해서 보여줘:
1. 사용되지 않는 import
2. 호출되지 않는 함수/변수
3. 주석 처리된 dead code
4. 중복된 로직
실제로 수정하지 말고, 목록만 먼저 보여줘.
```

---

### 🧹 [정리] 안전한 불필요 코드 제거

```
방금 분석한 내용 중, 제거해도 안전한 항목부터 제거해줘.
- 사용되지 않는 import 먼저 제거
- 각 파일 수정 후 npm run typecheck 실행해서 오류 없는지 확인
- 오류 발생하면 해당 파일 수정을 중단하고 나에게 알려줘
```

---

### 🏗️ [구조 개선] 컴포넌트 분리

```
timeflow/src/components/ 폴더에서
100줄이 넘는 컴포넌트 파일 목록을 보여줘.
각 파일에 대해 어떤 부분을 분리할 수 있는지 제안해줘.
실제 수정은 내가 확인한 뒤에 진행해줘.
```

---

### 🔄 [리팩토링] 특정 파일 개선

```
[파일 경로]를 리팩토링해줘.
조건:
- 외부 동작(UI/기능)은 바꾸지 말 것
- 불필요한 코드 제거
- 함수가 너무 길면 분리
- 주석을 한국어로 추가
- 수정 후 npm run build 통과 여부 확인
```

---

### 📦 [타입 정리] TypeScript 타입 통합

```
timeflow/src/ 전체에서 중복 정의된 TypeScript 타입과 인터페이스를 찾아줘.
통합할 수 있는 타입은 types/ 폴더에 모아서 정리하는 방안을 제안해줘.
```

---

### ✅ [최종 검증] 리팩토링 완료 체크

```
리팩토링이 끝났어. 아래 순서로 최종 확인해줘:
1. npm run typecheck → 타입 오류 없는지
2. npm run build → 빌드 성공하는지
3. 수정된 파일 목록과 변경 내용 요약해줘
4. 혹시 더 개선할 수 있는 부분이 있으면 제안해줘
```

---

## 작업 기록 (Change Log)

> 리팩토링 작업을 진행할 때마다 아래에 날짜와 내용을 기록한다.

| 날짜 | 작업 내용 | 담당 |
|------|----------|------|
| - | - | - |
