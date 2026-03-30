# HE Maturity Assessment 出力テンプレート

`cc-he-doctor` エージェントの出力フォーマット定義。

---

## Single Scope Report

```markdown
# Harness Engineering Maturity Assessment ({scope}) [Experimental]

Assessment date: {date}
Target: {path}

> ⚠️ この評価は本プラグイン独自の成熟度モデルに基づいています。
> HE 関連記事群（OpenAI, Anthropic, Martin Fowler 等）のコンセプトを Claude Code 向けに
> 独自に段階化したものであり、業界標準のフレームワークではありません。
> 各項目の [HE原則] / [Claude Code固有] / [独自解釈] ラベルでソースを区別しています。

## HE Maturity Level

**Level: {達成レベル} / 5 ({レベル名})**

| Level | Name | Status | Notes |
|-------|------|--------|-------|
| 1 | Prompt Engineering | ✅/❌ | {specific findings} |
| 2 | Context Engineering | ✅/❌ | {specific findings} |
| 3 | Safety Harness | ✅/❌/— | {specific findings or "前提レベル未達成"} |
| 4 | Feedback Loop | ✅/❌/— | {specific findings} |
| 5 | Full Harness | ✅/❌/— | {specific findings} |

## Detailed Findings

### Level {N}: {Name}

**達成項目:**
- ✅ [HE原則/Claude Code固有/独自解釈] {item}

**未達成項目:**
- ❌ [HE原則/Claude Code固有/独自解釈] {item}: {具体的な状況と根拠}

**推奨項目の状況:**
- ⚠️ [HE原則/Claude Code固有/独自解釈] {item}: {改善提案}

## Next Steps

次のレベル（Level {N+1}: {Name}）達成に必要なアクション:

### Priority: High
- {具体的かつ実行可能な提案}

### Priority: Medium
- {具体的かつ実行可能な提案}
```

## Dual Scope Report (scope: all)

上記を user / project それぞれで出力し、最後に Cross-Scope Findings を追加:

```markdown
## Cross-Scope Findings

- {安全ゲートの配置が適切か（グローバル推奨）}
- {品質フィードバックの配置が適切か（プロジェクトごと推奨）}
- {hooks の競合や冗長がないか}
```

---

## 出力ルール

1. **disclaimer は必ず冒頭に含める** — 省略不可
2. **全ての項目にソースラベルを付与する** — `[HE原則]`, `[Claude Code固有]`, `[独自解釈]` のいずれか
3. **根拠を具体的に示す** — hooks の matcher パターン、deny リストの具体エントリ、ファイルパスを引用する
4. **事実のみ報告する** — 推測や意図の解釈は行わない
5. **数値は正確に記載する** — ファイル数、行数は実際に確認した値を使用する
