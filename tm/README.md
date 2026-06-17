# tm/ — 翻訳メモリ（Translation Memory）

過去の「原文 → 訳文」の対を貯めて、新しい案件で**似た箇所を再利用し、一貫性と速度を上げる**ための置き場。
用語集（`glossary/`）が語レベル、改善ループ（`improvement/learnings.md`）がルールレベルなのに対し、
TM は**文・セグメントレベルの対訳**を扱う。

## ⚠️ 最高機密・ローカル限定（最重要）

- TM は**生の対訳データ**（クライアントの原文・訳文そのもの）。再利用価値を保つため**マスキングしない**。
- そのかわり **絶対に公開・共有しない**。実データは `tm/tm.local.jsonl` に保存し、`.gitignore` で git に乗らない。
- 配布リポに載るのは、この README と雛形 `tm/tm.example.jsonl`（ダミーデータ）だけ。
- 共有が必要な場合は private な経路のみ（SPEC §7・§9-4）。

## ファイル

| ファイル | 追跡 | 中身 |
|---|---|---|
| `tm/README.md` | ✅ 配布 | この説明 |
| `tm/tm.example.jsonl` | ✅ 配布 | 形式を示すダミー（実データではない） |
| `tm/tm.local.jsonl` | ❌ ローカル限定 | 実際の対訳。ここに貯まる |

## 形式（1 行 1 対訳・JSONL）

```json
{"date":"2026-06-17","client":"実名でよい(ローカル限定)","domain":"医療","profile":"profile-academic-grant","source":"原文","target":"訳文","note":"補足"}
```

- `client` はローカル限定なので**実名で記録してよい**（git に乗らないため）。クライアント別の一貫性確認に使う。
- 使い方は `translation-memory` スキルを参照。
