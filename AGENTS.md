# AGENTS.md

駐車禁止は何m？：場所を見て、車をとめてはいけない範囲（その場所の中・1m・3m・5m・10m以内の5つ）を当てる1日5問の学習ゲーム。正解したら、停車もだめ（駐停車禁止）か駐車だけだめ（駐車禁止）かも選ぶ。`index.html` と `data.js`（問題の種類）を GitHub Pages で公開する。

## 準備（最初に一度）

```bash
bash scripts/setup.sh   # Node.js（版は .nvmrc）を確かめ、検査の依存とブラウザを入れる。何度実行してもよい
```

Claude Code のクラウドセッションでは `.claude/settings.json` のフックが自動で実行する。

## push する前の確認

```bash
node qa/run.mjs   # 問題（19種類×3000問：選び肢・答えは1つ・答えと2段目と条文の号が別に書いた表どおり）・今日の5問と上級（3年分）・とことん・通しで遊ぶ・続きから・2段目・復習・幅320px・PWA・引き継ぎ・見やすさ（CI の QA と同じ）
```

## このリポジトリの決まり

- 中身は e-Gov 法令検索の道路交通法 第44条・第45条に書いてあることだけ（2026-11-17 に読んだ）。種類を足すときも条文を読み、`qa/run.mjs` の `TB`（別に書いた表：種類 → 答えの範囲・2段目・条文の号）にも足す。
- 道路標識・標示で別に決まる場所・時間制限駐車区間・第45条第2項（右側の余地3.5m）・反則金や違反点数は出さない。運転免許の試験の対策の代わりにしない。範囲は場所ごとに1つなので、まちがいの選び肢から外す組（`NEVER`）は今は無い。
- 公式の情報の代わりにしない：遊び方と「このゲームについて」と README に、条文を言いかえた文であること・実際は道路標識・標示と警察の案内で確かめることを置く（QA が見る）。
- 問題は、やさしいが選び肢3つ（まぎらわしい範囲 `near` を入れない）、ふつうが4つ、むずかしいが4つ（`near` を1つ必ず入れる。5mと10m、1mと3m、その場所の中と5mなど）。2回目のヒントはまちがいを消して2つにしぼり、条文の言葉（`key`）も見せる。2段目は「停車もだめ？駐車だけだめ？」（2つ）で、第44条の場所か第45条の場所かで決まる（種類ごとの `grp`）。
- 今日の5問（やさしい2・ふつう2・むずかしい1、種類は重ねない）・上級（むずかしい3、今日のむずかしいと別）は日本時間の日付の種で決まり、だれでも同じ。`Math.random()` は練習・とことんの種だけ。乱数は `sort` の比較の中で引かない。`KINDS`・言い回しの並びは最後に足すだけ（QA の `PIN` が第1回の1問目を見る）。`data.js` を変える PR は自動ではマージされない（`hold_paths`）。
- ★はまちがい＋ヒントの数（0で★3・1で★2・2以上で★1）。上級はヒントなし。時間制限なし。
- 好きなだけ遊べる：チケット・ライフ・回数制限・🔒で開くモードを入れない（shared-workflows `routines/game-dev.md` F1〜F7。QA が見る）。とことん（`runs.e`）はまちがえても続き、「ここで降りる」で終わる。
- 1問を深くする：`depth.js` と `qa/depth-check.mjs` は shared-workflows `template/` の写しで、ここでは直さない。自信あり・2段目「停車もだめ？駐車だけだめ？」・復習（1→3→7日後）。記録 `ckr-v1`・文字 `ckr-fs`・復習 `ckr-depth`。
- 共有は遊ぶ人が押したときに開くリンクだけ。シェアの文に問題・答えを出さない。感想は Issue フォーム（`.github/ISSUE_TEMPLATE/feedback.yml`）を開くだけ。応援は Ko-fi へのただのリンクだけ。
- 外へ通信しない（CSP の `connect-src 'self'` を広げない）。見やすさ（`<!-- a11y:begin -->`〜`end`・`qa/a11y.mjs`）と引き継ぎ（`transfer.js`・`qa/transfer-check.mjs`）は shared-workflows `template/` の写しで、ここでは直さない。
- 画面の土台は 届け出いつまで？（todokede-kigen。その元は 危険物の類あて・ 用途地域あて・岩石なかま分け・SDGsの目標あて・震度よみ）から写した。写し元の言葉が残っていないかを QA が見る。
- PWA：画面のファイルを足したら `sw.js` の `SHELL` にも足し、中身を大きく変えたら `CACHE` の版を上げる。アイコンは `node tools/make-icons.cjs`、`og.png` は `node tools/make-og.cjs`。
- 公開リポジトリなので、個人情報・鍵を入れない。

## 公開

- GitHub Pages は `main` のルート（`/`）を公開する。PR の QA と Secret scan が ✅ になると `automerge.yml` が自動でマージする。止めたいときは下書きにするか、ラベル「自動マージしない」を付ける。
- `sw.js`・`manifest.webmanifest`・`data.js` を変える PR は自動ではマージされない（`hold_paths`）。CodeRabbit のレビューを読み、手でマージする直前に今の head へのレビューを読み直す。CodeRabbit は PR に `@coderabbitai review` とコメントして頼む。

## 共通のきまり

- PR の作り方・マージの仕方は satou-aaaaa/shared-workflows の `routines/pr.md`、全体のきまりは `routines/common.md` に従う。
- CI の部品は shared-workflows の共通部品を呼ぶ。リポジトリ側の .github/workflows には呼び出しと設定値だけを書く。
- Claude 以外の AI・人が作業するときは、shared-workflows の `routines/tool-map.md` と `routines/without-claude.md` を読む。
