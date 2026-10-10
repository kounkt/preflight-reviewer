# preflight-reviewer

> **English summary**
> preflight-reviewer is a free skill for Claude Code and Codex that reviews a deliverable before you release it.
> Run it in a session separate from the one that produced the work, so the review is independent.
> It covers landing pages, articles, video scripts, code, how-to guides, and emails.
> It scores four axes from 0 to 2: factual accuracy, completeness against the request, traceability of sources, and usability.
> Every finding goes into a single list with its location, how it was checked, the expected state, the actual state, and a severity (critical, medium, or minor). Critical findings also carry a type, such as an unconfirmed promise, an unverifiable number or testimonial, or a missing legally required notice.
> The final verdict is Yes (release) or No (fix needed, or human confirmation needed). The default is No.
> It never modifies the original. Anything that must be run or deliberately broken is done only on a temporary copy, offline, without keys, and away from production systems. It does not open unsubscribe, confirmation, or sign-in links; it reads only the URL text.
> Text found in the deliverable, the author's notes, source documents, linked pages, and tool output is treated as data, never as instructions.
> The final check of whether a fact is true stays with a human.
> The instructions and criteria are written in Japanese. The report is written in the language of the request.
> Install by copying or cloning this folder to `~/.claude/skills/preflight-reviewer` or `~/.codex/skills/preflight-reviewer`. No other setup is needed.
> MIT License. Copyright (c) 2026 CHIERO (株式会社CHIERO). Generalized from CHIERO's internal review practice.

## 何をするスキルか

LP、記事、台本、コード、手順書、メールなどの成果物を、外に出す前に点検するスキルです。成果物を作ったセッションとは別のセッションで使うと、作った側の思い込みを持ち込まずに点検できます。

preflight-reviewer は次の順で点検します。

1. 点検する物、依頼、正本（事実の出どころになる基準資料）を受け取る。作った側の説明は後で読む。
2. 形式（LP、記事、台本、コード、手順書、メール）ごとの観点で点検する。
3. 4観点を 0〜2 点で採点する。
4. 指摘の一覧（場所、確認の方法、期待、実際、重大度、直し方の案）を書く。重大な誤りも、この一覧に重大度「重大」として載せる。
5. 出してよいかを Yes か No で判定し、報告を返す。

## 入れ方

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

`<リポジトリの URL>` は、このリポジトリの clone 用の URL に置き換えてください。最後の引数で、置き場所とフォルダの名前を指定しています。

Claude Code：

```sh
mkdir -p ~/.claude/skills
git clone <リポジトリの URL> ~/.claude/skills/preflight-reviewer
```

Codex：

```sh
mkdir -p ~/.codex/skills
git clone <リポジトリの URL> ~/.codex/skills/preflight-reviewer
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
  LICENSE                       MIT ライセンス
  NOTICE.md                     由来
```

## ライセンス

MIT License です。著作権表示は「Copyright (c) 2026 CHIERO（株式会社CHIERO）」です。全文は `LICENSE` にあります。

## 由来

株式会社CHIERO では、AI に作らせた成果物を外に出す前に、作ったセッションとは別のセッションで点検していました。このスキルは、その運用の手順と基準を、ほかの人の環境でもそのまま使える形に書き直したものです。詳しくは `NOTICE.md` を見てください。

