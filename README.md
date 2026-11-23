# Where You Stand

A 61-question test that maps your answers onto **24 dimensions** of political judgement, then compares the result with **54 traditions** and **90 historical regimes**.

It names a tradition only when the shape matches. When nothing fits, it says so — and shows which dimensions you left blank.

- **Take it (English)**: https://whereyoustand.github.io/
- **中文版**: https://whereyoustand.github.io/zh/
- **Method and data**: [METHOD.md](METHOD.md)

---

## 中文说明

一套 61 题的自我定位测试：把答案映射到 **24 个维度**，再与 **54 个政治传统**、**90 个历史时期**对照。

**它只在形状对得上时才给名字。** 对不上就说「没有明确的政治立场」，并列出你在哪些维度上是清楚的、在哪些维度上留了空白。多数人属于后者——这不是缺陷。

- **24 个维度**：不只有「大政府 / 小政府」，还有「规则因能说出理由而有效，还是因被传下来而有效」「历史由决定推动，还是由趋势推动」这类问题。每维一对相反的极，取值 −1 到 +1。
- **54 个传统**：从无政府共产主义到秩序自由主义，从儒家到世界联邦主义，按原典、传承与在世信徒编写。只在**双方都明确表态**的维度上比较。
- **90 个历史时期**：不是给你贴标签，是给你参照——这套偏好真的被实施过，长这样。分不开两族时它会说「落在交界处」，而不是硬挑一个。

方法与数据全部公开在 [METHOD.md](METHOD.md) 和 [data/](data/)，**包括已知缺陷**：3000 份随机作答里有约 30% 能通过当前的 70% 判定线。这一条写在文档里，不藏。

**边界**：不做地区、职业、群体的聚合统计；不评价任何制度；不构成诊断。

---

## What it does

Most political quizzes put you on a left–right line. This one does three things instead:

**1. It measures 24 dimensions, not one.** Not just "big government vs small government", but: is a rule valid because it can state its reason, or because it was handed down? Is history driven by decisions or by trends? Is technology neutral? Does obligation fade with distance? Each dimension is a pair of opposed poles, scored from −1 to +1.

**2. It compares you with traditions, not with a compass.** 54 anchor traditions — from anarcho-communism to ordoliberalism, from Confucianism to world federalism — each defined by its classical texts, its lineage, and its living adherents. Matching uses a smoothed agreement rate over the dimensions where *both* you and the tradition take a clear position, with cosine similarity deciding near-ties.

**3. It shows you a regime that actually ran.** 90 historical periods, grouped into 17 families — not as a label but as a reference: *this set of preferences has been implemented, and this is what it looked like*. When it cannot separate two families, it says so instead of picking one.

---

## How the matching works

1. Each question maps to one dimension, each option to a value in [−1, +1].
2. Answers are averaged per dimension → a 24-dimensional vector.
3. A dimension only counts if **both** sides have |value| ≥ 0.3. This blocks the common artefact where "no opinion" is silently read as "mild disagreement".
4. Primary ranking is a **Laplace-smoothed agreement rate** `(same + 1) / (k + 2)`, where `k` is the number of comparable dimensions. Smoothing stops a tradition with only 6 comparable dimensions from scoring 100% by accident.
5. When two traditions are within 0.001 on that rate, **cosine similarity** of the two vectors decides — so "more dimensions aligned" cannot beat "clearly closer in shape".
6. **A tradition is named only if its shape also matches.** Even with a high agreement rate, the cosine similarity over comparable dimensions must reach 0.70. Below that the result says "no clear political stance", lists the dimensions where you *are* clear, and explains why the shape does not fit any tradition.
7. The historical layer uses only **ten structural dimensions**. A regime family is shown only if its centroid also passes a rate and cosine floor.

The full account — including the calibration data, the known failure modes, and the numbers behind every threshold — is in [METHOD.md](METHOD.md).

---

## Open method

Everything that decides a result is in this repository, not hidden in a server:

| File | What it contains |
|---|---|
| [`METHOD.md`](METHOD.md) | The full algorithm, calibration evidence, and known limitations |
| [`data/dimensions.json`](data/dimensions.json) | 24 dimensions: both poles, the question behind each, English names and sources |
| [`data/questions.json`](data/questions.json) | 61 questions, every option, and the value each option carries |
| [`data/anchors.json`](data/anchors.json) | 54 traditions × 24 coordinates |
| [`data/anchors_en.json`](data/anchors_en.json) | English names for the 54 traditions |
| [`data/anchors_info.json`](data/anchors_info.json) | Per-tradition text: one-line summary, figures, tenets, misreadings, reading |
| [`data/regimes.json`](data/regimes.json) | 90 periods, 17 family centroids, and the ten structural dimensions |

The published HTML is self-contained; these files are the source of truth it was built from. If a coordinate looks wrong, an issue can cite the exact number.

---

## A note on scenarios

This English edition uses cross-institutional settings — towns, city governments, local councils, residential blocks — rather than Chinese local institutions.

The Chinese edition at [/zh/](zh/) keeps the original Chinese settings (villages, village committees, counties and townships). The 24 dimensions, option values, anchors, thresholds and algorithm are identical; **only the scenario nouns differ**. A figure answering from primary texts lands on the same tradition in both editions.

---

## What it is not

- **Not a diagnosis.** The output describes a pattern of answers, not a person.
- **Not a recommendation.** Nothing here says which system is better.
- **Not a test you can fail.** There are no right answers, and "it depends" is a legitimate answer that is recorded as such.
- **Not exhaustive.** 24 dimensions cannot hold a political worldview, and the test says so on the result page — including which dimensions you left blank.

---

## Privacy

Everything runs in your browser. Answers are stored in `localStorage` under the key `pv4en`. Nothing is uploaded, collected, or aggregated. There is no analytics, no cookie, no server-side component.

**Timing.** The page records how long each question took, total time, time away from the tab, and how many answers were changed. This is shown to **you** at the bottom of the result page and included in the text you can copy. It is stored only in your browser, never transmitted, and it does not affect the result.

---

## Rebuilding

The HTML is generated, not hand-edited. The published file is self-contained. The data files above are included; the Python build scripts are not.

---

## Contributing

Corrections to historical facts, translations, and misreadings of a tradition are welcome — open an issue with the specific claim and a source. Disagreements about where a tradition sits on a dimension are also welcome, but bring the citation.

Three issue templates are provided (correction / question wording / method). They are for **errors and ambiguity**, not for arguing about politics: the repository takes no position on which system is better.

Please also note what this project will **not** do, by design:

- It will not publish aggregate distributions by region, occupation, or any other group. Turning test results into "which province is more liberal" is exactly the use this design exists to avoid.
- It will not tell you which tradition you *should* hold.

---

## License

Text and data: **CC BY-NC-SA 4.0** — share and adapt with attribution, non-commercial, same license.
Code: **MIT**.

---

## Disclaimer

This is an educational tool for self-understanding. The result is an approximate description of answer patterns. It carries no moral judgement, states no political position, and makes no claim about any existing government.

**It is not a psychometric instrument.** There are no population norms, no reliability or validity testing, and the calibration set is small — 11 profiles written from primary texts and 12 human answer sheets. Treat it as a structured way of describing your answers, not as a measurement of a trait.

It is also deliberately **not** framed as entertainment. A toy does not need published thresholds and a documented false-positive rate; this does, which is why those numbers are in [METHOD.md](METHOD.md).
