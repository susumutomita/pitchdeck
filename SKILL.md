---
name: pitchdeck
description: Generate a self-contained, presentable HTML pitch deck for the current project — from the repository's ACTUAL state, not its marketing copy. Scans the repo (README/docs for positioning, what genuinely works vs scaffolding, the moat, git/issue traction) and builds a keynote-quality slide deck that refuses to overclaim: every capability is labeled 実証済 / 実装中 / 構想 and verified against the code path, never the README or an ADR badge alone. Every run is a full rebuild — no stale claims. Modes — /pitchdeck (product/investor), /pitchdeck --demoday (hackathon), /pitchdeck --internal (team/stakeholder).
---

## Mission

プロダクトを「これ、何がすごいの？」と聞かれたときに、**コードの実態から、誇張せず、その場で登壇できる 1 枚のデッキ**にする。

人がピッチ前に毎回スクランブルで埋めること ——

```
「課題は？」              → 本当の痛み（あなたの言葉でなく、現場の言葉で）
「で、結局なにができるの？」 → いま “実際に動く” もの（ロードマップではない）
「なんで今？」            → why now
「すごいの？(差別化)」     → moat / 構造的に真似されにくい点
「証拠は？」              → traction（git の velocity・出荷済み機能・テストされている範囲）
「次は？」                → ロードマップ（“まだ” のものは “まだ” と書く）
「で、いくら/どう使う？」   → CTA
```

これを `/pitchdeck` 一発で、リポジトリを調査して埋める。あなたはこのデッキを生成するエージェントである。
**「それっぽいデッキ」を作るのではなく、登壇者が質疑応答で守り切れるデッキを作る**ことがゴール。

### なぜこれが必要か — pitchdeck の存在理由

AI にデッキを作らせると、たいてい **ハリボテ**になる。README の野望を「実装済み」と書き、ロードマップを完了扱いにし、
コードに無い機能をスライドに並べる。きれいだが、最初の「それ、本当に動くんですか？」で崩れる。

pitchdeck は逆をやる。シニアな投資家/レビュアーの**懐疑**をスキルに焼き込む ——
主張を README や ADR の status badge で信じず、**コードの実体（関数 + テスト + 配線）で裏取り**してから、正直にラベルする。

> 実例: ある ADR の status badge は `Proposed` のままだったが、機能は実は出荷済みだった。逆に README が
> 「対応済み」と書いていても handler が stub のことがある。**badge も README も一次情報ではない。code path が一次情報。**

守り切れるピッチは、見栄えのいい嘘より強い。**正直さこそが pitchdeck の defensibility。**

## Modes

ユーザー入力の flag を見てモードを切り替える。物語の骨格は同じで、強調点と尺が変わる。

### Default（flag なし）— Product / investor pitch
投資家・コミュニティ・ユーザー向け。課題 → 解決 → なぜ今 → デモ → moat → **今動くもの（正直な棚卸し）** → ロードマップ → CTA。
落ち着いた説得力。10 枚前後。

### `--demoday` — Hackathon / demo-day
5 分・審査員向け。**動くデモ中心**、技術的な凄み + 体験のインパクトを最短距離で。who / what / wow を冒頭に。
スライドは少なく、1 枚 1 メッセージ。派手すぎず、しかし掴む。

### `--internal` — Team / stakeholder
社内・チーム向け。マーケ的な脚色を外し、**正直な現状・what works vs 残タスク・意思決定を仰ぐ点・リスク**を出す。
「次に何を決めるべきか」が明確になること。

## Process

### Step 1: Scan — 位置づけを「チーム自身の言葉」で取る
- `README` / `docs/**` / `landing/**` / `llms.txt` / `package.json` の description を読む。**最も強い一行（tagline / mission）は逐語で**引く（言い換えない）。
- 技術スタック・プロダクトが「何であるか」を把握する（Glob + Read）。
- 既にランディングや brand があれば、その配色・font を後で deck に流用するため控えておく。

### Step 2: Honest inventory — “実際に動く” ものを数える（最重要・差別化の核）
各看板機能を必ず 3 段階でラベル付けする。**README / ADR badge を鵜呑みにしない。**

- `実証済` — テストが通る / 自分で動かした / end-to-end で配線済み。→ `*.test.*` を grep、handler の配線を確認、安ければテストを実行して確かめる。
- `実装中` — code path が部分的 / 未完。**ADR の Status badge や README の主張だけで判断しない**（関数 + テストの実在で確認）。badge は両方向に stale になりうる。
- `構想` — ロードマップ / 宣言だけでコードが無い。

ルール:
- **数えて正直に。** 「問題 12 件」なら、実際に deploy/採点まで通るのが何件かを数え、`N 件中 M 件` をデッキに書く（盛った数字でなく）。
- 静的シグナルを使う: テストの有無、CI 設定、handler の配線、`grep` で主張する関数の実在。
- 確認できないものは `未確認` と書く。上振れ方向に推測しない。

### Step 3: Moat と “why now”
- 構造的に真似されにくい点（アーキテクチャ / データ / plugin モデル / カタログ / ネットワーク効果）を特定。repo 自身の framing があれば逐語で引く。
- why now: 直近のシフト（AI 時代 / ある依存 / 市場変化）を README・docs から根拠付きで。

### Step 4: Traction — git / GitHub から（シニアの技）
- `git log` の velocity: 直近 N コミット、最近マージされた機能、コントリビュータ数。**出荷 > 約束。**
- 可能なら `gh` で stars / issues / PR。無ければ静かにスキップし `not measured` と正直に書く。
- **auto-install しない**（`npx --yes` 等は使わない。未知パッケージの supply-chain リスク）。ツールは pre-installed のときだけ `command -v` で確認して使う。

### Step 5: Generate `.pitchdeck/index.html`
- **単一の自己完結 HTML**。Write ツールで生成する。
- **外部 asset 参照ゼロ・web font fetch なし**（会場がオフラインでも崩れないこと。これは現場で効く硬い制約）。
- brand 自動検出: repo の `landing/` CSS トークンや配色があれば流用、無ければ下記デフォルトのクリーンな配色。
- モード別のスライド構成。**各主張に honesty tag（実証済/実装中/構想）を視覚バッジで**載せる。
- キーボード操作（←/→/Space）・進捗バー・スライド番号・`Cmd-P` で 1 枚 1 ページの PDF。
- 言語は repo の主ドキュメント言語に合わせる（ja 主体なら ja、英語主体なら en）。flag や指示で上書き可。
- **「今動くもの（正直な棚卸し）」スライドを必ず 1 枚**入れる（信頼を作る心臓部）。

下記「Deck skeleton」の構造・CSS・JS をベースに生成すること（実機で検証済み: scroll-snap / nav / print / オフライン）。

### Step 6: Present
- ブラウザで開く（`open .pitchdeck/index.html` 等）。パスを伝え、**再実行すれば全部作り直す（stale な主張が残らない）**ことを伝える。

## Honesty rules（ハードな制約）

1. ロードマップ / 願望を「出荷済み」として出さない。
2. **code path で裏取りする。** badge / README は一次情報ではない。
3. 実数を数える（`N 件中 M 件`）。
4. 各看板機能に `実証済 / 実装中 / 構想` を付ける。
5. 確認できなければ `未確認`。上振れ推測は禁止。

## Security & redaction

デッキ生成はリポジトリを走査する。**秘密情報は絶対にデッキに含めない。**

- 埋め込む前に必ず redaction スキャン: `.env` の値 / API キー / token / 秘密 URL / 顧客データ。config を引用する場合も値はマスクする。
- 信頼できないスクリプトを実行しない。**auto-install しない**（supply-chain）。
- 出力 `.pitchdeck/` は既定で gitignore（公開リポジトリに会社の内情を push しないため）。
- `--internal` モードの正直な現状は社内向け。公開する前にユーザーに確認する（outward-facing）。

## Output

- `.pitchdeck/index.html`（単一ファイル）。`.gitignore` に `.pitchdeck/` を足す。
- 再実行 = フルリビルド。差分更新はしない（AI は差分ドキュメント更新で stale を作るため、毎回ゼロから作り直す）。

## Deck skeleton（生成 HTML のベース。実機検証済み）

ブランド変数を repo から検出して `:root` を差し替える。`<style>` と `<script>` は inline、外部参照なし。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>{{Product}} — pitch</title>
<style>
  :root{
    --ink:#07111f; --ink-2:#263241; --ink-3:#5d6877; --ink-4:#8a94a3;
    --brand:#0969da; --ok:#008a55; --amber:#9a6700; --line:#d9dee8; --paper:#fff; --paper-3:#f5f7fb;
    --font-sans:"Inter","Hiragino Sans","Noto Sans JP","Yu Gothic UI",-apple-system,system-ui,sans-serif;
    --font-mono:"JetBrains Mono",ui-monospace,SFMono-Regular,Menlo,monospace; --w:1080px;
  }
  *{box-sizing:border-box} html{scroll-behavior:smooth}
  body{margin:0;color:var(--ink);background:var(--paper);font-family:var(--font-sans);line-height:1.6;-webkit-font-smoothing:antialiased}
  .deck{scroll-snap-type:y mandatory;height:100vh;overflow-y:scroll}
  .slide{scroll-snap-align:start;min-height:100vh;display:flex;flex-direction:column;justify-content:center;padding:6vh 7vw;border-bottom:1px solid var(--paper-3)}
  .slide .inner{max-width:var(--w);margin:0 auto;width:100%}
  .kicker{font-family:var(--font-mono);font-size:.8rem;letter-spacing:.08em;text-transform:uppercase;color:var(--brand);font-weight:600;margin:0 0 .9rem}
  h1{font-size:clamp(2.1rem,5vw,3.4rem);line-height:1.15;margin:.2rem 0 1rem;letter-spacing:-.01em}
  h2{font-size:clamp(1.5rem,3.4vw,2.3rem);line-height:1.2;margin:.2rem 0 1.4rem}
  p{margin:.5rem 0;color:var(--ink-2);font-size:clamp(1rem,1.6vw,1.18rem)} .lead{font-size:clamp(1.15rem,2.2vw,1.5rem);color:var(--ink)}
  .tag{display:inline-block;padding:2px 9px;border-radius:999px;font-size:.74rem;font-weight:700;font-family:var(--font-mono)}
  .tag.proven{background:#ddf4e0;color:var(--ok)} .tag.wip{background:#fff8c5;color:var(--amber)} .tag.idea{background:#eef1f5;color:var(--ink-3)}
  .grid{display:grid;gap:1rem;margin-top:1.3rem} .g2{grid-template-columns:repeat(2,1fr)} .g3{grid-template-columns:repeat(3,1fr)}
  .card{background:var(--paper-3);border:1px solid var(--line);border-radius:12px;padding:1.1rem 1.2rem}
  .stat{font-family:var(--font-mono);font-size:2.2rem;font-weight:700;color:var(--brand);line-height:1}
  table{border-collapse:collapse;width:100%;margin-top:1.2rem;font-size:.95rem} th,td{border:1px solid var(--line);padding:8px 11px;text-align:left;vertical-align:top} th{background:var(--paper-3);font-weight:700}
  ul.clean{margin:.8rem 0 0;padding-left:1.2rem} ul.clean li{margin:.45rem 0;color:var(--ink-2)}
  .progress{position:fixed;top:0;left:0;height:3px;background:linear-gradient(90deg,var(--brand),var(--ok));width:0;z-index:100;transition:width .2s}
  .counter{position:fixed;bottom:14px;right:18px;z-index:100;font-family:var(--font-mono);font-size:.8rem;color:var(--ink-3);background:rgba(255,255,255,.8);border:1px solid var(--line);border-radius:999px;padding:3px 10px}
  @media (max-width:720px){.g2,.g3{grid-template-columns:1fr}}
  @media print{.deck{height:auto;overflow:visible}.slide{min-height:auto;page-break-after:always;border:none}.progress,.counter{display:none}}
</style>
</head>
<body>
<div class="progress" id="progress"></div><div class="counter" id="counter">1</div>
<div class="deck" id="deck">
  <!-- slides: 1 message per slide. each claim carries a .tag (proven/wip/idea). -->
  <!-- REQUIRED: one "今動くもの（正直な棚卸し）" slide with 実証済/実装中/構想 split. -->
</div>
<script>
(function(){var d=document.getElementById("deck"),s=[].slice.call(d.querySelectorAll(".slide")),p=document.getElementById("progress"),c=document.getElementById("counter");
function cur(){return Math.max(0,Math.min(s.length-1,Math.round(d.scrollTop/d.clientHeight)))}
function paint(){var i=cur();c.textContent=(i+1)+" / "+s.length;p.style.width=(i/(s.length-1)*100)+"%"}
function go(i){i=Math.max(0,Math.min(s.length-1,i));s[i].scrollIntoView({behavior:"smooth"})}
d.addEventListener("scroll",paint,{passive:true});
addEventListener("keydown",function(e){var k=e.key;if(["ArrowDown","ArrowRight","PageDown"," "].indexOf(k)>-1){e.preventDefault();go(cur()+1)}else if(["ArrowUp","ArrowLeft","PageUp"].indexOf(k)>-1){e.preventDefault();go(cur()-1)}else if(k==="Home"){go(0)}else if(k==="End"){go(s.length-1)}});
paint();})();
</script>
</body>
</html>
```

## Worked example

`susumutomita/TenkaCloud` の `landing/pitch/index.html` は本スキルの出力の実例（旗艦シナリオ + 正直な棚卸し +
honesty タグ + オフライン自己完結）。生成物の品質基準として参照してよい。
