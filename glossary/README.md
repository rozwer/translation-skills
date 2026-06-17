# glossary/ — 用語集

> クライアント別・分野別の用語集／定訳集。SPEC.md §3.5・§7。

## 置けるもの（git に乗せてよい）
- **匿名化済み**の定訳集・分野別用語集（例: `medical-terms.md`, `<CLIENT_A>-style.md`）
- トークン（`<CLIENT_A>` 等）を見出しにした表記ルール

## 置いてはいけないもの（git に乗せない＝`.gitignore` 済み）
- 実名⇔匿名トークンの対応表 → `glossary/_realnames.local.json` に置く（追跡されない）
- クライアント名・固有名詞の実名そのものを含むファイル（`*client-map*`, `*.local.json` 等）

## 使い方
- 翻訳・推敲時、固有名詞や専門用語は**まずここを参照**して定訳から外れないようにする（`common/house-style.md` §2.2）。
- 新しい定訳が確定したら、匿名化した上でここに追記し、必要なら `improvement/learnings.md` 経由でルール化する。

## 対応表テンプレート（ローカル限定・git 追跡外）
`glossary/_realnames.local.json`:
```json
{
  "<CLIENT_A>": "（実名。このファイルは絶対に共有しない）",
  "<PRODUCT_X>": "（実名）"
}
```
