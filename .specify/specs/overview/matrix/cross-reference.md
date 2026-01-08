# Cross Reference Matrix

> ⚠️ **AUTO-GENERATED** from `cross-reference.json`. Do not edit directly.
>
> Generated: 2026-01-08
> Source: `.specify/specs/overview/matrix/cross-reference.json`
> Regenerate: `node .claude/skills/nick-q/scripts/generate-matrix-view.cjs`

---

## 1. Screen → Domain

Which Masters and APIs each screen uses.

| Screen ID | Name | Masters | APIs |
|-----------|------|---------|------|
| SCR-001 | ログイン画面 | M-USER | API-AUTH-LOGIN-001 |
| SCR-002 | ダッシュボード | M-CLIENT, M-SUBSIDY, M-DOCUMENT, M-SECTION-CONTENT, M-USER | API-DASHBOARD-001, API-CLIENT-LIST-001, API-AUTH-LOGOUT-001 |
| SCR-003 | クライアント詳細画面 | M-CLIENT, M-SUBSIDY | API-CLIENT-GET-001, API-SUBSIDY-LIST-001, API-SUBSIDY-CREATE-001 |
| SCR-004 | 補助金詳細画面 | M-SUBSIDY, M-DOCUMENT | API-SUBSIDY-GET-001, API-DOCUMENT-CREATE-001 |
| SCR-005 | 書類詳細画面 | M-DOCUMENT, M-SECTION | API-DOCUMENT-GET-001, API-EXPORT-WORD-001, API-EXPORT-PDF-001 |
| SCR-006 | セクション編集画面 | M-SECTION, M-SECTION-CONTENT, M-SECTION-CONTENT-HISTORY, M-REFERENCE-FILE, M-FILE-SECTION-LINK | API-SECTION-GET-001, API-SECTION-UPDATE-001, API-SECTION-HISTORY-001, API-SECTION-RESTORE-001, API-FILE-LINK-001, API-FILE-UNLINK-001, API-FILE-UPLOAD-001, API-FILE-LIST-001 |
| SCR-007 | 収益計画画面 | M-SECTION, M-SECTION-CONTENT, M-SECTION-CONTENT-HISTORY | API-SECTION-GET-001, API-SECTION-UPDATE-001, API-SECTION-HISTORY-001, API-SECTION-RESTORE-001, API-REVENUE-CALCULATE-001 |
| SCR-008 | スケジュール画面 | M-SECTION, M-SECTION-CONTENT, M-SECTION-CONTENT-HISTORY | API-SECTION-GET-001, API-SECTION-UPDATE-001, API-SECTION-HISTORY-001, API-SECTION-RESTORE-001 |
| SCR-009 | 参考ファイル管理画面 | M-CLIENT, M-REFERENCE-FILE | API-FILE-LIST-001, API-FILE-UPLOAD-001, API-FILE-PREVIEW-001, API-FILE-DELETE-001 |
| SCR-010 | テンプレート管理画面 | M-SUBSIDY-TEMPLATE | API-TEMPLATE-LIST-001, API-TEMPLATE-GET-001, API-TEMPLATE-UPDATE-001, API-TEMPLATE-CREATE-001 |
| SCR-011 | ユーザー管理画面 | M-USER | API-USER-LIST-001, API-USER-INVITE-001, API-USER-UPDATE-001, API-USER-DELETE-001 |
| SCR-012 | クライアント管理画面 | M-CLIENT | API-CLIENT-LIST-001, API-CLIENT-CREATE-001, API-CLIENT-UPDATE-001, API-CLIENT-DELETE-001 |


---

## 2. Feature → Domain

Which Screens, Masters, APIs, and Rules each feature uses.

*No features defined*


---

## 3. Reverse Lookup: Master → Usage

Find all screens and features that use a specific Master.

| Master | Used by Screens | Used by Features |
|--------|-----------------|------------------|
| M-CLIENT | SCR-002, SCR-003, SCR-009, SCR-012 | - |
| M-DOCUMENT | SCR-002, SCR-004, SCR-005 | - |
| M-FILE-SECTION-LINK | SCR-006 | - |
| M-REFERENCE-FILE | SCR-006, SCR-009 | - |
| M-SECTION | SCR-005, SCR-006, SCR-007, SCR-008 | - |
| M-SECTION-CONTENT | SCR-002, SCR-006, SCR-007, SCR-008 | - |
| M-SECTION-CONTENT-HISTORY | SCR-006, SCR-007, SCR-008 | - |
| M-SUBSIDY | SCR-002, SCR-003, SCR-004 | - |
| M-SUBSIDY-TEMPLATE | SCR-010 | - |
| M-USER | SCR-001, SCR-002, SCR-011 | - |


---

## 4. Reverse Lookup: API → Usage

Find all screens and features that use a specific API.

| API | Used by Screens | Used by Features |
|-----|-----------------|------------------|
| API-AUTH-LOGIN-001 | SCR-001 | - |
| API-AUTH-LOGOUT-001 | SCR-002 | - |
| API-CLIENT-CREATE-001 | SCR-012 | - |
| API-CLIENT-DELETE-001 | SCR-012 | - |
| API-CLIENT-GET-001 | SCR-003 | - |
| API-CLIENT-LIST-001 | SCR-002, SCR-012 | - |
| API-CLIENT-UPDATE-001 | SCR-012 | - |
| API-DASHBOARD-001 | SCR-002 | - |
| API-DOCUMENT-CREATE-001 | SCR-004 | - |
| API-DOCUMENT-GET-001 | SCR-005 | - |
| API-EXPORT-PDF-001 | SCR-005 | - |
| API-EXPORT-WORD-001 | SCR-005 | - |
| API-FILE-DELETE-001 | SCR-009 | - |
| API-FILE-LINK-001 | SCR-006 | - |
| API-FILE-LIST-001 | SCR-006, SCR-009 | - |
| API-FILE-PREVIEW-001 | SCR-009 | - |
| API-FILE-UNLINK-001 | SCR-006 | - |
| API-FILE-UPLOAD-001 | SCR-006, SCR-009 | - |
| API-REVENUE-CALCULATE-001 | SCR-007 | - |
| API-SECTION-GET-001 | SCR-006, SCR-007, SCR-008 | - |
| API-SECTION-HISTORY-001 | SCR-006, SCR-007, SCR-008 | - |
| API-SECTION-RESTORE-001 | SCR-006, SCR-007, SCR-008 | - |
| API-SECTION-UPDATE-001 | SCR-006, SCR-007, SCR-008 | - |
| API-SUBSIDY-CREATE-001 | SCR-003 | - |
| API-SUBSIDY-GET-001 | SCR-004 | - |
| API-SUBSIDY-LIST-001 | SCR-003 | - |
| API-TEMPLATE-CREATE-001 | SCR-010 | - |
| API-TEMPLATE-GET-001 | SCR-010 | - |
| API-TEMPLATE-LIST-001 | SCR-010 | - |
| API-TEMPLATE-UPDATE-001 | SCR-010 | - |
| API-USER-DELETE-001 | SCR-011 | - |
| API-USER-INVITE-001 | SCR-011 | - |
| API-USER-LIST-001 | SCR-011 | - |
| API-USER-UPDATE-001 | SCR-011 | - |


---

## 5. Permission Matrix

Role-based API permissions.

*No permissions defined*


---

## 6. Statistics

| Metric | Count |
|--------|-------|
| Total Screens | 12 |
| Total Features | 0 |
| Total Masters Referenced | 10 |
| Total APIs Referenced | 34 |
| Total Rules Referenced | 0 |

---

*This file is auto-generated. To update, edit `cross-reference.json` and run the generator script.*
