# 外部ツール採用判定レポート（SPEC §8.1）

> 調査日: 2026-06-17 / 判定者: 外部ツール調査・採用判定担当
> 対象: SPEC §2/§5/§6/§7 が候補に挙げた外部スキル・リポジトリ・検出サービス
> 注: 料金・star数は調査時点。採用前に各公式で最新を再確認すること（SPEC §300 注記）。

---

## §8.1 判定テーブル

| ツール | 実在性 (URL) | ライセンス | 最終更新 | star | 安全性 | 推奨 |
|---|---|---|---|---|---|---|
| hussi9/skill-router | 実在 [link](https://github.com/hussi9/skill-router) | MIT（再配布可） | 2026-06-11 | 9 | **注意**：自動 install スクリプト・catalog フェッチ・"全タスク前に発火"指令あり | **条件付き採用**（SKILL.md の構造のみ流用、スクリプト類は不使用） |
| conorbronsdon/avoid-ai-writing | 実在 [link](https://github.com/conorbronsdon/avoid-ai-writing) | MIT | 2026-06-12 | 1867 | 良好（SKILL.md＋detector JSは純正規表現、外部送信なし） | **採用** |
| blader/humanizer | 実在 [link](https://github.com/blader/humanizer) | MIT | 2026-06-07 | 24551 | 良好（単一 SKILL.md、コード実行なし） | **採用** |
| matsuikentaro1/humanizer_academic | 実在 [link](https://github.com/matsuikentaro1/humanizer_academic) | MIT（"Other"表示だが実体は MIT, ©2025 K.Matsui, blader 派生） | 2026-06-12 | 106 | 良好（単一 SKILL.md） | **採用** |
| brandonwise/humanizer | 実在 [link](https://github.com/brandonwise/humanizer) | MIT | 2026-05-23 | 90 | 概ね良好（CLI＋任意のローカル API サーバ localhost:3000。外部送信なし。要 `npm install`／Node18+） | **条件付き採用**（QAゲートとして。インストールは運用者が手動、§安全制約） |
| skill-improve (OIAE) | サービスとして実在（mcpmarket 掲載）だが**正本 GitHub リポジトリを特定できず** | 不明 | 不明 | 不明 | 検証不能 | **自作推奨**（learnings loop を SPEC §4 仕様で内製。下記参照） |
| avoid-ai-writing-ja (ローカル) | ローカル実在（後述パス） | MIT (©2026 Conor Bronsdon ベース＋多数JP出典) | 導入済み v0.12.0-ja | — | 良好（外部API不要と明記、1738行 SKILL.md のみ） | **採用**（日本語 AI 臭処理の本命） |

検出系サービス（採用対象ではなく §6.2 の手動リスクレーダー）:

| サービス | 実在 | 料金（調査時点） | API | 多言語 | 位置づけ |
|---|---|---|---|---|---|
| Pangram | [link](https://www.pangram.com) | 無料4回/日、個人$20/月600cr、Pro $65/月3000cr。API: $25/500cr〜 | あり（Enterprise） | 20+言語 | 偽陽性最少・高ステークス向き |
| GPTZero | [link](https://gptzero.me) | 無料枠（〜10,000字）＋有料 | あり（/developers） | EN/DE/PT/FR/ES を正式サポート | 知名度高だが偽陽性出やすい |
| Originality.ai | [link](https://originality.ai) | PAYG $30/3000cr、Pro $12.95/月2000cr、Ent $136.58/月〜 | あり（Ent） | 公式に明記なし（英語中心） | パラフレーズ耐性・出版/SEO向き |
| Copyleaks | [link](https://copyleaks.com) | Essential $8.99/月、Business $23.99/月（API限定的） | あり（Ent） | 30+言語 | 多言語向き |

---

## 各ツール所見

### hussi9/skill-router（ルーター足場）— 条件付き採用
- SKILL.md は「3問トリアージ（BROKEN/BUILD/OPERATE）→ Skill+Agent+Model を出力」という決定的ルーティング構造。SPEC §2 のトリアージ設計の手本として有用。
- **要注意点（採用前に必ず無効化）**:
  1. SKILL.md 冒頭に `INVOKE BEFORE EVERY NON-TRIVIAL TASK — Do not skip` という強い常時発火指令。本システムの「ユーザー明示選択を主経路」(§2.1) と競合し得るので、流用時はこの文言を削除/書き換える。
  2. `scripts/ensure-plugin-deps.sh` は **SessionStart フックから `bun install`/`npm install` を自動実行**する設計。`online_catalog_fetcher.py` / `query_online_catalog.py` は外部カタログを取得。`statusline.sh`、`weekly-analysis.sh` 等も同梱。
  3. docs に `curl -sL https://raw.githubusercontent.com/.../SKILL.personal.md | ...` 形式のパイプ実行手順あり。
- **方針**: SPEC §2 の参考実装どおり「自作せず構造を借りる」。`tools/vendor/skill-router/SKILL.md` の**トリアージ表の形だけ**を router/SKILL.md 起草の参考にし、同梱スクリプト・フック・catalog フェッチは一切配線しない。安全のため vendoring は読み取り参照専用。

### conorbronsdon/avoid-ai-writing — 採用（広報・読み物プロファイルの主力）
- star 1867、MIT、活発（2026-06更新）。SKILL.md＋`detector/patterns.js`（純正規表現ベースの検出器、外部送信なし）。`plugins/avoid-ai-writing/skills/avoid-ai-writing/SKILL.md` に Claude Code プラグイン形式も同梱。
- SPEC §5 の「42パターン＋語置換＋構造検出」に合致。広報・読み物の徹底クリーニングに配線。

### blader/humanizer — 採用（音声台本・コピー）
- star 24551、MIT、単一 SKILL.md でコード実行なし。Voice Calibration（書き手サンプルから声を学習）あり。SPEC §5 の「personality/soul 注入」に合致。音声台本プロファイルへ。

### matsuikentaro1/humanizer_academic — 採用（学術・申請）
- star 106、実体 MIT（LICENSE は "MIT License, ©2025 Kentaro Matsui, Based on blader/humanizer"。gh の "Other" 表示は誤分類）。
- formal な学術文体を壊さず AI 臭のみ除去。標準的な接続詞・ヘッジ・機能副詞を温存する設計で SPEC §3 の「学術・申請＝命題高忠実」と整合。**ただし SPEC §6.3 のとおり学術パイプラインの本流に検出系は組み込まない**点に留意（本ツールは書き換え系であり検出系ではない）。

### brandonwise/humanizer — 条件付き採用（全ジャンル横断 QA ゲート）
- star 90、MIT。Node.js CLI（`src/cli.js`）＋統計エンジン（burstiness/TTR/Flesch、28パターン、560+語彙）。`scripts/analyze.sh`/`humanize.sh`、任意のローカル API サーバ（`api-server/server.js`、localhost:3000、CORS）、MCP サーバも同梱。
- **安全性**: 外部送信なし。API サーバは明示起動時のみ動作。`package.json` に postinstall/preinstall フックなし（依存は dev のみ）。
- **採用条件（§安全制約のため当方は未実行）**: 運用者が手動でセットアップ。推奨手順は下記。退行検知（ベースライン比較）を会社レベル QA ゲートに使う設計（SPEC §5 配線原則）。

### skill-improve (OIAE) — 自作推奨
- mcpmarket に「OIAE: Observe/Inspect/Amend/Evaluate でスキルを自己改善、バージョン管理・retrospective・path-discipline ガードを付与」と掲載されているが、**gh search / code search / 主要アグリゲータ（travisvn, Composio）いずれでも正本 GitHub リポジトリを特定できなかった**。ライセンス・保守状況・コード安全性すべて検証不能。
- **方針**: SPEC §4 は learnings loop を自前仕様で定義済み（4.0 役割分担／4.1 二経路昇格／4.2 統治ルール）。OIAE の発想（蓄積ログから再発失敗を抽出→証拠ベース修正→有効性評価で維持/差戻し）は improvement/SKILL.md に**概念として内製**するのが安全。外部依存にしない。

---

## avoid-ai-writing-ja（ローカル導入済みスキル）所在と要約

- **実体パス**: `/Users/roz/Desktop/AI-writing/avoid-ai-writing-ja/plugins/avoid-ai-writing-ja/skills/avoid-ai-writing-ja/SKILL.md`
- **シンボリックリンク**: `~/.claude/skills/avoid-ai-writing-ja` → 上記
- **リポジトリroot**: `/Users/roz/Desktop/AI-writing/avoid-ai-writing-ja/`（README-JA.md, BENCHMARK.md, detector/, scripts/, CHANGELOG-JA.md 等を完備）
- **メタ**: name=avoid-ai-writing-ja, version=0.12.0-ja, **license=MIT**（©2026 Conor Bronsdon ベース）。SKILL.md は約1738行・120KB。`compatibility` に「外部APIは不要」と明記。
- **出典**: conorbronsdon/avoid-ai-writing v3.8.0 ＋ textlint-ja の AI-writing/technical-writing ルールプリセット（azu）＋ gonta223/humanizer-ja ＋ zephel01/oasis-article ＋ 各種日本語 note/Qiita のチェックリスト群。
- **機能**: 3モード（rewrite既定 / detect / edit-in-place）、voice profile（casual/professional/technical/warm/blunt）、context（note/blog/tech-blog/business-mail/docs/sns）、収束反復（最大2パス）。引用・コードブロック・第三者発言は書き換えず検出のみ。
- **検出軸（日本語固有テル, 0〜24+）**: 翻訳調メタテル / チャットボット痕跡 / 婉曲・断言回避 / 重ね丁寧体 / 「〜について」見出し / SEOテンプレ見出し（徹底解説・完全ガイド等）/ 体言止め羅列 / カタカナ語濫用 / テンプレ三段構成（空白三段 vs 実質三項分析の区別）/ 絵文字装飾 / 強調連発 / 過剰CTA / 段落均一性 / em-dash・curly quotes 混入 / タイトルケース過剰 / 「〜と言っても過言ではない」「〜の本質に迫る」「〜の可能性を秘めている」/ 推測の事実化 など。
- **位置づけの自己宣言**: 「シグナルであって証拠ではない」「真偽判定器ではなく執筆品質の道具」。日本語のビジネス/PR 文体は AI 出力と近く誤検知しやすい点を明記。SPEC §6 の「検出器はリスクレーダー」哲学と完全に整合。
- **判定**: SPEC §H1/§9-1 のとおり、日本語 AI 臭処理は**まず本スキルを実機検証**。SEOテンプレ・体言止め・婉曲・チャットボット痕跡など SPEC §5 が例示した日本語 AI 臭（「いかがでしたか」「ポイントは3つ」「体言止め連続」等）をすでに網羅。**自作スコープはほぼ不要、不足分のみ multilingual 手法で補う**判断が妥当。

---

## vendoring 結果（このリポジトリに取り込んだもの）

すべて MIT、テキスト/コードのみ。各クローンの `.git` は削除済み（履歴不要）。**install スクリプトは一切実行していない。**

```
tools/
├── rewrite/
│   ├── avoid-ai-writing/          # conorbronsdon（広報・読み物）
│   ├── humanizer/                 # blader（音声台本・コピー）
│   ├── humanizer_academic/        # matsuikentaro1（学術・申請）
│   └── brandonwise-humanizer/     # brandonwise CLI（横断QAゲート・要手動セットアップ）
├── vendor/
│   └── skill-router/              # hussi9（ルーター構造の参考専用・スクリプト不使用）
└── detect/                        # 検出系の手動チェックリスト置き場（メモのみ、サービスは取り込まない）
```

> avoid-ai-writing-ja は既に `~/.claude/skills/` にローカル導入済みのため、本リポジトリへの再 vendoring は不要（消失リスクが懸念される場合のみ `tools/rewrite/avoid-ai-writing-ja/` へコピーを検討）。

---

## 推奨インストール手順（運用者が手動実行。当方は未実行＝§安全制約）

### brandonwise/humanizer（CLI QA ゲート, Node 18+）
```bash
cd tools/rewrite/brandonwise-humanizer
npm install            # dev依存のみ。postinstallフックなし
# スコア確認
echo "This serves as a testament to innovation." | node src/cli.js score
# ファイル解析 / 自動修正
node src/cli.js analyze -f your-draft.md
node src/cli.js humanize --autofix -f article.txt
# （任意）グローバル導入は信頼後に: npm install -g .
```
※ `api-server/server.js`（localhost:3000）と `mcp-server/` は本用途では起動不要。使わない。

### SKILL.md 系（avoid-ai-writing / humanizer / humanizer_academic）
インストール不要。各 `SKILL.md` を該当プロファイル子スキルから参照/配線するか、`~/.claude/skills/<name>/` に SKILL.md を配置するだけ。実行コードを伴わない。

### hussi9/skill-router
**スクリプトは導入しない。** `tools/vendor/skill-router/SKILL.md` のトリアージ表構造を router/SKILL.md 起草時に参考にするのみ。

---

## 要注意点（セキュリティ）

1. **skill-router**: SessionStart フックから自動で `bun/npm install` する `ensure-plugin-deps.sh`、外部カタログ取得 `online_catalog_fetcher.py`、`curl | sh` 形式の install 手順、SKILL.md の「全タスク前に必ず発火」指令を同梱。**いずれも配線・実行しない**。構造参照専用。
2. **brandonwise**: ローカル API サーバ（CORS 有効, :3000）を同梱。意図せず起動しないこと。外部送信は無し。
3. **rewrite 系3スキル**: コード実行・外部送信・隠れたプロンプト注入は検出されず。安全。
4. **検出系4サービス**: SPEC §6 のとおり合否判定でなくリスクレーダー。翻訳英語は偽陽性が出やすいため単一スコアを証拠にしない。学術・契約パイプラインには組み込まない（§6.3）。
