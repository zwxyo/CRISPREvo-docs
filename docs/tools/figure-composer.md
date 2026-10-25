# ArrayPhyloViz

Use **ArrayPhyloViz** (`/figure-composer`) to align CRISPR arrays beside an existing tree, annotate the figure and export an editable project. It does not infer a new tree, reroot the tree or change branch lengths. Supply a tree from [CRISPRPhylo](crisprphylo.md) or another analysis and arrays from [SpacerMap](spacermap.md).

## Load a tree and arrays

1. Under **Phylogenetic tree**, paste one Newick tree or upload `.nwk`, `.newick`, `.tree` or `.txt`.
2. Under **CRISPR arrays**, paste numbered arrays or upload TXT/TSV, supported array JSON, or a newly exported SpacerMap SVG.
3. Choose the name-matching rule, then select **Commit**. The tool composes the figure and attempts to save an initial private-link snapshot.
4. Open **Review & edit mapping** before interpreting the figure.

For a first trial, select **Load example**, then **Commit**. This self-contained example uses matching strain names:

```text
((strain_A:0.12,strain_B:0.08)95:0.05,strain_C:0.18);
```

```text
strain_A	1,2,3,6,7,9
strain_B	1,2,4,6,8,9
strain_C	10,2,4,6,8
```

Newick requires unique leaf names and a final semicolon. Quoted labels are supported. Plain internal labels and branch lengths are retained; bracket comments are ignored. In this illustrative tree, `95` is an input internal label: ArrayPhyloViz displays it but does not calculate or validate its statistical meaning.

Use a SpacerMap SVG to preserve colors and source alignment positions. Use numbered TXT when only names, IDs and order are needed. PNG/JPEG screenshots and older SVGs without metadata cannot reconstruct editable arrays. CRISPRCasFinder JSON is not an array-figure project: convert it through CRISPRPrep and SpacerMap first.

To resume editing, select **Open saved project** and load the JSON produced by **Save project**. A supported ArrayPhyloViz SVG can also restore embedded project data when imported through the array upload control.

## Match arrays to tree leaves

Matching uses **original Newick names**, not row order or edited display labels.

| Rule | Example / behavior |
| --- | --- |
| **Exact names** (default) | `strain_A` matches leaf `strain_A`. |
| **Also strip _CRISPRn / _arrayn** | `strain_A_CRISPR1` or `strain_A_array2` can match `strain_A`. This is a specific suffix rule, not arbitrary substring matching. |
| **Ignore case and outer spaces** | Normalizes case and surrounding spaces; ambiguous normalized matches remain unassigned. |

In **Review & edit mapping**, inspect each array's selected **Tree leaf** and **Status**. Choose the correct leaf manually when necessary. Search can narrow the mapping table. After changing the rule, **Reapply name matching** recomputes assignments, so recheck manual choices. Export the final correspondence with **Export mapping TSV**.

Several arrays can map to one strain; they form a group centered beside its leaf. Unassigned arrays remain in the project but are not drawn. Leaves with no mapped arrays remain visible. Neither an unassigned array nor an empty leaf proves biological absence: first check identifiers and mapping.

A common real-data mismatch is `Folder` versus `Seq_Name`: CRISPRPhylo names leaves from strain `Folder`, whereas SpacerMap names arrays from `Seq_Name`. Use the processed CSV to look up the correct strain for each array and assign it explicitly. Suffix matching only helps if your names actually follow that convention.

## Read the combined figure

Read each tree leaf together with its mapped array row or rows. Matching spacer IDs show shared types in the supplied numbering scheme. Numbered TXT from unrelated runs may reuse an ID for different sequences; prepare all arrays together in SpacerMap before combining them.

The tree depicts the relationships already present in the input Newick. Arrays display spacer type and order, not base-pair distances. Shared-spacer links help follow matching IDs; they are not evidence of transfer direction. Colors and row highlights are visual annotations you control, not automatically inferred biological categories.

**Use branch lengths** is off by default. Enable it when you want proportional horizontal tree distances. Scaling is used only if every edge has a nonnegative length and at least one is positive; check the note below the canvas for **Branch-length scale** or **Topology scale**. Turning it off changes the drawing, not the underlying tree.

## Adjust layout and inspect details

Drag empty canvas space to pan. Use **+ / −**, Ctrl/Command plus wheel, or **Fit** to navigate. **Find** locates a leaf; **Expand workspace** enlarges the editing area. Screen zoom does not control export resolution.

| Layout control | Practical use |
| --- | --- |
| **Tree layout** | Choose rectangular or slanted branches without changing relationships. |
| **Array alignment** | Right/left aligns array ends; **Source positions** uses imported positions when available, such as a SpacerMap SVG. |
| **Tree width / Label space / Array width** | Increase the relevant region if branches, long labels or spacers are crowded. |
| **Row spacing / Label size / Branch weight** | Balance readability with figure size. |
| **Internal node labels / support** | Show labels already present in Newick; their meaning comes from the source analysis. |
| **Spacer IDs / Row guides / Export spacer legend / Shared-spacer links** | Include the visual aids needed by the reader. |
| **Filename name** | Set the figure's export name. |

## Annotate a selection

1. Select a branch or node label and open **Selection**.
2. Set **Display label**, branch/clade color, label color or clade row highlight.
3. Select **Apply annotation**. Use **Clear annotation** to remove that styling.
4. Use **Rotate children** to swap their display order, or **Collapse clade** to simplify a dense region. **Expand all** restores folded clades.

Display labels do not change matching IDs. Clade colors pass to descendants unless overridden individually. Collapsing hides detail in the view; it does not remove those descendants from the saved tree.

Select a spacer or legend item to edit fill/border colors and choose **Apply to all occurrences**. When an array is selected, **Reverse displayed order** flips its drawing only; it neither infers biological orientation nor changes source IDs. Use this deliberately and retain the project recording the edit. **Undo** and **Redo** help correct editing mistakes.

## Save an editable snapshot

After **Commit**, keep the private results link when saving succeeds. After changing mapping, annotations, layout or displayed array order, select **Save private link** again. Each save creates a new snapshot and Job ID; old links keep their earlier state. Reopen a snapshot with **Retrieve previous results** or [My Jobs](../my-jobs.md). Snapshots expire seven days after the save task ends.

If server saving fails, the current figure remains available. Download **Save project** JSON or retry **Save private link**. A downloaded project is the durable copy for later editing. Uploading/opening a local project does not by itself save subsequent edits to an existing server link.

## Save and export

| Control | Result | What to use it for |
| --- | --- | --- |
| **Save project** | ArrayPhyloViz JSON with tree, original IDs, mapping, settings, labels, colors, reversed arrays and collapsed clades. | Resume editing with **Open saved project**. Save before closing or refreshing. |
| **SVG** | Vector figure with embedded editable project data. | Further figure editing and supported re-import. |
| **PNG** | Complete raster figure at 1×, 2× (default) or 3×, independent of screen zoom. | Slides and previews. |
| **Newick** | Full tree in edited child order with original names and lengths. | Downstream tree tools; display labels/colors are not exported as tree annotations. |
| **PDF** | Direct PDF download sized to the complete figure. | Share a figure without the browser print dialog. Most elements remain vector; non-ASCII labels are rendered as images to preserve their appearance. Use SVG/project JSON for editable text and project state. |
| **Export mapping TSV** | Array-to-leaf assignments. | Audit sample matching alongside the figure. |

Keep the project JSON, mapping and final figure together. An image alone does not preserve a separately readable mapping report or the original sequence evidence.

## Limits and troubleshooting

The editor accepts one Newick up to 1 MB, 1,000 leaves, 4,000 nodes and depth 250. Array text is limited to 4 MB, with up to 1,500 arrays, 2,000 spacers per array and 20,000 total spacer instances. PNG export is limited to 32 million pixels and 16,000 pixels on either side.

| Symptom | Action |
| --- | --- |
| Arrays missing beside leaves | Review unassigned rows and original names; map them manually. |
| Labels overlap | Increase label space or row spacing, or shorten display labels. |
| Recoloring changes several rows | Expected: spacer color applies to all occurrences of that ID. |
| Branch lengths not reflected | Enable **Use branch lengths** and check the layout note and input lengths. |
| PNG too large | Reduce scale or export SVG. |
| PDF export fails | Retry after refreshing the page and restoring your saved project; use SVG if PDF support could not load. |
| Old SVG cannot be imported | Use numbered TXT plus Newick, or a saved project JSON. |
