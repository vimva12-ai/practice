# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

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

## Commands

```bash
cd timeflow

npm run dev          # Start dev server (localhost:3000)
npm run build        # Production build — must pass before deploying
npm run typecheck    # TypeScript check without emit
npx vercel --prod    # Deploy to production (https://timeflow-nine-mu.vercel.app)
```

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

## Detailed Architecture

See `timeflow/CLAUDE.md` for the full architecture reference including auth flow, Firestore data model, React Query patterns, timetable grid internals, drag & resize system, i18n, and timezone rules.
