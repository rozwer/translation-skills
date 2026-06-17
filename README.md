# 多ジャンル翻訳支援スキルシステム

ジャンルごとに最適な翻訳処理を自動で選び、使うほど会社専用に育つ翻訳支援システム。
Claude Code（Desktop の Code タブ）上で運用する。

> **仕様の正本は [SPEC.md](./SPEC.md)。** 本 README は導入・運用手順のみを扱い、
> 設計の詳細は重複させず SPEC.md を参照する（SPEC §M2）。

> ## ⚠️ このリポジトリは「配布専用（ダウンロード元）」
> public で公開しているのは**枠組み（スキル・仕様・空のテンプレート）**だけ。ここで会社の中身を育てるのではなく、**ダウンロードして手元の導入先で育てる**。
> - **このリポには育成データ（learnings の蓄積ルール・glossary の実用語・ルーター実ログ・原稿）を push しない。** 配布する雛形だけを置く。
> - 育成は各導入先（ローカルの作業コピー）で行い、その成果はこの配布リポには戻さない。共有が必要なら別途 private な経路を使う（SPEC §7・§9-4）。
> - クライアント原稿・実名・実名⇔トークン対応表は当然 commit しない（`.gitignore` で多重に防止。最後の砦は人の確認）。
> - 配布物を更新して push する前は、`git status` と差分を目視し、機密や育成データが混じっていないか確認する。

---

## これは何か（30秒）

1. 翻訳タスクを投げると、**ルーター**が「どのジャンルか」を聞いて判定する。
2. ジャンル別の**プロファイル**（音声台本／広報／学術申請／契約公文書）が、そのジャンルに合った忠実さ基準・チェック・NG集で処理する。
3. あなたの修正・指摘を**改善ループ**が記録し、承認したものを会社専用ルールとして育てる。

詳しい設計思想は SPEC.md を参照。

---

## 入手（HTTP ダウンロード）

公開URL: https://github.com/rozwer/translation-skills

```bash
# 方法1: git clone（更新を pull できる。推奨）
git clone https://github.com/rozwer/translation-skills.git

# 方法2: HTTP で zip を落とす（git 不要）
curl -L -o translation-skills.zip \
  https://github.com/rozwer/translation-skills/archive/refs/heads/main.zip
unzip translation-skills.zip

# 方法3: tar.gz
curl -L https://github.com/rozwer/translation-skills/archive/refs/heads/main.tar.gz | tar xz
```

落としたフォルダを Claude Code（Desktop の Code タブ）で開けば、`.claude/skills/` のスキルがそのまま使える。

## ディレクトリ構成

| 場所 | 役割 |
|---|---|
| `SPEC.md` | 仕様の正本 |
| `.claude/skills/translation-router/` | ルーター（ジャンル判定・振り分け） |
| `.claude/skills/profile-*/` | ジャンル別の処理スキル |
| `.claude/skills/translation-improve/` | 改善ループ（指摘のルール化） |
| `common/house-style.md` | 全ジャンル共通ルール・機密マスキング |
| `improvement/learnings.md` | 指摘ログ（承認待ち＋確定ルール） |
| `glossary/` | 用語集・定訳集（匿名化済みのみ git 管理） |
| `tools/` | 外部ツールの調査結果・取り込み（`tools/ADOPTION.md`） |

---

## 使い方（日々の運用）

1. Claude Code の Code タブでこのプロジェクトを開く。
2. 翻訳・推敲したい原稿を渡し、「翻訳して」「この英訳を直して」と指示する。
3. ルーターの質問（成果物の種類・忠実さ・制約・レビュー要否・機密度）に答える。
4. 処理結果を確認し、修正したい点を伝える。
5. セッション終わりに **「改善ループを起動して」** と指示する。`translation-improve` スキルが今回の指摘を棚卸しし、「この指摘をルール化しますか？」と差分で提案する。**承認すると会社専用ルールになる**（差分レビューUIが承認ゲート）。
   - 改善ループを起動しないと指摘が蓄積されず、システムが育たない。セッション終了時の習慣にすること。

---

## 機密・セキュリティ（必読）

- **クライアント原稿は原則このリポジトリの外に置く。** 中に置く場合も `manuscripts/` 等は `.gitignore` 済みで git に乗らない。
- 実名は記録時に匿名トークン（`<CLIENT_A>` 等）へ置換する（`common/house-style.md` §1）。
- 実名⇔トークン対応表（`*.local.json`）は**絶対に共有しない**（`.gitignore` 済み）。
- 外部スキルは導入前に `tools/ADOPTION.md` の安全性判定を確認する（SPEC §8）。
- Claude のデータ学習設定はオフにする（SPEC §7）。

---

## 配布と育成の分離（SPEC §7）

- **この public リポ = 配布専用**。枠組み（スキル・仕様・空テンプレート）を置き、ダウンロード元にする。
- **育成（learnings・glossary の蓄積）は各導入先のローカルで行い、ここには戻さない。**
- 配布物そのもの（スキルの改良など）を更新したいときだけ、機密・育成データが混じっていないことを確認して push する。
- 二人で育成成果を共有したい場合の経路（private repo / Team プラン等）は SPEC §9-4 で判断中。

---

## セットアップ状況（最終更新: 2026-06-17）

- [x] リポジトリ骨格・機密ファースト `.gitignore`
- [x] 共通レイヤー（house-style / learnings / glossary）
- [x] 各スキル（router / profiles ×4 / improve）
- [x] 外部ツール採用判定・vendoring（`tools/ADOPTION.md`）
- [ ] ドメイン content の本格起草（SPEC §9-1・運用者の専門知識が必要）
- [ ] 足場の実機検証（`avoid-ai-writing-ja` の日本語AI臭カバレッジ確認ほか・SPEC §9-3）
- [ ] GitHub（private）リモート整備
