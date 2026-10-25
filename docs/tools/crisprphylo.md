# CRISPRPhylo

Use **CRISPRPhylo** (`/tree-builder`) to infer strain relationships from CRISPR spacer data. You can also request separate array trees within repeat groups. The page displays the strain tree, supports interactive rooting, and exports Newick and tree images. Use [ArrayPhyloViz](figure-composer.md) to combine a chosen tree with arrays.

## Prepare a processed spacer CSV

Use **Processed CSV** from [CRISPRPrep](crispr-processing.md), either transferred through its **CRISPRPhylo** link or uploaded manually. Each row is one spacer; keep the order within each array. All seven columns must be present, even when some cells are empty:

```csv
Folder,Seq_Name,Orientation,Spacer_Sequence,DR_Consensus,Reverse_Complement_Sequence,Reverse_Complement_DR
```

| Field | Meaning / check |
| --- | --- |
| `Folder` | Strain ID. Use the same ID for all arrays from one strain and distinct IDs for different strains. |
| `Seq_Name` | Array ID, consistent across spacer rows from that array. |
| `Orientation` | For `-`, `reverse` or `reversed`, the input validation uses the reverse-complement spacer column. |
| `Spacer_Sequence` | Original spacer sequence; used for other orientations. |
| `DR_Consensus` | Array repeat sequence; preserve the detector-derived value. |
| `Reverse_Complement_Sequence` | Required usable spacer sequence for reverse rows. Blank values can make those rows unusable. |
| `Reverse_Complement_DR` | Reverse-complement repeat; CRISPRPrep supplies it for negative arrays. |

At least two distinct nonempty strain IDs and two usable spacer rows are required. This minimum does not guarantee informative cross-strain evidence. In particular, CRISPRFormer cannot infer a strain tree if no repeat group contains arrays from two different strains. Numbered TXT, genome FASTA and an unprocessed CSV lacking required columns are not valid substitutes.

## Select the method and settings

| Setting | Default | How to choose |
| --- | --- | --- |
| **Tree Inference method** | GraphMSA | GraphMSA builds a neighbor-joining strain tree using graph-based array comparisons. CRISPRFormer uses a pretrained model. |
| **Model profile** (CRISPRFormer) | High-variation data | Choose Conservative data, High-variation data or Mixed data according to your study assumptions. These are different models, not confidence thresholds. |
| **Repeat grouping distance** | 0 | A nonnegative integer edit-distance setting. See the method-specific behavior below before changing it. |
| **Also build array trees** | Unchecked | Enable before submission to generate a ZIP of array trees as well as the strain tree. |

**Repeat grouping distance has different effects in the two current methods.** In CRISPRFormer, 0 groups identical normalized repeats; a positive value allows repeat sequences within that edit distance of a group's representative to join it. One edit is an insertion, deletion or substitution; a value of 1 is not 1% divergence. Assignment uses the first qualifying group, so it is not a guarantee that every pair of repeats within a group is within the threshold.

In GraphMSA, repeat families remain grouped by their repeat sequence. Despite the shared control's name, its current value adjusts **spacer sequence** edit tolerance within those families; increasing it does not merge different repeat families. Keep 0 as a starting point and record both method and value. Do not assume identical numerical settings make the two methods equivalent.

If the model profile or tolerance is uncertain, compare the stability of important groupings across justified settings. Do not raise a threshold solely to obtain a desired grouping.

## Submit and recover a run

1. Select the method, repeat-distance value and optional model profile.
2. Check **Also build array trees** if needed. It cannot be added to a completed result without a new run.
3. Paste CSV or upload one file; use only one input method. **Load example** supplies trial data.
4. Select **Commit**. After a run the same submit control may read **Build tree**.
5. Wait for the Job ID, then save **Copy results link**. Processing continues after the tab closes.
6. Return through [My Jobs](../my-jobs.md) or **Retrieve previous results** to inspect and download the result.

The inference process has a 15-minute limit; queue waiting is separate. Results expire seven days after the task ends. Keep the original CSV and a settings record for reruns. The saved result report retains method/profile, repeat distance and counts, but not the submitted CSV or full run log. A blank **Run log** in a restored result does not by itself indicate failure.

## Read the strain tree

**Leaves are strains.** Check that leaf names correspond to the intended `Folder` values. The saved report's strain count and usable spacer-row count describe the validated input, not statistical support; spacer rows are occurrences rather than distinct spacer types. Check the actual leaves too, since the inference depends on available repeat-group evidence.

**Branching shows inferred grouping.** Vertical order is a drawing arrangement, so adjacent labels are not necessarily the closest pair. A CRISPR-based grouping alone does not establish transmission or a whole-genome relationship.

**Lengths and rooting need separate interpretation.** Numbers after colons in **View Newick source** are branch lengths. Positive supplied lengths guide horizontal distances in the drawing; otherwise the viewer uses topology spacing. Do not interpret the tree as elapsed time without independent calibration. The web workflow does not add a separate bootstrap-confidence assessment.

For example, `((strain_A:0.1,strain_B:0.1):0.2,strain_C:0.3);` groups A and B together in that rooted representation. The numeric branch lengths are not support percentages.

Drag inside the viewer to pan and scroll to zoom. With the viewer focused, use `+` / `-` to zoom and **Home** to fit the complete tree. The initial tree is fitted automatically.

## Choose a root

| Control | Effect |
| --- | --- |
| **Midpoint root** | Places the root halfway along the longest leaf-to-leaf path. Requires known nonnegative lengths on every edge and at least one positive length. |
| **Root on branch** | Enters branch-selection mode. Click a branch at the desired position to place a root there. Keyboard activation of a focused branch selects its midpoint. |
| **Restore original root** | Returns to the original inferred tree supplied by the saved result. |

Press **Escape** to leave branch-selection mode. Midpoint rooting can be unavailable for trees with missing, negative or all-zero branch lengths. Use branch rooting only when you have a justified interpretation, such as an appropriate outgroup; a chosen display root is not new evidence for ancestry.

Root changes update the displayed tree, Newick source and downloads **in the current page**. The saved job retains its original tree. Download the changed Newick before leaving if you need to preserve that root, and use that downloaded tree in ArrayPhyloViz. Downloading directly from My Jobs retrieves the original saved tree.

## Read optional array trees

When requested, **Array trees** reports the generated count and offers **Download array trees (.zip)**. There are no individual array-tree previews on this page; the main preview remains the strain tree.

| ZIP member | How to use it |
| --- | --- |
| `group_*.nwk` | A tree of arrays within one repeat family/group. These leaves represent arrays, not one leaf per strain. |
| `index.csv` | Group identifier, array count, tree filename and status. `skipped_single_array` means the group contained only one array and has no tree. |
| `labels.csv` | Use **both** `group_id` and `tree_label` to recover the original `array_uid` and identify strain/array membership. Labels are unique within a group, not necessarily across all groups. |
| `README.txt` | Description of the archive and its label mapping. |

Array trees reuse the distances from the same analysis. Different files are separate within-group comparisons, not branches of one combined array tree. A count of zero can mean no group had enough arrays; inspect `index.csv` before treating it as an export failure. The ZIP is a separate download and is not embedded in the JSON report. It remains available from restored results until job expiry.

## Export and continue

Select **Download .nwk** for the currently displayed root. Choose **SVG** or **PNG**, then **Download tree**, for an image of the complete current tree independent of pan/zoom. PNG uses 2× rendering and is limited to 32 million pixels and 16,000 pixels per side; use SVG for larger trees.

Keep the input CSV, settings, original Newick, any rerooted Newick, and optional array-tree ZIP. Open **ArrayPhyloViz**, add the chosen Newick and corresponding SpacerMap TXT/SVG, and review mapping: tree leaves use strain `Folder`, whereas array names use `Seq_Name`. Multiple arrays may belong to one strain.

## Troubleshooting

| Symptom | Action |
| --- | --- |
| Missing columns or too few valid rows | Use CRISPRPrep Processed CSV; check strain IDs and orientation-specific sequence fields. |
| No cross-strain repeat group | Check repeat sequences and sample grouping. For CRISPRFormer, reconsider repeat tolerance only if biologically justified. |
| Missing code/model or no output tree | Save the Job ID/error and contact the site administrator. |
| Midpoint root disabled | Inspect Newick lengths; missing, negative or all-zero lengths do not meet the requirements. |
| A root change disappeared on reopening | Expected: the job stores the original tree. Use the rerooted Newick you downloaded. |
| Array-tree ZIP missing | Confirm the option was enabled before submission; reopen interactive results rather than the generic report download. |
