# BabelDoc の挙動観察

`il_transform_lm_only.py` において、テキストを LLM に送り翻訳をさせている。

Two columns がある文書のページでのやり取りを抜き出した。

### Prompt

> You are a professional, authentic machine translation engine.
> The first title in the full text: Synbot
> The most similar title in the full text: AI-driven robotic chemist for
> 
> You will be given a JSON formatted input containing entries with "id" and "input" fields. Here is
> the input:
> 
> ```json
> [
>   {
>     "id": 0,
>     "input": "{{v1}}synthesizing organic molecules{{v2}}Synbot employs a multilayered system,
> comprising an AI software layer for chemical synthesis planning, a robot software layer for translating
> commands, and a physical robot layer for conducting experiments. The closed-loop feedback mechanism
> between the AI and the robotic system enables Synbot to develop synthetic recipes with yields equal to
> or exceeding established references (Figure 5.1.4). In an experiment aimed at synthesizing
> M{{v3}}-(2,3-dimethoxyphenyl)- 1H-pyrrolo{{v4}}-b{{v5}}pyridine{{v6}}Synbot developed multiple
> synthetic formulas that achieved conversion yields surpassing",
>     "layout_label": "plain text"
>   },
>   {
>     "id": 1,
>     "input": "Figure 5.1.4",
>     "layout_label": "figure_caption"
>   },
>   {
>     "id": 2,
>     "input": "{{v1}}Synbot design{{v2}}Source: Ha et al., 2023",
>     "layout_label": "figure_caption"
>   },
>   {
>     "id": 3,
>     "input": "{{v1}}Reaction kinetics of M1 autonomous optimization experiment, Synbot vs.
> reference{{v2}}Source: Ha et al., {{v3}}Chart: 2024 AI Index report",
>     "layout_label": "figure_caption"
>   },
>   {
>     "id": 4,
>     "input": "the mid-80% reference range and completed the synthesis in signi cantly less time (Figure
> 5.1.5). Synbot’s automation of organic synthesis highlights AI’s potential in  elds such as
> pharmaceuticals and materials science.",
>     "layout_label": "plain text"
>   }
> ]
> ```

### Response

> ```json
> [
>     {
>         "id": 0,
>         "output":
> "{{v1}}有機分子の合成{{v2}}Synbotは、化学合成計画のためのAIソフトウェア層、コマンドを翻訳するためのロボ
> ットソフトウェア層、実験を実施するための物理ロボット層からなる多層システムを採用しています。AIとロボッ
> トシステム間の閉ループフィードバックメカニズムにより、Synbotは確立された基準と同等またはそれを超える収
> 率を持つ合成レシピを開発することができます（図5.1.4）。M{{v3}}-(2,3-ジメトキシフェニル)-1H-ピロロ{{v4}}
> -b{{v5}}ピリジン{{v6}}の合成を目指した実験では、Synbotは変換収率が超過する複数の合成式を開発しました。"
> ,
>         "layout_label": "plain text"
>     },
>     {
>         "id": 1,
>         "output": "図5.1.4"
>     },
>     {
>         "id": 2,
>         "output": "{{v1}}Synbotの設計{{v2}}出典: Ha et al., 2023"
>     },
>     {
>         "id": 3,
>         "output": "{{v1}}M1自律最適化実験の反応速度論、Synbot対参照{{v2}}出典: Ha et al.,
> {{v3}}チャート: 2024 AIインデックスレポート"
>     },
>     {
>         "id": 4,
>         "output":
> "中80%の基準範囲を超え、合成を大幅に短時間で完了しました（図5.1.5）。Synbotの有機合成の自動化は、製薬や
> 材料科学などの分野におけるAIの可能性を強調しています。"
>     }
> ]
> ```

## 観察

各 id は BabelDOC が一塊の文章だと認識している部分。種類として次のような `layout_label` があるようだ:

- `plain text`
- `figure_caption`
- `abandon`: ページ上下端に出てくる小さい物に振られている

タグ `{{vn}}` はフォントや色などが変化した際のマーカーとして入れられるようである。これが翻訳文に残っていると、それを利用して翻訳文でのフォントや色を設定するのだと思われる。

## 考察

id 0 と id 4 が two columns になっている本文のブロックである。BabelDOC 及び種々の PDF translator はこの二つが連続するものだとは理解していない。そのため、それぞれを独立して翻訳することになり、翻訳結果の質の低下につながっている。
