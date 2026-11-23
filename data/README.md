# Data schema

These files are the source of truth behind the published HTML. All coordinates are on a −1 to +1 scale; a dimension's negative pole is listed first.

| File | Schema |
|---|---|
| `dimensions.json` | `[{id, pole_negative, pole_positive, question, low, high, en, en_source}]` |
| `questions.json` | `[{n, source_id, dimension, type, text, options:[{key, text, value}]}]` — `value: null` means the option does not sit on the axis |
| `anchors.json` | `{name: [24 numbers], "_clusters": {...}, "_variants": {...}}` |
| `anchors_en.json` | English names for the 54 traditions |
| `anchors_info.json` | `{name: {one, figures, tenets, misread, read, history}}` |
| `regimes.json` | `{structural_dimensions, families:[{id,name,description,centroid}], periods:[{id,fam,name,period,place,self,scholar,note,vec,kin?}]}` |

Question numbering (`n`) is presentation order; `source_id` is the stable ID used in the build pipeline. Only questions with `dimension != null` enter the vector.

See [`../METHOD.md`](../METHOD.md) for how these numbers are used.
