# ArrayPhyloViz

Use **ArrayPhyloViz** (`/figure-composer`) to align CRISPR arrays beside an existing tree, annotate the figure and export an editable project. It does not infer a new tree, reroot the tree or change branch lengths. Supply a tree from [CRISPRPhylo](crisprphylo.md) or another analysis and arrays from [SpacerMap](spacermap.md).

## Load a tree and arrays

1. Under **Phylogenetic tree**, paste one Newick tree or upload `.nwk`, `.newick`, `.tree` or `.txt`.
2. Under **CRISPR arrays**, paste numbered arrays or upload TXT/TSV, supported array JSON, or a newly exported SpacerMap SVG.
3. Choose the name-matching rule, then select **Commit**. The tool composes the figure and attempts to save an initial private-link snapshot.
4. Open **Review and edit mapping** before interpreting the figure.

For a first trial, select **Load example**, then **Commit**: the built-in dataset has eight leaves (`C01`–`C08`) and nine arrays and selects suffix matching. Alternatively, paste the following three-leaf walkthrough data with **Exact names**:

```text
((strain_A:0.12,strain_B:0.08)95:0.05,strain_C:0.18);
```

```text
strain_A	1,2,3,6,7,9
strain_B	1,2,4,6,8,9
strain_C	10,2,4,6,8
```

Newick requires unique leaf names and a final semicolon. Quoted labels are supported. Plain internal labels and branch lengths are retained; bracket comments are ignored. In this illustrative tree, `95` is an input internal label: ArrayPhyloViz displays it but does not calculate or validate its statistical meaning.

Use the **array-map SVG** from SpacerMap to preserve colors and source alignment positions. Occurrence charts and cluster-network SVGs do not carry the editable array-map data and cannot be used instead. Use one-array-per-line numbered TXT when only names, IDs and order are needed. SpacerMap's **Download numbered file** now contains `Spacer_ID` / `Spacer_Sequence` pairs, without array membership; it cannot reconstruct the arrays. Use the array-map SVG or copy the normalized array text restored by a SpacerMap job instead. PNG/JPEG screenshots and older SVGs without metadata cannot reconstruct editable arrays. CRISPRCasFinder JSON is not an array-figure project: convert it through CRISPRPrep and SpacerMap first.

When a supported SpacerMap SVG is loaded, **Preserve original SpacerMap style (colors and unique-spacer triangles)** is initially checked. The same choice is available under Layout after composing.

To resume editing, select **Open saved project** and load the JSON produced by **Save project**. A supported ArrayPhyloViz SVG can also restore embedded project data when imported through the array upload control.

## Match arrays to tree leaves

Matching uses **original Newick names**, not row order or edited display labels.

| Rule | Example / behavior |
| --- | --- |
| **Exact names** (default) | `strain_A` matches leaf `strain_A`. |
| **Also strip _CRISPRn / _arrayn** | `strain_A_CRISPR1` or `strain_A_array2` can match `strain_A`. This is a specific suffix rule, not arbitrary substring matching. |
| **Ignore case and outer spaces** | Normalizes case and surrounding spaces; ambiguous normalized matches remain unassigned. |

In **Review and edit mapping**, inspect each array's selected **Tree leaf** and **Status**. Choose the correct leaf manually when necessary. Search can narrow the mapping table. After changing the rule, **Reapply name matching** recomputes assignments, so recheck manual choices. **Assign by leaf** separately matches the part before the first underscore to an exact original leaf name: `702_NZ_JOLF01000035_1` maps to `702`. It recomputes all assignments and leaves nonmatches unassigned; use **Undo** if needed. It cannot recover a leaf such as `strain_A` from `strain_A_CRISPR1`, because the first-underscore prefix is only `strain`. Use the suffix rule or manual selection in that case. Export the final correspondence with **Export mapping TSV**.

Several arrays can map to one strain; they form a group centered beside its leaf. Unassigned arrays remain in the project but are not drawn. Leaves with no mapped arrays remain visible. Neither an unassigned array nor an empty leaf proves biological absence: first check identifiers and mapping.

CRISPRPhylo names leaves from strain `Folder`; SpacerMap CSV labels combine `Folder_Seq_Name` when Folder is nonempty. Use **Assign by leaf** only when the prefix before the first underscore is the full strain name. For names containing underscores, consult the processed CSV and assign leaves manually or use a matching rule appropriate to your actual names.

## Merge or reorder arrays belonging to one leaf

After checking assignments, open **Layout → Merge arrays by leaf**:

1. Select **Merge all by leaf** to place every multi-array leaf's arrays into one display row, keeping their current display order.
2. For individual control, choose a **Leaf** and toggle **Merge into one row**.
3. Use **↑ / ↓** to change left-to-right order when merged, or top-to-bottom order when separate.
4. Use **Undo** to revert a change, or uncheck **Merge into one row** to separate that leaf's arrays again. Save the edited project or select **Save changes**.

Merging is a display operation. It retains every spacer copy and each original array in the project; it does not establish genomic adjacency, reconstruct one biological array or remove repeated IDs. Keep the mapping TSV and original processed CSV to document the underlying arrays.

## Read the combined figure

Read each tree leaf together with its mapped array row or rows. Matching spacer IDs show shared types in the supplied numbering scheme. Numbered TXT from unrelated runs may reuse an ID for different sequences; prepare all arrays together in SpacerMap before combining them.

The tree depicts the relationships already present in the input Newick. Arrays display spacer type and order, not base-pair distances. Shared-spacer links help follow matching IDs; they are not evidence of transfer direction. Colors and row highlights are visual annotations you control, not automatically inferred biological categories.

**Use branch lengths** is off by default. Enable it when you want proportional horizontal tree distances. Scaling is used only if every edge has a nonnegative length and at least one is positive; check the note below the canvas for **Branch-length scale** or **Topology scale**. Turning it off changes the drawing, not the underlying tree.

## Adjust layout and inspect details

Drag empty canvas space to pan. Use **+ / −**, Ctrl/Command plus wheel, or **Fit** to navigate. **Find** locates a leaf; **Expand workspace** enlarges the editing area. Screen zoom does not control export resolution.

| Layout control | Practical use |
| --- | --- |
| **Tree layout** | Choose rectangular or slanted branches without changing relationships. |
| **Array alignment** | Right/left aligns array ends; **Justify (both ends)** uses SpacerMap's repeated-spacer column assignment, preserves order and expands the canvas if needed. It falls back to right alignment when no ID is repeated. **Source positions** uses imported positions when available. |
| **Tree width / Label space / Array width** | Increase the relevant region if branches, long labels or spacers are crowded. |
| **Row spacing / Label size / Branch weight** | Balance readability with figure size. |
| **Internal node labels / support** | Show labels already present in Newick; their meaning comes from the source analysis. |
| **Preserve SpacerMap style** | Available for imported style metadata. Retains the source palette and spacer appearance; spacer ID labels are hidden and their checkbox disabled while this is enabled. Turn it off to show IDs using the editor's style. |
| **Spacer IDs / Row guides / Export spacer legend / Shared-spacer links** | Include the visual aids needed by the reader. Shared-spacer links are on for new projects; saved projects retain their own setting. |
| **Shared-spacer link width** | Adjust line width from 0.2–8 px; enabled when shared-spacer links are shown. |
| **Filename name** | Set the figure's export name. |

## Annotate a selection

1. Select a branch or node label and open **Selection**.
2. Set **Display label**, branch/clade color, label color or clade row highlight.
3. Select **Apply annotation**. Use **Clear annotation** to remove that styling.
4. Use **Rotate children** to swap their display order, or **Collapse clade** to simplify a dense region. **Expand all** restores folded clades.

Display labels do not change matching IDs. Clade colors pass to descendants unless overridden individually. Collapsing hides detail in the view; it does not remove those descendants from the saved tree.

Select a spacer or legend item to edit fill/border colors and choose **Apply to all occurrences**. When an array is selected, **Reverse displayed order** flips its drawing only; it neither infers biological orientation nor changes source IDs. Use this deliberately and retain the project recording the edit. **Undo** and **Redo** help correct editing mistakes.

## Save an editable snapshot

After **Commit**, keep the private results link when saving succeeds. After changing mapping, annotations, layout, merging or displayed array order, select **Save changes** again. Each save creates a new snapshot and Job ID; old links keep their earlier state. Reopen a snapshot with **Retrieve previous results** or [My Jobs](../my-jobs.md). Snapshots expire seven days after the save task ends.

If server saving fails, the current figure remains available. Download **Save project** JSON or retry **Save changes**. A downloaded project is the durable copy for later editing. Uploading/opening a local project does not by itself save subsequent edits to an existing server link.

## Save and export

| Control | Result | What to use it for |
| --- | --- | --- |
| **Save project** | ArrayPhyloViz JSON with tree, original IDs, mapping, settings, labels, colors, reversed arrays, per-leaf merging/order and collapsed clades. | Resume editing with **Open saved project**. Save before closing or refreshing. |
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
| Spacer ID checkbox is disabled | Turn off **Preserve SpacerMap style** if you want the editor to draw ID labels. |
| Assign by leaf loses mappings | It uses only the prefix before the first underscore. Undo and use suffix matching or manual selection for strain names containing underscores. |
| Labels overlap | Increase label space or row spacing, or shorten display labels. |
| Recoloring changes several rows | Expected: spacer color applies to all occurrences of that ID. |
| Branch lengths not reflected | Enable **Use branch lengths** and check the layout note and input lengths. |
| PNG too large | Reduce scale or export SVG. |
| PDF export fails | Retry after refreshing the page and restoring your saved project; use SVG if PDF support could not load. |
| Old SVG cannot be imported | Use numbered TXT plus Newick, or a saved project JSON. |
