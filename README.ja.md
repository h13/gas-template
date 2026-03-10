# gas-template

[![CI](https://github.com/h13/gas-template/actions/workflows/ci.yml/badge.svg)](https://github.com/h13/gas-template/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Node.js](https://img.shields.io/badge/Node.js-%3E%3D24-green.svg)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-strict-blue.svg)](https://www.typescriptlang.org/)
[![Google Apps Script](https://img.shields.io/badge/Google%20Apps%20Script-Template-4285F4.svg)](https://developers.google.com/apps-script)

[English](README.md)

Google Apps Script のプロジェクトテンプレートです。TypeScript で GAS を書き、テスト・リント・CI/CD まですべてセットアップ済みですぐに使えます。

## このテンプレートを使う理由

Google Apps Script のプロジェクトは、規模が大きくなると管理が難しくなりがちです。このテンプレートはそれを解決します：

- **TypeScript** — strict モードの型チェックで、デプロイ前にバグを発見
- **ローカル開発** — GAS のウェブエディタではなく、好きなエディタで開発・テスト
- **自動品質チェック** — コミットのたびにリント・フォーマット・テストが自動実行
- **CI/CD パイプライン** — `main` にプッシュするだけで GAS に自動デプロイ
- **依存管理** — Renovate がすべてのパッケージを最新に保つ

## 含まれるもの

| カテゴリ   | ツール                                               |
| ---------- | ---------------------------------------------------- |
| 言語       | TypeScript（strict モード）                          |
| バンドラー | Rollup（GAS 互換の ESM 出力）                        |
| デプロイ   | clasp（dev / prod 環境）                             |
| テスト     | Jest（カバレッジ閾値 100%）                          |
| リント     | ESLint, Prettier, Stylelint（HTML 内 CSS）, HTMLHint |
| Git hooks  | husky + lint-staged（コミット時に自動修正）          |
| CI/CD      | GitHub Actions（PR で CI、マージで CD）              |
| 依存管理   | Renovate（自動更新 + オートマージ）                  |

## はじめかた

### 前提条件

以下のいずれかの方法でセットアップしてください：

#### 方法 A: ローカル（mise）

[mise](https://mise.jdx.dev/) を使う場合、Node.js と pnpm は `.mise.toml` で自動設定されます。

```bash
mise install   # Node.js >= 24 と pnpm 10 をインストール
```

手動でインストールする場合：

| ツール                         | バージョン | インストール方法                    |
| ------------------------------ | ---------- | ----------------------------------- |
| [Node.js](https://nodejs.org/) | >= 24      | [ダウンロード](https://nodejs.org/) |
| [pnpm](https://pnpm.io/)       | 10         | `npm install -g pnpm`               |

#### 方法 B: Dev Container / GitHub Codespaces

ローカル環境のセットアップは不要です。`.devcontainer/` にすべて設定済みです。

- **VS Code**: プロジェクトを開くと「コンテナーで再度開く」と表示されるのでクリック
- **GitHub Codespaces**: GitHub で **"Code" → "Codespaces" → "Create codespace on main"** をクリック

> **注意**: コンテナ内での `clasp login` は `pnpm exec clasp login --no-localhost` を使い、URL ベースの認証フローに従ってください。

### 1. リポジトリを作成

GitHub で **"Use this template"** をクリックし、新しいリポジトリをクローンします：

```bash
git clone https://github.com/<your-org>/<your-project>.git
cd <your-project>
```

### 2. 依存関係をインストール

```bash
pnpm install
```

### 3. clasp をセットアップ

Google アカウントにログインします：

```bash
pnpm exec clasp login
```

新しい GAS プロジェクトを作成します（既存のものを使うこともできます）：

```bash
pnpm exec clasp create --type webapp --title "My GAS App"
```

環境ごとの設定ファイルを作成します（gitignore 済み）：

**`.clasp-dev.json`** — 開発環境：

```json
{
  "scriptId": "YOUR_DEV_SCRIPT_ID",
  "rootDir": "dist"
}
```

**`.clasp-prod.json`** — 本番環境：

```json
{
  "scriptId": "YOUR_PROD_SCRIPT_ID",
  "rootDir": "dist"
}
```

> **ヒント**: Script ID は GAS エディタの「プロジェクトの設定」から確認できます。

### 4. 動作確認

```bash
pnpm run check    # lint + typecheck + test
pnpm run build    # TypeScript バンドル + アセットを dist/ にコピー
```

### 5. デプロイ

```bash
pnpm run deploy       # check → build → dev 環境にデプロイ
pnpm run deploy:prod  # check → build → 本番環境にデプロイ
```

## プロジェクト構成

```text
gas-template/
├── src/
│   ├── index.ts            # GAS エントリポイント（doGet 等）
│   └── index.html          # GAS webapp が配信する Web UI
├── test/
│   └── index.test.ts       # ユニットテスト
├── .github/
│   └── workflows/
│       ├── ci.yml          # CI: lint → typecheck → test → build
│       └── cd.yml          # CD: CI 成功後にデプロイ
├── appsscript.json         # GAS マニフェスト（OAuth スコープ・ランタイム設定）
├── rollup.config.mjs       # バンドラー設定
├── tsconfig.json           # TypeScript 設定（strict モード）
├── jest.config.json        # テスト設定（カバレッジ 100%）
├── eslint.config.mjs       # ESLint 10 フラット設定
├── .prettierrc             # Prettier 設定
├── .stylelintrc.json       # Stylelint 設定（HTML 内 CSS 対応）
├── .htmlhintrc             # HTMLHint 設定
├── .devcontainer/
│   └── devcontainer.json   # Dev Container / Codespaces 設定
├── .mise.toml              # mise ツールバージョン（Node.js, pnpm）
└── renovate.json           # Renovate 自動更新設定
```

## 開発ワークフロー

### 日常の開発

1. `src/` 配下のソースを編集
2. ローカルでチェックを実行：

   ```bash
   pnpm run check
   ```

3. dev 環境にデプロイして動作確認：

   ```bash
   pnpm run deploy
   ```

4. GAS エディタから Web App URL を開いてテスト

### 変更を反映する（PR フロー）

1. フィーチャーブランチを作成
2. 変更してコミット — husky が自動で lint-staged を実行
3. プッシュして Pull Request を作成
4. CI が自動で lint・typecheck・test・build を実行
5. `main` にマージ — CD が本番に自動デプロイ

### コマンド一覧

| コマンド                   | 説明                                              |
| -------------------------- | ------------------------------------------------- |
| `pnpm run lint`            | ESLint（自動修正付き）                            |
| `pnpm run lint:css`        | Stylelint（HTML 内 CSS 含む）                     |
| `pnpm run lint:html`       | HTMLHint                                          |
| `pnpm run format`          | Prettier で全ファイルフォーマット                 |
| `pnpm run typecheck`       | TypeScript 型チェック                             |
| `pnpm run test`            | Jest テスト実行（カバレッジ付き）                 |
| `pnpm run test -- --watch` | Jest ウォッチモード                               |
| `pnpm run check`           | lint + typecheck + test を一括実行                |
| `pnpm run build`           | TypeScript バンドル + アセットを `dist/` にコピー |
| `pnpm run deploy`          | check + build + dev 環境にデプロイ                |
| `pnpm run deploy:prod`     | check + build + 本番環境にデプロイ                |

## テスト

テストは `test/` に置き、Jest で実行します。カバレッジ閾値は全メトリクス（branches, functions, lines, statements）で **100%** に設定されています。

`src/index.ts` はカバレッジ対象外です。GAS 固有のグローバル（`HtmlService` 等）を含むため Node.js では実行できません。

```bash
pnpm run test              # カバレッジ付きで実行
pnpm run test -- --watch   # ウォッチモードで開発
```

### テストの書き方

`test/` に `.test.ts` 拡張子でファイルを作成します。例：

```typescript
import { myFunction } from "../src/myModule.js";

describe("myFunction", () => {
  it("期待する値を返すこと", () => {
    expect(myFunction("input")).toBe("expected");
  });
});
```

## CI/CD

### 仕組み

```text
Push / PR  →  CI (ci.yml)  →  CD (cd.yml)
               ├── Lint          └── Build
               ├── Typecheck         └── clasp push
               ├── Test                  └── clasp deploy
               └── Build
```

CI はすべての push と PR で実行されます。CD は `workflow_run` を使い、`dev` または `main` で CI が成功した場合にのみ起動します。

| トリガー       | パイプライン    | 動作                                                             |
| -------------- | --------------- | ---------------------------------------------------------------- |
| PR to `main`   | CI のみ         | lint → typecheck → test → build                                  |
| Push to `dev`  | CI → CD（dev）  | cancel-in-progress: 新しいプッシュで実行中のデプロイをキャンセル |
| Push to `main` | CI → CD（本番） | キュー: デプロイは順番に実行、スキップしない                     |

### GitHub の設定

リポジトリの Settings で以下を設定します：

**Repository Secret（全環境共通）：**

| 名前           | 値                                         |
| -------------- | ------------------------------------------ |
| `CLASPRC_JSON` | `~/.clasprc.json` の内容（clasp 認証情報） |

**Environment: `development`**

| 種別     | 名前            | 値                                            |
| -------- | --------------- | --------------------------------------------- |
| Secret   | `CLASP_JSON`    | `{"scriptId":"YOUR_DEV_ID","rootDir":"dist"}` |
| Variable | `DEPLOYMENT_ID` | dev のデプロイメント ID                       |

**Environment: `production`**

| 種別     | 名前            | 値                                             |
| -------- | --------------- | ---------------------------------------------- |
| Secret   | `CLASP_JSON`    | `{"scriptId":"YOUR_PROD_ID","rootDir":"dist"}` |
| Variable | `DEPLOYMENT_ID` | 本番のデプロイメント ID                        |

> **`CLASPRC_JSON` の取得方法**: `pnpm exec clasp login` を実行し、`~/.clasprc.json` の内容をコピーします。
>
> **`DEPLOYMENT_ID` の取得方法**: `pnpm exec clasp deployments` を実行し、デプロイメント ID をコピーします。

## カスタマイズ

### OAuth スコープの追加

`appsscript.json` の `oauthScopes` を編集します：

```json
{
  "oauthScopes": [
    "https://www.googleapis.com/auth/script.external_request",
    "https://www.googleapis.com/auth/spreadsheets",
    "https://www.googleapis.com/auth/drive",
    "https://www.googleapis.com/auth/gmail.send"
  ]
}
```

### ソースファイルの追加

1. `src/` にモジュールを作成（例: `src/utils.ts`）
2. `src/index.ts` からインポート — Rollup が単一ファイルにバンドル
3. `test/` にテストを追加

> **重要**: GAS から呼び出せるのは `src/index.ts` のトップレベルで定義された関数のみです。GAS から（または HTML の `google.script.run` 経由で）呼び出したい関数は、必ず `index.ts` からエクスポートしてください。

### カバレッジ閾値の変更

`jest.config.json` の `coverageThreshold` を編集します：

```json
{
  "coverageThreshold": {
    "global": {
      "branches": 100,
      "functions": 100,
      "lines": 100,
      "statements": 100
    }
  }
}
```

## 依存管理

[Renovate](https://docs.renovatebot.com/) で依存関係を自動更新します。設定は共有プリセット [`h13/renovate-config:node`](https://github.com/h13/renovate-config) を使用：

- **minor / patch** アップデートは自動マージ
- **major** アップデートは PR を作成して手動レビュー（`breaking` ラベル付き）
- **devDependencies** はグループ化して自動マージ
- **eslint** や **@types/** パッケージは専用の PR にグループ化
- リリースから **7 日以上** 経過したバージョンのみ更新（安定性バッファ）
- 毎週**日曜 21 時以降**に実行

## ライセンス

[MIT](LICENSE)
