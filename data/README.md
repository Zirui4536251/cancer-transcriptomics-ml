# Data sources and file formats

The notebooks use course-provided CSV exports from **The Cancer Genome Atlas (TCGA)**.
Source descriptions below were checked against the supplied assignment slides;
headers, identifier formats and clinical categories were inspected in the uploaded
files. The analytical pipelines were not rerun.

## Sources

| Analysis | Source stated in the coursework | Course reference |
| --- | --- | --- |
| HW4: pan-cancer exploration | TCGA pan-cancer project; 801 randomly chosen samples from five tumor types | Homework Assignment 4, slide 1 |
| HW5: Luminal A/B classification | TCGA-BRCA database; 1,190 breast cancer and normal samples, with 26,991 normalized expression measurements | Homework Assignment 5, slides 1–2 |
| HW6.1: N0/N+ prediction | The same expression and clinical files used in HW5 | Homework Assignment 6.1, slide 1 |

The slides do not specify an exact TCGA release, download manifest, expression
unit or log-transform formula. HW5 is explicitly described as normalized expression;
do not label these files as raw counts, TPM, FPKM or log2(TPM+1) without further documentation.
The course exports are the inputs to these notebooks; a fresh TCGA download may
have different samples, gene annotations or processing.

## Files expected locally

Place the following four files in `data/raw/`. HW5 and HW6.1 share the last two files.

| Canonical filename | Uploaded filename | Layout |
| --- | --- | --- |
| `HW4data.csv` | `HW4data(1).csv` | 20,531 gene rows × 801 expression columns; additional first column contains gene IDs |
| `HW4labels.csv` | `HW4labels(1).csv` | 801 sample rows; first column contains sample IDs, followed by `Class` |
| `HW5data.csv` | `HW5data.csv(1).zip` → `HW5data.csv` | 26,991 gene rows × 1,190 expression columns; additional first column contains gene IDs |
| `HW5clinical.csv` | `HW5clinical(1).csv` | 1,190 sample records × 84 named columns, including `barcode` |

Dimensions of expression matrices are documented in the assignment and saved output;
uploaded headers confirm their column layout. The clinical table was read for schema
and category inspection only. No model fitting was performed.

Extract only `HW5data.csv` from the ZIP. Ignore `__MACOSX/._HW5data.csv`, which is
macOS metadata rather than a second expression file. Rename the other downloaded
files to the canonical names above. Do not transpose or alter the CSVs before loading.

## HW4 expression and labels

- Delimiter: comma; first header cell is empty and the first column is the row index.
- Expression columns use IDs such as `sample_0`, `sample_1`, …, `sample_800`.
- Gene IDs are compound strings such as `?|100130426`; retain the entire identifier.
- The labels file has an index column and one data column, `Class`.
- `Class` values are `BRCA`, `COAD`, `KIRC`, `LUAD` and `PRAD`.
- Match expression column names to label index values. The original HW4 code assumes
  they are in the same order; this order was checked in the supplied files.

Schematic layout (ellipses indicate omitted columns or rows):

```text
HW4data.csv
<gene_id>,sample_0,sample_1,...
?|100130426,<expression>,<expression>,...

HW4labels.csv
<sample_id>,Class
sample_0,PRAD
...
```

## HW5 expression and clinical metadata

- Expression has genes in rows and full TCGA aliquot barcodes in columns, for example
  `TCGA-AC-A8OP-01A-11R-A36F-07`.
- Gene IDs include the symbol and versioned Ensembl identifier, for example
  `TSPAN6|ENSG00000000003.15`. Retain both parts; the symbol alone may not be unique.
- `HW5clinical.csv` has a header row and no separate unnamed index column.
- The clinical CSV includes a UTF-8 byte-order mark; use `encoding="utf-8-sig"`.
- Join expression columns to the clinical **`barcode`** field. Do not replace it with
  the shorter `patient` identifier: multiple sample records can belong to one patient.
- This export contains 1,190 distinct barcodes and 1,074 distinct `patient` values.

| Field | Purpose | Observed values / example |
| --- | --- | --- |
| `barcode` | Expression-to-clinical join key | Full TCGA barcode |
| `pam50` | HW5 subtype/group label | `LumA`, `LumB`, `Her2`, `Healthy`, `Basal`, `Normal` |
| `ajcc_pathologic_n` | HW6.1 endpoint source | `N0`, `N0 (i-)`, `N0 (i+)`, `N0 (mol+)`, `N1mi`, `N1`, `N1a`, `N2a`, `N3a`, `NX`, etc. |
| `patient` | Patient grouping for future validation | `TCGA-AC-A8OP` |
| `sample_type` | Tissue-type annotation | `Primary Tumor` (1,078), `Solid Tissue Normal` (112) |

The source `pam50` column uses `Normal`; HW5 displays this as `Normal-like`.
`Healthy` is a course-supplied label for normal-tissue samples, not proof of a
healthy-donor cohort. Observed counts: LumA 560, LumB 209, Her2 82, Basal 188,
Normal 39, Healthy 112.

## HW6.1 label construction retained from the homework

| Original category after parsing | Binary target |
| --- | --- |
| `N0` and N0 suffix variants | 0 (`N0`) |
| `N1`, `N2`, `N3` and their suffix variants | 1 (`N+`) |
| `NX`, missing or unrecognized values | Excluded |

The existing parser includes `N1mi` in N+ and `N0 (i+)` / `N0 (mol+)` in N0.
The recorded modeling cohort contains 553 N0 and 614 N+ sample records. The original
code does not filter to primary tumors; it is not a tumor-only or patient-independent
cohort. This definition is preserved and documented, not clinically reinterpreted.

## Distribution

Raw CSVs are kept locally and excluded by `.gitignore`. The expression ZIP expands
to a large file (approximately 562 MB); it is not included in this portfolio package.
The repository contains the format specification, notebooks, saved figures and
selected recorded results. Authorized course participants can use the matching
NTULearn exports. The assignment PDFs are cited here but are not redistributed.
