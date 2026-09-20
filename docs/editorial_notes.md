# Editorial notes

This edition follows the original uploaded homework notebooks. Data files were
inspected only for format, identifiers and clinical categories. No PCA, clustering,
model fitting, enrichment query or other analytical pipeline was rerun.

## Presentation changes

- Replaced assignment-number headings and screenshot duplicates with short English sections.
- Split long code blocks only between complete Python statements.
- Removed repetitive comments and decorative separators; added comments at consequential choices.
- Replaced desktop paths with `data/raw/` and moved future Boruta exports to `results/luminal_subtype/rerun/`.
- Preserved PNG outputs byte-for-byte and archived original stdout. Screenshots duplicating output figures were removed; unique enrichment screenshots were retained.
- Cleared execution counters because cells were split. Saved results are shown explicitly as original-run figures and recorded summaries, not as fresh outputs of edited cells.
- Updated data provenance from the supplied assignment slides: TCGA pan-cancer for HW4 and TCGA-BRCA for HW5/HW6.1.

## Small code maintenance changes

- Added missing HW4 ARI/NMI imports and HW5 `os`, `learning_curve`, `roc_curve`, `auc` and scorer imports.
- Expressed the HW4 two-dimensional cross product as its scalar determinant; the endpoint-distance formula is unchanged.
- Used an explicit MCC scorer in the HW5 grid search; the scoring objective and grid are unchanged.
- Read the clinical CSV with `utf-8-sig` and replaced notebook `exit()` calls with descriptive exceptions.
- Removed HW6.1's unused interaction/cluster/ratio-feature experiments and redundant anonymous-gene remapping. RF importance already carried the gene IDs from the variance-filtered training columns. These removed branches did not feed the reported models.
- Removed unused classifier/search imports in the edited HW6.1 block. Model choices, parameters, label parsing, splits and selection order were otherwise retained.

## Interpretation corrections, without changing the original experiments

**HW4:** the weighted-average choice of k = 5 is a heuristic. Stability scoring
includes self-pairs and treats unobserved pairs as stable. Post-clustering tests use
the same data as the clustering; reported gene contrasts are exploratory. The source
slides do not specify the expression scale, so the original ratio-derived fold
changes are not validated biological fold changes. Set iteration does not establish
a gene importance rank. The unverified drug narrative is omitted.

**HW5:** variance selection, scaling and PCA use the full cohort before splitting.
Cross-validation reuses those fitted PCs. Boruta selects PCs, then candidate genes
are obtained from loadings; it does not directly select genes in this implementation.
Gene loadings do not establish Luminal B upregulation. The enrichment screenshots
lack a complete stored query/results trail. Baseline test MCC slightly exceeds the
tuned model and selected-PC model; no performance improvement is claimed.

**HW6.1:** data include normal-tissue records and repeated patients. The code does
not filter to primary tumors or use patient-grouped splits. Variance filtering is
before the test split; ANOVA selection is before CV. Model selection uses the same
test set as the reported evaluation. Feature importance is from the RF selector,
not the selected LightGBM model. An AUC point estimate above 0.5 alone does not prove
the hypothesis or establish clinical utility.

The original methods and scores remain visible so these qualifications can be
evaluated directly. A future methodological revision would require a separate run;
none is claimed here.

## Static checks performed

Parsed each code cell with Python's AST parser, checked local links and embedded
image references, verified retained image hashes against the originals, inspected
the data headers and checked expression-to-clinical barcode alignment. These are
editorial/schema checks, not validation of predictive performance.
