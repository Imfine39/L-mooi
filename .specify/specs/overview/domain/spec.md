# Domain Spec: LSystem

Spec Type: Domain
Spec ID: S-DOMAIN-001
Created: 2026-01-08
Status: Draft
Author: System
Related Vision: S-VISION-001
Related Screen: S-SCREEN-001

---
[USER FEEDBACK: コメント]←これをコピペしてコメントを入れてください。
## 1. Domain Overview

### 1.1 Domain Description

LSystem（補助金申請フォーマット自動作成システム）のドメインモデルと API 定義。

- Vision Spec: `.specify/specs/overview/vision/spec.md`
- Screen Spec: `.specify/specs/overview/screen/spec.md`
- Domain boundaries: 補助金申請書類の作成・管理
- Core concepts: 補助金、書類、セクション、参考ファイル

### 1.2 System Context

```
[User Browser] <---> [Web Application] <---> [Database]
                           |
                           v
                    [File Storage]
```

- Upstream systems: なし（Phase 1）
- Downstream systems: なし（Phase 1）
- Integration points: なし（Phase 1）

---

## 2. Actors and Roles

| Actor | Role | Permissions | Authentication |
|-------|------|-------------|----------------|
| Admin | システム管理者 | 全機能アクセス、テンプレート編集、ユーザー管理 | Email + Password |
| Creator | 申請書作成者 | 補助金・書類・セクションの作成・編集、ファイル管理 | Email + Password |

---

## 3. Master Data Definitions (M-*)

### M-USER: ユーザー

**Purpose:** システムにログインするユーザー情報を管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| email | string | Yes | ログイン用メールアドレス（一意） |
| passwordHash | string | Yes | ハッシュ化されたパスワード |
| name | string | Yes | 表示名 |
| role | enum | Yes | 権限（Admin, Creator） |
| createdAt | datetime | Yes | 作成日時 |
| updatedAt | datetime | Yes | 更新日時 |

**Relationships:**
- SectionContent: 作成者として参照
- SectionContentHistory: 編集者として参照

**Constraints:**
- email は一意
- role は Admin または Creator のみ

---

### M-SUBSIDY: 補助金

**Purpose:** 補助金の基本情報を管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| name | string | Yes | 補助金名（例: 新規事業創出補助金） |
| description | string | No | 補助金の説明 |
| deadline | date | No | 申請期限 |
| templateId | UUID | No | テンプレートへの参照 |
| createdAt | datetime | Yes | 作成日時 |
| updatedAt | datetime | Yes | 更新日時 |

**Relationships:**
- SubsidyTemplate: テンプレートを参照
- Document: 複数の書類を持つ

**Constraints:**
- name は必須

---

### M-SUBSIDY-TEMPLATE: 補助金テンプレート

**Purpose:** 補助金の書類・セクション構造のテンプレートを管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| name | string | Yes | テンプレート名 |
| structure | JSON | Yes | 書類・セクション構造 |
| createdAt | datetime | Yes | 作成日時 |
| updatedAt | datetime | Yes | 更新日時 |

**Structure JSON Format:**
```json
{
  "documents": [
    {
      "name": "事業計画書",
      "sections": [
        {
          "name": "SWOT分析",
          "type": "text",
          "description": "...",
          "charLimit": 300,
          "tags": ["新規事業", "外部環境"]
        },
        {
          "name": "収益計画",
          "type": "revenue_plan"
        },
        {
          "name": "スケジュール",
          "type": "schedule"
        }
      ]
    }
  ]
}
```

**Relationships:**
- Subsidy: テンプレートを使用する補助金

**Constraints:**
- structure は有効な JSON

---

### M-DOCUMENT: 書類

**Purpose:** 補助金申請に必要な書類を管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| subsidyId | UUID | Yes | 補助金への参照 |
| name | string | Yes | 書類名（例: 事業計画書） |
| order | int | Yes | 表示順 |
| createdAt | datetime | Yes | 作成日時 |
| updatedAt | datetime | Yes | 更新日時 |

**Relationships:**
- Subsidy: 所属する補助金
- Section: 複数のセクションを持つ

**Constraints:**
- subsidyId は有効な Subsidy を参照

---

### M-SECTION: セクション

**Purpose:** 書類内のセクション（入力項目）を管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| documentId | UUID | Yes | 書類への参照 |
| name | string | Yes | セクション名（例: SWOT分析） |
| type | enum | Yes | タイプ（text, revenue_plan, schedule） |
| description | string | No | セクションの説明文 |
| charLimit | int | No | 文字数制限（text タイプのみ） |
| tags | string[] | No | タグ配列 |
| order | int | Yes | 表示順 |
| createdAt | datetime | Yes | 作成日時 |
| updatedAt | datetime | Yes | 更新日時 |

**Relationships:**
- Document: 所属する書類
- SectionContent: セクションの入力内容

**Constraints:**
- type は text, revenue_plan, schedule のいずれか
- charLimit は text タイプの場合のみ有効

---

### M-SECTION-CONTENT: セクション内容

**Purpose:** セクションに入力された内容を管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| sectionId | UUID | Yes | セクションへの参照 |
| content | JSON | Yes | 入力内容（タイプにより構造が異なる） |
| status | enum | Yes | 状態（draft, completed） |
| createdBy | UUID | Yes | 作成者 |
| createdAt | datetime | Yes | 作成日時 |
| updatedAt | datetime | Yes | 更新日時 |

**Content JSON Format (type: text):**
```json
{
  "fields": {
    "strengths": "...",
    "weaknesses": "...",
    "opportunities": "...",
    "threats": "..."
  },
  "memo": "...",
  "instruction": "..."
}
```

**Content JSON Format (type: revenue_plan):**
```json
{
  "parameters": {
    "existingRevenue": 10000,
    "newBusinessInitial": 1000,
    "growthRate": 20,
    "laborCostRate": 30,
    "otherExpenses": 2000
  },
  "calculated": {
    "years": [
      { "year": 1, "existing": 10000, "new": 1000, "total": 11000, "laborCost": 3300, "profit": 5700 }
    ]
  }
}
```

**Content JSON Format (type: schedule):**
```json
{
  "tasks": [
    {
      "id": "T-001",
      "name": "市場調査",
      "startDate": "2026-04-01",
      "endDate": "2026-05-31"
    }
  ]
}
```

**Relationships:**
- Section: 所属するセクション
- User: 作成者
- SectionContentHistory: 編集履歴
- ReferenceFile: 紐づけられた参考ファイル

**Constraints:**
- status は draft または completed

---

### M-SECTION-CONTENT-HISTORY: セクション内容履歴

**Purpose:** セクション内容の編集履歴を管理（P-SETUP-001 採用）

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| sectionContentId | UUID | Yes | セクション内容への参照 |
| content | JSON | Yes | 保存時点の内容 |
| editedBy | UUID | Yes | 編集者 |
| editedAt | datetime | Yes | 編集日時 |

**Relationships:**
- SectionContent: 履歴元のセクション内容
- User: 編集者

**Constraints:**
- 内容更新時に自動で履歴を作成

---

### M-REFERENCE-FILE: 参考ファイル

**Purpose:** アップロードされた参考ファイルを管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| filename | string | Yes | ファイル名 |
| originalName | string | Yes | 元のファイル名 |
| mimeType | string | Yes | MIMEタイプ |
| size | int | Yes | ファイルサイズ（バイト） |
| path | string | Yes | ストレージパス |
| category | string | No | カテゴリ（基本情報, 計画, 財務 等） |
| uploadedBy | UUID | Yes | アップロードユーザー |
| createdAt | datetime | Yes | 作成日時 |

**Relationships:**
- User: アップロードユーザー
- SectionContent: 紐づけられたセクション内容（多対多）

**Constraints:**
- 許可されたファイル形式のみ（PDF, Word, Excel, 画像）
- ファイルサイズ上限: 10MB

---

### M-FILE-SECTION-LINK: ファイル-セクション紐づけ

**Purpose:** 参考ファイルとセクション内容の紐づけを管理

**Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | Yes | Primary identifier |
| fileId | UUID | Yes | 参考ファイルへの参照 |
| sectionContentId | UUID | Yes | セクション内容への参照 |
| createdAt | datetime | Yes | 作成日時 |

**Relationships:**
- ReferenceFile: 紐づけられたファイル
- SectionContent: 紐づけられたセクション内容

**Constraints:**
- 同一ファイルと同一セクション内容の組み合わせは一意

---

## 4. API Contracts (API-*)

### API-DASHBOARD-001: ダッシュボードデータ取得

**Purpose:** ダッシュボード表示用のデータを取得

**Endpoint:**
```
GET /api/v1/dashboard
```

**Response (Success):**
```json
{
  "recentEdits": [
    {
      "sectionId": "uuid",
      "sectionName": "string",
      "documentName": "string",
      "subsidyName": "string",
      "editedAt": "datetime"
    }
  ],
  "progressSummary": {
    "totalSubsidies": 5,
    "completedSections": 24,
    "totalSections": 30
  },
  "subsidies": [
    {
      "id": "uuid",
      "name": "string",
      "deadline": "date",
      "progress": 60
    }
  ]
}
```

**Authorization:** 認証済みユーザー

---

### API-AUTH-LOGIN-001: ログイン

**Purpose:** ユーザー認証を行いトークンを発行

**Endpoint:**
```
POST /api/v1/auth/login
```

**Request:**
```json
{
  "email": "string",
  "password": "string"
}
```

**Response (Success):**
```json
{
  "token": "string",
  "user": {
    "id": "uuid",
    "email": "string",
    "name": "string",
    "role": "Admin|Creator"
  }
}
```

**Error Codes:**
| Code | HTTP Status | Description |
|------|-------------|-------------|
| INVALID_CREDENTIALS | 401 | メールまたはパスワードが不正 |

**Authorization:** なし

---

### API-AUTH-LOGOUT-001: ログアウト

**Purpose:** トークンを無効化

**Endpoint:**
```
POST /api/v1/auth/logout
```

**Response (Success):**
```json
{
  "success": true
}
```

**Authorization:** 認証済みユーザー

---

### API-SUBSIDY-LIST-001: 補助金一覧取得

**Purpose:** 補助金の一覧を取得

**Endpoint:**
```
GET /api/v1/subsidies
```

**Query Parameters:**
- `search`: 検索キーワード（任意）

**Response (Success):**
```json
{
  "subsidies": [
    {
      "id": "uuid",
      "name": "string",
      "deadline": "date",
      "progress": 60
    }
  ]
}
```

**Authorization:** 認証済みユーザー

---

### API-SUBSIDY-GET-001: 補助金詳細取得

**Purpose:** 補助金と紐づく書類一覧を取得

**Endpoint:**
```
GET /api/v1/subsidies/:id
```

**Response (Success):**
```json
{
  "subsidy": {
    "id": "uuid",
    "name": "string",
    "description": "string",
    "deadline": "date",
    "documents": [
      {
        "id": "uuid",
        "name": "string",
        "progress": 60
      }
    ]
  }
}
```

**Error Codes:**
| Code | HTTP Status | Description |
|------|-------------|-------------|
| NOT_FOUND | 404 | 補助金が見つからない |

**Authorization:** 認証済みユーザー

---

### API-SUBSIDY-CREATE-001: 補助金作成

**Purpose:** 新規補助金を作成

**Endpoint:**
```
POST /api/v1/subsidies
```

**Request:**
```json
{
  "name": "string",
  "description": "string",
  "deadline": "date",
  "templateId": "uuid"
}
```

**Response (Success):**
```json
{
  "subsidy": {
    "id": "uuid",
    "name": "string"
  }
}
```

**Authorization:** 認証済みユーザー

---

### API-DOCUMENT-GET-001: 書類詳細取得

**Purpose:** 書類とセクション一覧を取得

**Endpoint:**
```
GET /api/v1/documents/:id
```

**Response (Success):**
```json
{
  "document": {
    "id": "uuid",
    "name": "string",
    "progress": 60,
    "sections": [
      {
        "id": "uuid",
        "name": "string",
        "type": "text|revenue_plan|schedule",
        "status": "draft|completed"
      }
    ]
  }
}
```

**Authorization:** 認証済みユーザー

---

### API-DOCUMENT-CREATE-001: 書類作成

**Purpose:** 補助金に新規書類を追加

**Endpoint:**
```
POST /api/v1/subsidies/:subsidyId/documents
```

**Request:**
```json
{
  "name": "string",
  "order": 1
}
```

**Response (Success):**
```json
{
  "document": {
    "id": "uuid",
    "name": "string"
  }
}
```

**Authorization:** 認証済みユーザー

---

### API-SECTION-GET-001: セクション詳細取得

**Purpose:** セクションと入力内容を取得

**Endpoint:**
```
GET /api/v1/sections/:id
```

**Response (Success):**
```json
{
  "section": {
    "id": "uuid",
    "name": "string",
    "type": "text",
    "description": "string",
    "charLimit": 300,
    "tags": ["tag1", "tag2"],
    "content": {
      "fields": {},
      "memo": "string"
    },
    "linkedFiles": [
      {
        "id": "uuid",
        "filename": "string"
      }
    ]
  }
}
```

**Authorization:** 認証済みユーザー

---

### API-SECTION-UPDATE-001: セクション内容更新

**Purpose:** セクションの入力内容を更新（履歴も自動作成）

**Endpoint:**
```
PUT /api/v1/sections/:id/content
```

**Request:**
```json
{
  "content": {},
  "status": "draft|completed"
}
```

**Response (Success):**
```json
{
  "success": true,
  "historyId": "uuid"
}
```

**Authorization:** 認証済みユーザー

---

### API-SECTION-HISTORY-001: セクション履歴一覧取得

**Purpose:** セクションの編集履歴を取得

**Endpoint:**
```
GET /api/v1/sections/:id/history
```

**Response (Success):**
```json
{
  "history": [
    {
      "id": "uuid",
      "editedBy": {
        "id": "uuid",
        "name": "string"
      },
      "editedAt": "datetime"
    }
  ]
}
```

**Authorization:** 認証済みユーザー

---

### API-SECTION-RESTORE-001: セクション履歴復元

**Purpose:** 過去の履歴から内容を復元

**Endpoint:**
```
POST /api/v1/sections/:id/restore/:historyId
```

**Response (Success):**
```json
{
  "success": true
}
```

**Authorization:** 認証済みユーザー

---

### API-FILE-UPLOAD-001: ファイルアップロード

**Purpose:** 参考ファイルをアップロード

**Endpoint:**
```
POST /api/v1/files
```

**Request:** multipart/form-data
- file: ファイル
- category: カテゴリ（任意）

**Response (Success):**
```json
{
  "file": {
    "id": "uuid",
    "filename": "string",
    "size": 1234
  }
}
```

**Error Codes:**
| Code | HTTP Status | Description |
|------|-------------|-------------|
| FILE_TOO_LARGE | 413 | ファイルサイズ超過 |
| INVALID_FILE_TYPE | 400 | 許可されていないファイル形式 |

**Authorization:** 認証済みユーザー

---

### API-FILE-LIST-001: ファイル一覧取得

**Purpose:** 参考ファイルの一覧を取得

**Endpoint:**
```
GET /api/v1/files
```

**Response (Success):**
```json
{
  "files": [
    {
      "id": "uuid",
      "filename": "string",
      "originalName": "string",
      "category": "string",
      "size": 1234,
      "createdAt": "datetime"
    }
  ]
}
```

**Authorization:** 認証済みユーザー

---

### API-FILE-PREVIEW-001: ファイルプレビュー取得

**Purpose:** ファイルのプレビュー用データを取得

**Endpoint:**
```
GET /api/v1/files/:id/preview
```

**Response (Success):**
- PDF: PDF データをそのまま返却
- 画像: 画像データを返却
- Word/Excel: 変換後の PDF または HTML

**Authorization:** 認証済みユーザー

---

### API-FILE-LINK-001: ファイル紐づけ

**Purpose:** 参考ファイルをセクションに紐づけ

**Endpoint:**
```
POST /api/v1/sections/:sectionId/files/:fileId
```

**Response (Success):**
```json
{
  "success": true
}
```

**Authorization:** 認証済みユーザー

---

### API-FILE-UNLINK-001: ファイル紐づけ解除

**Purpose:** 参考ファイルとセクションの紐づけを解除

**Endpoint:**
```
DELETE /api/v1/sections/:sectionId/files/:fileId
```

**Response (Success):**
```json
{
  "success": true
}
```

**Authorization:** 認証済みユーザー

---

### API-FILE-DELETE-001: ファイル削除

**Purpose:** 参考ファイルを削除

**Endpoint:**
```
DELETE /api/v1/files/:id
```

**Response (Success):**
```json
{
  "success": true
}
```

**Error Codes:**
| Code | HTTP Status | Description |
|------|-------------|-------------|
| NOT_FOUND | 404 | ファイルが見つからない |
| FILE_IN_USE | 400 | ファイルがセクションに紐づけられている |

**Authorization:** 認証済みユーザー

---

### API-REVENUE-CALCULATE-001: 収益計画計算

**Purpose:** パラメータから収益計画を計算

**Endpoint:**
```
POST /api/v1/revenue-plan/calculate
```

**Request:**
```json
{
  "existingRevenue": 10000,
  "newBusinessInitial": 1000,
  "growthRate": 20,
  "laborCostRate": 30,
  "otherExpenses": 2000,
  "years": 5
}
```

**Response (Success):**
```json
{
  "calculated": {
    "years": [
      {
        "year": 1,
        "existingRevenue": 10000,
        "newRevenue": 1000,
        "totalRevenue": 11000,
        "laborCost": 3300,
        "otherExpenses": 2000,
        "operatingProfit": 5700
      }
    ]
  }
}
```

**Authorization:** 認証済みユーザー

---

### API-EXPORT-WORD-001: Word エクスポート

**Purpose:** 書類を Word 形式でエクスポート

**Endpoint:**
```
GET /api/v1/documents/:id/export/word
```

**Response (Success):**
- Content-Type: application/vnd.openxmlformats-officedocument.wordprocessingml.document
- Word ファイルをダウンロード

**Authorization:** 認証済みユーザー

---

### API-EXPORT-PDF-001: PDF エクスポート

**Purpose:** 書類を PDF 形式でエクスポート

**Endpoint:**
```
GET /api/v1/documents/:id/export/pdf
```

**Response (Success):**
- Content-Type: application/pdf
- PDF ファイルをダウンロード

**Authorization:** 認証済みユーザー

---

### API-TEMPLATE-LIST-001: テンプレート一覧取得

**Purpose:** 補助金テンプレートの一覧を取得

**Endpoint:**
```
GET /api/v1/templates
```

**Response (Success):**
```json
{
  "templates": [
    {
      "id": "uuid",
      "name": "string"
    }
  ]
}
```

**Authorization:** Admin のみ

---

### API-TEMPLATE-GET-001: テンプレート詳細取得

**Purpose:** テンプレートの構造を取得

**Endpoint:**
```
GET /api/v1/templates/:id
```

**Response (Success):**
```json
{
  "template": {
    "id": "uuid",
    "name": "string",
    "structure": {}
  }
}
```

**Authorization:** Admin のみ

---

### API-TEMPLATE-UPDATE-001: テンプレート更新

**Purpose:** テンプレートの構造を更新

**Endpoint:**
```
PUT /api/v1/templates/:id
```

**Request:**
```json
{
  "name": "string",
  "structure": {}
}
```

**Response (Success):**
```json
{
  "success": true
}
```

**Authorization:** Admin のみ

---

### API-TEMPLATE-CREATE-001: テンプレート作成

**Purpose:** 新規補助金テンプレートを作成

**Endpoint:**
```
POST /api/v1/templates
```

**Request:**
```json
{
  "name": "string",
  "structure": {}
}
```

**Response (Success):**
```json
{
  "template": {
    "id": "uuid",
    "name": "string"
  }
}
```

**Authorization:** Admin のみ

---

### API-USER-LIST-001: ユーザー一覧取得

**Purpose:** ユーザーの一覧を取得

**Endpoint:**
```
GET /api/v1/users
```

**Response (Success):**
```json
{
  "users": [
    {
      "id": "uuid",
      "email": "string",
      "name": "string",
      "role": "Admin|Creator"
    }
  ]
}
```

**Authorization:** Admin のみ

---

### API-USER-INVITE-001: ユーザー招待

**Purpose:** 新規ユーザーを招待

**Endpoint:**
```
POST /api/v1/users/invite
```

**Request:**
```json
{
  "email": "string",
  "name": "string",
  "role": "Admin|Creator"
}
```

**Response (Success):**
```json
{
  "user": {
    "id": "uuid",
    "email": "string"
  }
}
```

**Authorization:** Admin のみ

---

### API-USER-UPDATE-001: ユーザー更新

**Purpose:** ユーザー情報を更新

**Endpoint:**
```
PUT /api/v1/users/:id
```

**Request:**
```json
{
  "name": "string",
  "role": "Admin|Creator"
}
```

**Response (Success):**
```json
{
  "success": true
}
```

**Error Codes:**
| Code | HTTP Status | Description |
|------|-------------|-------------|
| NOT_FOUND | 404 | ユーザーが見つからない |
| CANNOT_DEMOTE_LAST_ADMIN | 400 | 最後の管理者の権限は変更できない |

**Authorization:** Admin のみ

---

### API-USER-DELETE-001: ユーザー削除

**Purpose:** ユーザーを削除

**Endpoint:**
```
DELETE /api/v1/users/:id
```

**Response (Success):**
```json
{
  "success": true
}
```

**Error Codes:**
| Code | HTTP Status | Description |
|------|-------------|-------------|
| NOT_FOUND | 404 | ユーザーが見つからない |
| CANNOT_DELETE_LAST_ADMIN | 400 | 最後の管理者は削除できない |
| CANNOT_DELETE_SELF | 400 | 自分自身は削除できない |

**Authorization:** Admin のみ

---

## 5. Business Rules

### 5.1 Domain Rules

| Rule ID | Description | Applies To |
|---------|-------------|------------|
| BR-001 | セクション内容を更新する際、必ず履歴を作成する | M-SECTION-CONTENT, API-SECTION-UPDATE-001 |
| BR-002 | 書類の進捗は完了セクション数 / 全セクション数 で計算 | M-DOCUMENT |
| BR-003 | 収益計画の新規事業売上は複利計算（前年 * (1 + 成長率/100)） | API-REVENUE-CALCULATE-001 |
| BR-004 | ファイル紐づけ解除時、ファイル自体は削除されない | API-FILE-UNLINK-001 |

### 5.2 Validation Rules

| Rule ID | Field/Entity | Validation | Error Message |
|---------|--------------|------------|---------------|
| VR-001 | M-USER.email | 有効なメール形式 | 有効なメールアドレスを入力してください |
| VR-002 | M-USER.password | 8文字以上 | パスワードは8文字以上で入力してください |
| VR-003 | M-REFERENCE-FILE.size | 10MB以下 | ファイルサイズは10MB以下にしてください |
| VR-004 | M-SECTION-CONTENT.content | charLimit以下（textタイプ） | 文字数制限を超えています |

### 5.3 Calculation Rules

| Rule ID | Description | Formula/Logic |
|---------|-------------|---------------|
| CR-001 | 新規事業売上（n年目） | 初年度売上 * (1 + 成長率/100)^(n-1) |
| CR-002 | 人件費 | 合計売上 * 人件費率 / 100 |
| CR-003 | 営業利益 | 合計売上 - 人件費 - その他経費 |
| CR-004 | 書類進捗率 | 完了セクション数 / 全セクション数 * 100 |

---

## 6. Non-Functional Requirements

### 6.1 Performance

| Metric | Target | Measurement |
|--------|--------|-------------|
| API Response Time | < 500ms (p95) | アプリケーションログ |
| File Upload | < 5s (10MB) | アプリケーションログ |

### 6.2 Security

- Authentication method: JWT トークン
- Authorization model: ロールベース（Admin, Creator）
- Data encryption: HTTPS（通信）、パスワードはbcryptハッシュ
- Audit requirements: 基本的なアクセスログ

### 6.3 Reliability

- Availability target: 99%（営業時間内）
- Backup strategy: 日次バックアップ
- Disaster recovery: バックアップからのリストア

### 6.4 Observability

- Logging standards: JSON形式、リクエストID付与
- Metrics to collect: リクエスト数、レスポンス時間、エラー率
- Tracing requirements: 基本的なリクエストトレーシング

---

## 7. Technology Decisions

### 7.1 Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| Frontend | React + TypeScript | モダンなSPA開発、型安全性 |
| Backend | Node.js + Express | JavaScript統一、豊富なエコシステム |
| Database | PostgreSQL | リレーショナルデータ、JSON対応 |
| File Storage | Local / S3 互換 | シンプル、将来の拡張性 |
| Infrastructure | Docker | 環境の再現性、デプロイ容易性 |

### 7.2 External Dependencies

| Dependency | Version | Purpose |
|------------|---------|---------|
| React | 18.x | フロントエンドUI |
| Express | 4.x | バックエンドAPI |
| Prisma | 5.x | ORM、データベースアクセス |
| docx | latest | Word エクスポート |
| pdfkit | latest | PDF エクスポート |

---

## 8. Feature Index

| Feature ID | Title | Path | Status | Related M-*/API-* |
|------------|-------|------|--------|-------------------|
| (Features will be added after Foundation) | | | | |

---

## 9. Open Questions

(All resolved)

---

## 10. Clarifications

| Date | Question | Answer | Impact |
|------|----------|--------|--------|
| 2026-01-08 | テナント機能は必要か？ | Phase 1 は不要 | M-TENANT を削除 |
| 2026-01-08 | 収益計画の計算ロジックは？ | 複利計算、パラメータ入力 | CR-001, CR-002, CR-003 を追加 |
| 2026-01-08 | 履歴機能のスコープは？ | セクション内容の履歴のみ | M-SECTION-CONTENT-HISTORY を追加 |

---

## 11. Changelog

| Date | Change Type | Description | Issue |
|------|-------------|-------------|-------|
| 2026-01-08 | Created | Initial domain specification | - |
| 2026-01-08 | Updated | Reviewer C: 欠落 API 追加 (DASHBOARD-001, FILE-DELETE-001, TEMPLATE-CREATE-001, USER-UPDATE-001, USER-DELETE-001, DOCUMENT-CREATE-001) | - |

---

## 12. Implementation Notes

- Technical constraints discovered: (to be filled during implementation)
- Design decisions made: (to be filled during implementation)
- Deviations from original spec: (to be filled during implementation)
