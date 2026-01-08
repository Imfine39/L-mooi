# Vision Spec: LSystem

Spec Type: Vision
Spec ID: S-VISION-001
Created: 2026-01-08
Status: Draft
Author: System

---
[USER FEEDBACK: コメント]←これをコピペしてコメントを入れてください。
## 1. System Purpose

### 1.1 Why are we building this?

- Problem statement: 補助金申請支援業務において、事業計画書等のドキュメント作成に多大な時間と労力がかかっている
- Current pain points:
  - 事業計画書の各セクション（SWOT分析、事業背景、収益計画等）を毎回手作業で作成
  - 参考資料から必要な情報を探し出す作業が非効率
  - 収益計画の数値作成に時間がかかる
  - スケジュール作成が属人的
- Opportunity: AI を活用したドキュメント自動生成により、申請書類作成の効率を大幅に向上させる

### 1.2 Vision Statement

> 補助金申請に必要な事業計画書を、参考資料とパラメータ入力から自動生成し、申請書類作成にかかる時間を大幅に削減するシステム

### 1.3 Success Definition

- What does success look like?
  - 実際にこのプロジェクトを用いて申請書類を作成することができる
- Key outcomes we want to achieve:
  - 事業計画書の各セクションを効率的に作成できる
  - 収益計画を数値パラメータから自動生成できる
  - スケジュール/WBSを管理できる
  - 作成した書類をWord/PDFでエクスポートできる
- How will we measure success?
  - 申請書類作成に要する時間の短縮
  - システムを使用した申請の成功

---

## 2. Target Users and Stakeholders

### 2.1 Primary Users

| Actor | Description | Primary Goals |
|-------|-------------|---------------|
| 申請書作成者 | 補助金申請書類を作成する担当者 | 事業計画書のセクション内容を効率的に作成したい |
| システム管理者 | システムの設定・テンプレート管理を行う | 補助金テンプレートを管理し、参考資料を整備したい |

### 2.2 Secondary Users

| Actor | Description | Interaction |
|-------|-------------|-------------|
| 経営者 | 申請内容の最終確認者 | 作成された書類を確認・承認 |

### 2.3 Stakeholders

- Business stakeholders: 社内の補助金申請支援チーム
- Technical stakeholders: システム運用担当
- External parties: （Phase 2 以降）外部の行政書士事務所等

---

## 3. User Journeys

### J-001: 事業計画書セクション作成

**Actor:** 申請書作成者
**Goal:** 事業計画書のセクション（SWOT分析等）を効率的に作成する

**Story:**
> 申請書作成者は、新規事業創出補助金の申請に必要なSWOT分析セクションを作成したい。システムにログインし、対象の補助金と書類を選択する。SWOT分析セクションを開くと、関連する参考ファイルが自動で紐づけられている。追加の指示を入力し、生成ボタンを押すと、セクションの内容が自動生成される。必要に応じて編集し、保存する。

**Key Steps:**
1. 補助金・書類を選択
2. セクションを選択
3. 参考ファイルを確認・紐づけ
4. 追加指示を入力
5. 内容を生成・編集・保存

**Success Outcome:**
- SWOT分析の内容が参考資料を元に適切に生成される
- 編集履歴が保存され、後から確認できる

---

### J-002: 収益計画作成

**Actor:** 申請書作成者
**Goal:** 新規事業の収益計画表を作成する

**Story:**
> 申請書作成者は、新規事業の5年間の収益計画を作成する必要がある。既存事業の売上、新規事業の成長率、人件費の考え方などのパラメータを入力すると、システムが収益計画表を自動生成する。新規事業の売上が年間20%成長する想定で、既存事業と合算した数値が計算される。

**Key Steps:**
1. 収益計画セクションを選択
2. パラメータを入力（既存売上、成長率、人件費等）
3. 計算ロジックを確認
4. 収益計画表を生成
5. 必要に応じて調整・保存

**Success Outcome:**
- 入力パラメータに基づいた5年間の収益計画表が生成される
- 新規事業と既存事業の合算が正しく計算される

---

### J-003: スケジュール/WBS作成

**Actor:** 申請書作成者
**Goal:** 事業のスケジュール・WBSを作成する

**Story:**
> 申請書作成者は、事業計画のスケジュールを作成する。アクションプランを箇条書きで入力すると、WBS形式で整理され、ガントチャートとして表示される。

**Key Steps:**
1. スケジュールセクションを選択
2. アクションプランを箇条書きで入力
3. 期間・依存関係を設定
4. ガントチャートで確認
5. 保存・エクスポート

**Success Outcome:**
- アクションプランがWBS形式で整理される
- ガントチャートで視覚的に確認できる

---

## 4. Scope

### 4.1 In Scope

High-level capabilities this system will provide:

**Phase 1（今回のスコープ）:**
- [x] 補助金・申請書類・セクションの管理（CRUD）
- [x] セクション内容の入力・編集（AI生成はPhase 2）
- [x] 参考ファイルのアップロード・管理
- [x] 参考ファイルの手動紐づけ
- [x] 収益計画のパラメータ入力による自動計算
- [x] スケジュール/WBSの手動入力・ガントチャート表示
- [x] セクション編集履歴の保存
- [x] ファイルプレビュー機能
- [x] Word/PDFエクスポート機能
- [x] ユーザー認証（メール+パスワード）

### 4.2 Out of Scope

What this system will NOT do (at least in initial release):

- AI によるセクション内容の自動生成
- AI による参考ファイルの自動判別・紐づけ
- マルチテナント機能（複数会社対応）
- テナント別カスタムテンプレート
- 外部システム連携
- AI による収益計画の推論

### 4.3 Future Considerations

Items that may be considered for future phases:

**Phase 2:**
- AI によるセクション内容の自動生成
- AI による参考ファイルの自動判別
- AI による収益計画数値の推論・補完
- AI によるアクションプランからのWBS自動生成

**Phase 3+:**
- マルチテナント機能（SaaS化）
- テナント別カスタムテンプレート
- 外部API連携

---

## 5. Screen Hints

### 5.1 Screen List (Provisional)

| # | Screen Name | Purpose | Key Elements |
|---|-------------|---------|--------------|
| 1 | ログイン画面 | ユーザー認証 | メール入力、パスワード入力、ログインボタン |
| 2 | ダッシュボード | 作業状況の概要表示 | 補助金一覧、最近の編集、進捗サマリー |
| 3 | 補助金一覧画面 | 補助金の管理 | 補助金リスト、検索、新規作成 |
| 4 | 補助金詳細画面 | 補助金の書類一覧 | 書類リスト、進捗状況 |
| 5 | 書類詳細画面 | 書類のセクション一覧 | セクションリスト、進捗バー |
| 6 | セクション編集画面 | セクション内容の編集 | 説明文、入力欄、参考ファイル、メモ、生成ボタン |
| 7 | 収益計画画面 | 収益計画の作成 | パラメータ入力、計算結果表、グラフ |
| 8 | スケジュール画面 | WBS・ガントチャート | アクションプラン入力、ガントチャート |
| 9 | 参考ファイル管理画面 | ファイルのアップロード・管理 | ファイルリスト、アップロード、プレビュー |
| 10 | テンプレート管理画面 | 補助金テンプレートの編集 | 書類・セクション構造の編集 |
| 11 | ユーザー管理画面 | ユーザーの管理 | ユーザーリスト、招待 |

### 5.2 Screen Transitions (Provisional)

- ログイン画面 → ダッシュボード: ログイン成功
- ダッシュボード → 補助金一覧画面: メニュー選択
- 補助金一覧画面 → 補助金詳細画面: 補助金選択
- 補助金詳細画面 → 書類詳細画面: 書類選択
- 書類詳細画面 → セクション編集画面: セクション選択
- 書類詳細画面 → 収益計画画面: 収益計画セクション選択
- 書類詳細画面 → スケジュール画面: スケジュールセクション選択

### 5.3 Design Preferences

- **Style**: 企業システム風、シンプルで見やすい
- **Responsive**: PC のみ（タブレット対応は将来検討）
- **Reference Images**: `.specify/assets/wireframes/template_image.png`

---

## 6. Constraints and Assumptions

### 6.1 Business Constraints

- Organizational: 社内利用のみ（Phase 1）
- Compliance requirements: 特になし

### 6.2 Technical Constraints

- Must integrate with: 特になし（Phase 1）
- Must comply with: 一般的なWebセキュリティ基準
- Platform/environment restrictions: Webブラウザ対応

### 6.3 Assumptions

- ユーザーは補助金申請の業務知識を持っている
- 参考資料は事前にデジタル化されている
- 新規事業創出補助金のフォーマットを主な対象とする

---

## 7. Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| 補助金フォーマットの変更 | Med | Med | テンプレートを柔軟に編集可能にする |
| 収益計画の計算ロジックが複雑化 | Med | Med | 基本的な計算パターンから開始し、段階的に拡張 |
| ファイルサイズが大きすぎる | Low | Med | アップロードサイズ制限を設ける |

---

## 8. Open Questions

- [x] テナント機能の要否 → Phase 1 は不要（シングルテナント）
- [x] AI 機能の範囲 → Phase 2 で追加
- [x] 技術スタック → おまかせ（Foundation で決定）

---

## 9. Clarifications

| Date | Question | Answer | Impact |
|------|----------|--------|--------|
| 2026-01-08 | テナント機能は Phase 1 で必要か？ | 不要（シングルテナント） | テナント管理機能を Phase 2 に移行 |
| 2026-01-08 | 収益計画生成の方式は？ | ハイブリッド（Phase 1: パラメータ入力、Phase 2: AI補完） | 収益計画画面のUI設計に反映 |
| 2026-01-08 | WBS生成は Phase 1 に含めるか？ | 含める（手動入力） | スケジュール画面を Phase 1 スコープに追加 |

---

## 10. Related Documents

- Screen Spec: `.specify/specs/overview/screen/spec.md`
- Domain Spec: `.specify/specs/overview/domain/spec.md`
- Feature Specs: `.specify/specs/features/`

---

## 11. Original Input

```
プロジェクト名: LSystem
一言で説明すると: 補助金申請フォーマット自動作成システム
解決したい課題: 補助金申請支援の際に多くドキュメント、特に顧客の事業計画書のようなものを作成するが、
この作成をある程度AIを駆使して自動化したい。そしてこのシステムを他の補助金申請業者に売りたい。

機能リスト:
1. 項目作成機能 - 事業計画書内のセクションをテンプレとして切り分け、参考資料を基に自動で埋める
2. 申請書テンプレ編集機能 - 補助金毎に必要な書類とセクションを編集
3. 補助金一覧機能 - テンプレ書類が補助金毎にまとまっている
4. テナント一覧機能 - システム利用しているテナントの管理
5. 関連書類保存機能 - 会社の関連書類、情報を保存

MVP: Phase 1は画面操作のみ、AIとの繋ぎこみはPhase 2
```

---

## 12. Changelog

| Date | Change Type | Description | Author |
|------|-------------|-------------|--------|
| 2026-01-08 | Created | Initial vision specification from QA | System |

Change types: Created, Updated, Clarified, Approved
