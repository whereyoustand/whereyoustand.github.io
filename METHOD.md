# Method / 方法

This document describes exactly how the test turns answers into a result, and what is known to be weak about it. It is written so that someone who disagrees can point at a specific number, threshold, or coordinate and say why.

**Edition note.** This describes the international English edition. Its scenario nouns use cross-institutional settings (towns, city governments, local councils, residential blocks). The Chinese edition keeps Chinese local-institution scenarios. The 24 dimensions, option values, anchors, thresholds and algorithm are identical; only the scenario nouns differ.

本文说明这套测试如何把答案变成结果，以及它已知的弱点。写这么细，是为了让不同意的人能指着某一个数、某一条门槛、某一个坐标说清楚哪里不对。

---

## 1. What is measured

- **24 dimensions.** Each is a pair of opposed poles (e.g. *centralization / decentralization*), scored from −1 to +1. A negative value means the first pole, positive the second.
- **61 questions.** 51 are scored on the 24 dimensions (two or three per dimension); 10 are descriptive only (attention, efficacy, participation, a ranking question, and one item about whether problems are finite). Descriptive items never enter the vector.
- **54 traditions.** Each is a coordinate in the same 24-dimensional space, written from classical texts, lineage, and living adherents. Two method-type entries (utilitarianism, Nietzscheanism) are marked and excluded from the political ranking.
- **90 historical periods, 17 families.** Used only as a separate reference layer (section 6).

All of it is in `data/`: `dimensions.json`, `questions.json`, `anchors.json`, `anchors_info.json`, `regimes.json`.

---

## 2. From answers to a vector

Each option of a scored question carries a value in [−1, +1] or `null`. `null` means "does not sit on this axis" (e.g. "it depends", "I don't know", "not my business") and is not counted.

For dimension *d*:

```
v_d = mean of the values of all answered, non-null options on d
```

If nothing on *d* was answered, `v_d = 0` and *d* is listed as "no position". Averaging over two questions is deliberate: it lets a person say "housing should be controlled, but most prices should not" and have that recorded as a moderate position rather than forced to one side.

**Why 0.3.** After sanitisation the coordinates use 0, ±0.5, ±1. A threshold of 0.3 therefore means "at least half a step away from neutral". It separates "has a position" from "answered the middle option". It is a design choice, not a measured constant.

---

## 3. Matching a tradition

Only dimensions where **both** the person and the tradition have |value| ≥ 0.3 are compared. Let *k* be their number.

### 3.1 Primary score: smoothed agreement

```
same = number of comparable dimensions where sign(v_d) == sign(a_d)
r    = (same + 1) / (k + 2)
```

This is the posterior mean of a Beta(1,1) prior — Laplace's rule of succession. It pulls small-*k* traditions toward 50%: k=6 with all six agreeing gives 7/8 = 87.5%, while k=18 with all agreeing gives 19/20 = 95%. Without it, a sparse tradition could win by being sparse.

Traditions with *k* < 6 are not eligible at all.

### 3.2 Tie-break: cosine on the comparable dimensions

```
c = Σ(v_d · a_d) / (√Σv_d² · √Σa_d²)    over the k comparable dimensions
```

`r` decides first. Cosine is used only when two traditions are within 0.001 on `r`, so "more dimensions aligned" cannot beat "clearly closer in shape".

---

## 4. The shape gate

A tradition is named only if `c ≥ 0.70`. Below that the result says **"no clear political stance"**, reports the closest shape and its strength, and lists the dimensions where the person *is* clear.

Why a gate at all: `r` only counts signs. A person can agree with a tradition on 8 of 10 dimensions while being far more extreme, or far more moderate, on every one of them. The gate asks whether the *shape* also matches.

Why 0.70: it is an operating point, not a theoretical value. The eleven figures answered from primary texts (Marx, Lenin, Bernstein, Hayek, Burke, Han Fei, etc.) score 0.81–0.98; twelve real answer sheets score 0.10–0.95. 0.70 keeps all eleven figures and labels four of the twelve real sheets. It is explicitly a **precision-over-coverage** choice.

---

## 5. What the result page reports

- The closest tradition, its `k`, `same`, `r` and `c`.
- The gap to the second tradition, in percentage points of `r`.
- Up to three "core principles" (dimensions with |v| ≥ 0.6).
- A three-layer breakdown (political stance / method / worldview).
- Dimensions not compared, split into: genuinely indifferent, context-dependent, two answers pointing opposite, and no clear leaning.
- A historical reference (section 6), or an explicit statement that the profile falls between families.

---

## 6. Historical layer

Separate from the tradition match, and only shown when a tradition was named.

- It uses **ten structural dimensions**: collectivism/individualism, interventionism/laissez-faire, paternalism/liberty, centralization/decentralization, appointment/electoral accountability, status quo/reform, reformism/revolutionism, vanguardism/spontaneism, unitary/pluralist, internationalism/nationalism.
- A family is eligible if `k ≥ 4`, smoothed agreement ≥ 0.72, and cosine ≥ 0.60 against the family centroid.
- Up to three families are shown; the period displayed inside a family is the one closest to the person, not the family centroid.
- If the top two families are within 0.05 cosine, the page says the profile falls **between** them instead of picking one.

The regime layer is deliberately looser than the tradition layer: it compares 10 dimensions instead of 24, so the same statistical bar would eliminate almost everyone.

---

## 7. Calibration and evidence

| Check | Result |
|---|---|
| Each of the 54 anchors answered from its own coordinates → matches itself | 54/54 |
| Eleven figures answered from primary texts → expected anchor in top 3 | 9/11 (Trotsky → classical Marxism; Rawls → social democracy) |
| Twelve real answer sheets | 4 named, 8 "no clear stance" |
| Anchor coordinate jitter σ=0.10 | top-1 drops ~2pp |
| Option-value jitter ±0.15 | top-1 essentially unchanged |
| Tie-breaker experiment (24 follow-up questions) | **rejected**: it lowered figure accuracy from 11/11 to 8–9/11 |

The tie-breaker code is still in the page behind `USE_TIE = false`. It is kept for reproducibility, not used.

---

## 8. Known limitations

These are published on purpose. A method that hides its failure modes cannot be improved by outsiders.

1. **The 70% gate is weak against random answers.** In 3,000 simulated sheets with uniformly random options, **30.2% passed the gate** and received a tradition name. The cause is multiple comparisons: taking the maximum cosine over 54 anchors inflates the top score, and with k≈10 the expected maximum is around 0.76. A stricter gate (`c ≥ 0.80`, `k ≥ 10`, full-vector cosine ≥ 0.58) would cut this to ~2.7% while keeping all eleven figures, but it would also reduce named results among the twelve real sheets from four to two. The change is documented and **deliberately deferred** until more human answer sheets are collected.
2. **Half-real, half-random answers are worse than either.** At 50% faithful answers, 80% still receive a name and 31% receive a *wrong* one. Timing data (section 9) is collected partly to identify these.
3. **Small-k comparisons are noisy.** With k=6 the standard deviation of a random cosine is ≈0.41; with k=24 it is ≈0.20. The smoothing and the gate reduce the damage but do not eliminate it.
4. **No external validation.** There is no self-rating field and no test–retest study. The eleven figures are the only external reference, and they were written by the same author as the anchors — they test internal consistency, not construct validity.
5. **The anchors are one interpretation each.** A tradition's coordinate is an argued position, not a measurement. Reasonable scholars would place Burke or Han Fei differently on at least one dimension.
6. **The historical layer is a reference, not a classification.** Four dimensions of a regime's life (religion, ethnicity, colonial legacy, the ruler's personality) are not measured at all.

---

## 9. Timing data

The page records per-question time, total time, time away from the tab, and the number of changed answers. It is stored only in `localStorage`, shown to the user, and included in the text the user may copy. It **never enters the score**.

It exists for two reasons: to let a reader judge how much weight to give a result, and to let the author find bad questions (a question with a median above two minutes is confusing; one answered in under two seconds by most people has no discriminating power).

---

## 10. Files and license

```
data/dimensions.json     24 dimensions: poles, question, both ends, English name, source
data/questions.json      61 questions: text, options, dimension, option values
data/anchors.json        54 traditions × 24 coordinates (+ clusters, variants)
data/anchors_info.json   per-tradition text: one-line, figures, tenets, misreadings, reading
data/regimes.json        90 periods × 24 coordinates, 17 family centroids, structural dimensions
```

Text and data: **CC BY-NC-SA 4.0**. Code: **MIT**.

If you think a coordinate is wrong, open an issue with the claim and the source. That is the point of publishing the numbers.
