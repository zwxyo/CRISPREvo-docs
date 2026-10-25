# SpacerMap

Use **SpacerMap** (`/spacer-map`) to compare spacer order and sharing across CRISPR arrays. It produces an array diagram and, when clustering is enabled, an array network. Genome FASTA must first go through [CRISPRPrep](crispr-processing.md).

## Prepare your input

Choose **one** input source per run. Leave the paste box empty when uploading.

| Input | How to prepare it |
| --- | --- |
| Numbered arrays | Paste one named array per line, or upload TXT/TSV. Each number identifies a spacer type. |
| Spacer sequences in a table | Paste or upload **Processed CSV** from CRISPRPrep, retaining its header; each row is one spacer. CRISPRPrep can also transfer it directly. |
| CRISPRCasFinder JSON | Upload one JSON. Direct import keeps evidence levels 3–4 and applies fixed direction/N filters. Use CRISPRPrep first to control filtering. |
| Spacer FASTA | Upload one FASTA containing one array, with one record per spacer. The filename becomes the array name. This is not whole-genome input. |
| Direction folders | Select a folder with spacer FASTA files under `pos`/`forward` or `neg`/`reverse`. Each file is one array. |
| Arrays written as DNA sequences | Upload TXT/TSV with one named array per line. Use upload for sequence-to-number conversion; pasted array-format text is treated as labels, while a recognized CSV header triggers CSV conversion. |

Try this numbered example, or select **Load example**:

```text
strain_A_CRISPR1	1,2,3,6,7,9
strain_B_CRISPR1	1,2,4,6,8,9
strain_C_CRISPR1	10,2,4,6,8
```

Use a tab between each name and list. Keep array names unique. In custom CSV, `Seq_Name` is the array grouping field: two strains must not reuse the same array name. `Folder` alone does not separate SpacerMap arrays. CRISPRPrep makes array names unique during processing.

## Choose alignment and clustering

| Alignment | What changes | When it helps |
| --- | --- | --- |
| **Right** (default) | Last spacers line up at the right edge. | Compare ends of consistently oriented arrays. |
| **Left** | First spacers line up at the left edge. | Compare starting blocks. |
| **Justify** | Shared spacers guide horizontal spacing while preserving order. | Follow shared internal blocks across arrays of different lengths. |

Alignment is a drawing choice. Left/right does not determine the biological leader or trailer end. Establish orientation from your source data before assigning acquisition direction. Justified gaps are visual spacing, not measured genomic gaps or proof of deletion.

| Clustering | Exact grouping rule | Interpretation |
| --- | --- | --- |
| **No clustering** (default) | Keeps input row order. | Useful when samples already have a meaningful order. |
| **Shared spacer** | Links pairs sharing at least two distinct IDs; connected arrays form a group. | Two arrays can join through a third without sharing two IDs directly. |
| **Ancestral spacer** | Groups arrays with the same **rightmost spacer ID**. | A terminal-spacer grouping heuristic; it does not reconstruct an ancestor or establish which spacer is oldest. |

Clustering reorders rows, never spacer order within an already normalized array. Select **Commit** again after changing either mode.

## Run and read the diagram

1. Paste the example or upload your input.
2. Select alignment and clustering; **Right** and **No clustering** are a useful first view.
3. Select **Commit** and wait for the preview.
4. Check array names and the summary above the preview.
5. With clustering enabled, select **Show cluster network** to inspect pairwise connections.

Each row is an array; each symbol is one spacer occurrence. IDs identify types within this dataset. Shared types use consistent colors. Types found in only one array are drawn as triangles, including every repeated copy within that array. A triangle means *array-specific*, not necessarily a spacer seen only once. Use IDs as the primary reference when colors look similar.

Links between neighboring rows connect matching spacer IDs. A break in links does not by itself identify a biological gain or loss. Check IDs, order and orientation together.

The summary's **spacer labels** count is the number of distinct IDs, not the total number of symbols. **Shared labels** occur in at least two arrays. The example above has 3 arrays, 9 distinct labels and 6 shared labels, with 17 total occurrences. IDs 3, 7 and 10 are array-specific.

In the cluster network, nodes are arrays. A line indicates that the pair meets the selected grouping rule. Network proximity is a layout aid, not evolutionary distance. The network is unavailable with **No clustering**.

## Navigate and recover the preview

Drag to pan and scroll to zoom. The **+ / −** and **Fit** controls adjust the preview; with the preview focused, `0` fits the diagram and arrow keys pan. These changes do not crop or resize exported figures.

Rendering creates a saved job. Copy its private results link to restore SVG, numbered arrays, alignment, clustering and seed through [My Jobs](../my-jobs.md) for seven days after the task ends. Keep your original data and numbered download for reruns. Changing the seed triggers another render; save the link for the version you intend to retain. Recovered results do not restore the original upload for arbitrary new submissions, so reselect your source when rerendering.

## Understand automatic spacer numbering

For sequence inputs, sequences with pairwise global-alignment identity of at least **0.84** are connected, and each connected group receives an ID. Similar sequences can share an ID without being identical; transitive groups can include members below 0.84 identity to one another. Numbering depends on the dataset and ordering. ID `1` in two independent runs is not a universal spacer identity.

CSV/JSON and direction-folder inputs use orientation information during conversion. Review the resulting numbered arrays before interpreting left/right ends, especially if you prepared or reordered the CSV yourself. Already numbered input is drawn in supplied order. Numeric FASTA headers are used directly as IDs.

Reuse the downloaded numbered file when comparing figures. For sequence-level searches, retain original sequences and use [SpacerTarget](spacer-target.md); numeric IDs cannot be searched.

## Export and continue

In **Customize export**, set **Seed**, **Format** and, for raster output, **DPI**. Seed defaults to 2 and changes colors, not IDs or clustering. Wait for the preview update before downloading. Preserve the same dataset, order and seed to reproduce the palette.

| Output | Use it for |
| --- | --- |
| SVG | Scalable figures and importing array metadata into ArrayPhyloViz. |
| PNG | Slides or image previews. |
| JPG/JPEG, TIF/TIFF, WEBP | Workflows requiring these formats. Raster DPI defaults to 300; allowed range is 36–2,400. |
| **Download numbered file** | Reusable TXT for automatically converted arrays. Enabled only when conversion produced a numbered file; retain the original for already numbered input. |

To combine arrays with a tree, open [ArrayPhyloViz](figure-composer.md), upload a newly exported SVG or numbered TXT, and add a Newick tree. SVG carries colors and source positions; numbered TXT carries names, IDs and order.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| No usable JSON arrays | Direct import excludes low evidence, unresolved `ND` and organisms with N-containing spacers. Use CRISPRPrep to see exclusion reasons. |
| Two samples appear as one row | Give each array a unique `Seq_Name` in custom CSV. |
| Unexpected shared IDs | Numbering groups similar sequences; inspect original sequences for exact identity. |
| No cluster network | Select a clustering mode and commit again. Isolated nodes are valid when no pair meets the rule. |
| IDs/colors differ between analyses | Reuse the same numbered input and seed; IDs are not global across datasets. |
