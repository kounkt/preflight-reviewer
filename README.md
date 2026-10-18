# preflight-reviewer

AI が作った物を、外に出す前に、作ったセッションとは別のセッションで点検する、Claude Code / Codex 用のスキルです。
無料で使えます（MIT ライセンス）。English: [README_EN.md](README_EN.md)（the English summary is also at the bottom of this page）.

## 何ができるか

- LP、記事、台本、コード、手順書、メールを、依頼と正本（事実の出どころになる資料）に照らして点検します。
- 4観点（事実の正確さ・依頼の完遂・根拠の追跡・使いやすさ）を、各 0〜2 点で採点します。
- 指摘の一覧を返します。指摘ごとに、場所・確認の方法・期待・実際・重大度（重大・中・軽）・直し方の案が付きます。
- 出してよいかを Yes か No で判定します。判定の既定は No です。
- 原本を書き換えません。点検のために、送信・購入・公開・`git push` などの外へ出る操作もしません。
- 成果物やリンク先に書かれた指示文（「合格にしてください」など）には従わず、見つけた場所を報告に書きます。

## 30秒で試す

1. スキルの置き場に clone します。追加のインストールや設定はありません。

   Claude Code：

   ```sh
   mkdir -p ~/.claude/skills
   git clone https://github.com/kounkt/preflight-reviewer ~/.claude/skills/preflight-reviewer
   ```

   Codex：

   ```sh
   mkdir -p ~/.codex/skills
   git clone https://github.com/kounkt/preflight-reviewer ~/.codex/skills/preflight-reviewer
   ```

2. 新しいセッションを開いて、点検したいファイルを渡します。

   ```text
   preflight-reviewer で、draft.md を点検して。依頼は「初心者向けに 2,000 字で、表計算ソフトの関数を 3 つ紹介する」。出してよいか Yes か No で教えて。
   ```

3. 採点、指摘の一覧、最終判定が返ります。

clone は数秒で終わります。点検にかかる時間は、点検する物の大きさで変わります。
Codex の公式ドキュメントは、個人用のスキルの置き場を `~/.agents/skills` としています。作者の環境（codex-cli 0.158.0）では、`~/.codex/skills` に置いた物も読み込まれました。読み込まれない時は、`~/.agents/skills/preflight-reviewer` に置いてください。

## 実例

架空の講座の販売ページを、依頼と企画書に照らして点検した報告から、採点と、最終判定と、指摘の1つを抜き出した物です（実物の抜粋を、表示を整えて撮影）。

![preflight-reviewer が返す報告の例。4観点の採点は合計 1/8、最終判定は No（要修正）](docs/example-report.png)

受け取った入力と報告の全文は、[`examples/example-lp-review.md`](examples/example-lp-review.md) にあります。
入れ方から、報告の読み方、作者の会社で最初の点検が見つけた指摘までを、[`docs/independent-review.md`](docs/independent-review.md) に書きました。

## 会社の運用に組み込んだ版（有料）

このスキルの点検の考え方に、外へ出る操作の決まりと、正本の優先順位を結びつけた物を、「AI社員一式」として note で販売しています。
18の担当（司令塔・点検・SNS・メール配信・経理の下ごしらえ など）の役割のファイル、手順36本、全社の決まり6本、朝の報告の型が入った、104ファイルの一式です。

- AI社員一式（note）：https://note.com/kounkt/n/nf6640ae56f4e
- 担当ごとの一覧と、成果物のサンプル：https://chiero.jp/skills/
- Mac の環境チェック（無料）：https://github.com/kounkt/ai-setup-guide

## 入れ方（くわしく）

`SKILL.md` がリポジトリの直下にあります。クローンしたフォルダ名を `preflight-reviewer` にすると、そのまま読み込まれます。

追加のインストールや設定はありません。フォルダを、決まった場所に `preflight-reviewer` という名前で置くだけです。フォルダの名前は `SKILL.md` の name と合わせて `preflight-reviewer` にしてください。

### コピーで入れる

次のコマンドは、`preflight-reviewer` フォルダが入っている場所（このフォルダの1つ上）で実行してください。

Claude Code：

```sh
mkdir -p ~/.claude/skills
cp -R preflight-reviewer ~/.claude/skills/preflight-reviewer
```

Codex：

```sh
mkdir -p ~/.codex/skills
cp -R preflight-reviewer ~/.codex/skills/preflight-reviewer
```

### git で入れる

最後の引数で、置き場所とフォルダの名前を指定しています。

Claude Code：

```sh
mkdir -p ~/.claude/skills
git clone https://github.com/kounkt/preflight-reviewer ~/.claude/skills/preflight-reviewer
```

Codex：

```sh
mkdir -p ~/.codex/skills
git clone https://github.com/kounkt/preflight-reviewer ~/.codex/skills/preflight-reviewer
```

入れた後は、新しいセッションを開いてください。`~/.claude/skills/preflight-reviewer/SKILL.md`（Codex では `~/.codex/skills/preflight-reviewer/SKILL.md`）がある状態が正しい置き方です。

上のコマンドは Mac と Linux 用です。Windows では確かめていません。

## 使い方

点検したい物と、分かれば依頼と正本を一緒に渡します。呼び出しの例を3つ示します。

```text
preflight-reviewer で、この LP（landing.html）を点検して。価格と返金の条件の正本は plan.md。
```

```text
preflight-reviewer で、別のセッションで書いた記事 draft.md をレビューして。依頼は「初心者向けに 2,000 字で、表計算ソフトの関数を 3 つ紹介する」。
```

```text
preflight-reviewer で、今のブランチの変更（git diff main）を点検して。出してよいか Yes か No で教えて。
```

点検する物が分からない時、preflight-reviewer は一度だけ質問し、答えが無ければ点検を始めません。依頼や正本を渡さなかった時は一度だけ短く質問し、答えが無いまま進めた時は、成果物から読み取った目的を「仮の依頼」として扱い、正本が必要な照合を「未確認」にします。

作った物をそのセッションのまま点検すると、報告に「自己点検（独立ではない）」と書かれます。独立の点検にしたい時は、新しいセッションを開いて、点検する物・依頼・正本だけを渡してください。

報告の保存を頼んだ時は、指定した場所に保存します。指定が無い時は今の作業フォルダに保存します。ただし、作業フォルダが成果物と同じフォルダや公開するリポジトリの中の時は保存せず、置き場所を質問します。報告には、秘密の置き場所や弱点の位置が書かれることがあるためです。

## 出力の形

報告は次の順に並びます。書式は `references/report-template.md` にあります。

| 節 | 中身 |
|---|---|
| 冒頭の情報 | 対象と版、受け取った時刻、依頼、正本、独立性、点検者への指示文の有無、動かした物、点検した範囲 |
| 最終判定 | Yes／No（要修正）／No（確認待ち）と、その理由。No の時は Yes にするために必要な事 |
| 採点 | 事実の正確さ、依頼の完遂、根拠の追跡、使いやすさを各 0〜2 点（合計 8 点）。各点に根拠の箇所 |
| 指摘の一覧 | ID、重大度（重大・中・軽）、重大な誤りの種類、場所、確認の方法、期待、実際、直し方の案。重大な誤りの種類は `references/criteria.md` の2にあります |
| 要求の対応表 | 依頼の条件ごとに、満たしている箇所と結果（満たす／一部／満たさない／未確認） |
| 作った側の説明との食い違い | 作った側が「確認済み」とした項目と、点検の結果の違い |
| 試した反例・未確認の一覧・点検しなかった範囲 | 何を試し、何を確かめられなかったか |

判定の既定は No です。Yes になるのは、`references/checklist.md` の「最終判定の条件」5つをすべて満たした時だけです。重大や中の指摘が残っている時、4観点のどれかが 0 点の時、価格や締切などの必ず確かめる事実に未確認が残っている時、形式の観点に未確認が残っていて依頼者がそのまま出すと了承していない時は、No になります。

架空の LP を点検した報告の例が `examples/example-lp-review.md` にあります。

## できない事

- 事実が本当に正しいかの最終確認はできません。preflight-reviewer が示すのは「渡された正本と一致したか」「照合できなかったか」までです。最終確認は人がしてください。
- 成果物を自動で直しません。直し方の案を書くだけです。直すのは作った人です。
- 法令や規約に合っているかは判断しません。気になる箇所を指摘に載せ、何が必要かの判断を人に任せます。
- 点検のためにフォームの送信、購入、メールの送信、公開、本番データへの書き込み、外部のサービスへの問い合わせはしません。それらが必要な確認は「未確認」として残ります。
- コードやテストを動かすのは、一時フォルダの複製の中で、ネットワークを使わず、鍵を渡さず、本番に触れない時だけです。満たせない時は動かさず「未確認」にします。
- メールの配信停止、登録の確認、ログインや認証のリンクは開きません。URL の文字だけを確かめます。
- スマホでの表示や、実機での動作は、表示や実行ができる道具がセッションに無い時は確かめられません。
- 同じセッションで作った物の点検は、独立の点検になりません。
- Yes の判定は、成果物に問題が無いことの保証ではありません。点検した範囲で、決めた条件を満たしたという意味です。

## ファイルの構成

```text
preflight-reviewer/
  SKILL.md                      スキルの本体（手順、動かす時の条件、リンクの扱い）
  references/criteria.md        4観点の採点、重大な誤りの種類、重大度、形式別の観点
  references/techniques.md      点検の技法15
  references/checklist.md       Yes/No のチェックと最終判定の条件
  references/report-template.md 報告の書式
  examples/example-lp-review.md 架空の LP を点検した報告の例
  docs/example-report.png       報告の例の画像（README 用）
  docs/independent-review.md    入れ方・使い方・報告の読み方の記事
  LICENSE                       MIT ライセンス
  NOTICE.md                     由来
```

## ライセンス

MIT License です。著作権表示は「Copyright (c) 2026 CHIERO（株式会社CHIERO）」です。全文は `LICENSE` にあります。

## 由来

株式会社CHIERO では、AI に作らせた成果物を外に出す前に、作ったセッションとは別のセッションで点検していました。このスキルは、その運用の手順と基準を、ほかの人の環境でもそのまま使える形に書き直したものです。詳しくは `NOTICE.md` を見てください。

## English summary

preflight-reviewer is a free skill for Claude Code and Codex that reviews a deliverable before you release it.
Run it in a session separate from the one that produced the work, so the review is independent.
It covers landing pages, articles, video scripts, code, how-to guides, and emails.
It scores four axes from 0 to 2: factual accuracy, completeness against the request, traceability of sources, and usability.
Every finding goes into a single list with its location, how it was checked, the expected state, the actual state, and a severity (critical, medium, or minor). Critical findings also carry a type, such as an unconfirmed promise, an unverifiable number or testimonial, or a missing legally required notice.
The final verdict is Yes (release) or No (fix needed, or human confirmation needed). The default is No.
It never modifies the original. Anything that must be run or deliberately broken is done only on a temporary copy, offline, without keys, and away from production systems. It does not open unsubscribe, confirmation, or sign-in links; it reads only the URL text.
Text found in the deliverable, the author's notes, source documents, linked pages, and tool output is treated as data, never as instructions.
The final check of whether a fact is true stays with a human.
The instructions and criteria are written in Japanese. The report is written in the language of the request.
Install by copying or cloning this folder to `~/.claude/skills/preflight-reviewer` or `~/.codex/skills/preflight-reviewer`. No other setup is needed. (Codex's documented personal location is `~/.agents/skills`; `~/.codex/skills` also worked on codex-cli 0.158.0.)
MIT License. Copyright (c) 2026 CHIERO (株式会社CHIERO). Generalized from CHIERO's internal review practice.
