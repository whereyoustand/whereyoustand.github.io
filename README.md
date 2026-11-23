# Where You Stand

A 61-question test that maps your answers onto **24 dimensions** of political judgement, then compares the result with **54 traditions** and **90 historical regimes**.

It names a tradition only when the shape matches. When nothing fits, it says so — and shows which dimensions you left blank.

- **Take it**: [index.html](index.html)
- **Method and data**: [METHOD.md](METHOD.md)
- **Online**: https://whereyoustand.github.io/

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

This English edition uses cross-institutional settings — towns, city governments, local councils, residential blocks — rather than the Chinese local institutions in the Chinese edition. The dimensions, option values, anchors and algorithm are identical; only the scenario nouns differ. The Chinese edition is published separately.

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

---

## License

Text and data: **CC BY-NC-SA 4.0** — share and adapt with attribution, non-commercial, same license.
Code: **MIT**.

---

## Disclaimer

This is an educational tool for self-understanding. The result is an approximate description of answer patterns. It carries no moral judgement, states no political position, and makes no claim about any existing government.
