# Downloads

Save the output appropriate to your next step. A private results link restores a job for seven days; it is not a permanent file archive. Tool-specific downloads are available after **Open results** from [My Jobs](../my-jobs.md).

| Tool | Download | Contents and use |
| --- | --- | --- |
| SeqFetcher | FASTA | Selected full-length records, deduplicated by returned accession. An assembly contains multiple contigs in one FASTA. |
| SeqFetcher | Metadata TSV / ZIP | Selected metadata; ZIP adds combined/per-accession FASTA, metadata JSON, run report and failed/pending ID lists. |
| CRISPRPrep | CRISPRCasFinder JSON | Generated detector output, available for detection jobs. Re-import it to change filters or combine samples. Uploaded source JSON must be kept separately. |
| CRISPRPrep | Unprocessed CSV | `spacers_unprocessed.csv`: spacer rows after evidence/count filtering, before direction/N normalization. |
| CRISPRPrep | Processed CSV | `spacers_processed.csv`: filtered/normalized rows and reverse-complement/effective-repeat fields. Use in SpacerMap, CRISPRPhylo or SpacerTarget. |
| CRISPRPrep | Processing report | Counts, warnings, array outcomes and retrieval failures where applicable. Saved processing options are omitted; record them separately. |
| SpacerMap | Numbered TXT | Array names and ordered spacer IDs from sequence conversion. Preserve these for consistent reuse. |
| SpacerMap | SVG / raster image | Array diagram; supported SVG carries editable array metadata. PNG/JPEG/TIFF/WEBP use the chosen DPI. |
| CRISPRPhylo | Download .nwk | Current displayed tree, including local rerooting. My Jobs' direct tree download retains the original inferred root. |
| CRISPRPhylo | Download tree | SVG or PNG of the complete current strain tree, independent of zoom/pan. |
| CRISPRPhylo | Download array trees (.zip) | Optional group Newick files, `index.csv`, `labels.csv` and `README.txt`; enable before running. Not included inside the result JSON. |
| SpacerTarget | Export filtered TSV | Match rows passing current table filters and node/edge focus, across all table pages. |
| SpacerTarget | All candidates TSV | All returned alignments without Strict/current table filtering; not an exhaustive database search. |
| SpacerTarget | Search report | Retained query/occurrence identifiers, match evidence, annotation status and batch history. Terminal reports omit original full sequences, parameters and raw BLAST responses. |
| SpacerTarget | PNG / SVG | Current association network view. Preserve TSV/report as supporting evidence. |
| ArrayPhyloViz | Save project | Editable local project JSON with mapping, tree, styles and figure edits. |
| ArrayPhyloViz | Save private link | A server snapshot of the project with its own ID and expiry; later edits require another save. |
| ArrayPhyloViz | Mapping TSV | Array-to-leaf assignments, spacer counts and displayed reversals. |
| ArrayPhyloViz | SVG / PNG / PDF | Complete integrated figure. SVG embeds project data; PDF is downloaded directly and is not a replacement for the editable JSON. |
| ArrayPhyloViz | Newick | Full tree in edited child order, using original names and branch lengths. Display annotations live in the project. |
| My Jobs | Export job links | Plain-text links for importing history on another device; no sequence data or figures are included. |

## Does a visible filter affect downloads?

| Tool | Scope |
| --- | --- |
| SeqFetcher | Selected retrieved rows, including selected rows hidden by search. The header checkbox affects only visible rows. |
| CRISPRPrep | All rows produced by the run's processing settings, regardless of preview search/page. |
| SpacerTarget | Filtered TSV follows table filters and graph focus; All candidates ignores them. Network type-visibility checkboxes affect the drawing only. |
| SpacerMap / CRISPRPhylo | Complete figure, independent of preview zoom/pan. CRISPRPhylo uses the current local root. |
| ArrayPhyloViz | Complete figure at selected export resolution; collapsed clades and styles affect the drawing. |

SpacerTarget table rows count occurrence–site matches; network edge weights count different supporting query sequences. Those totals need not agree. After input cleanup, the TSV `sequence` column can be blank while alignment evidence remains; keep original input FASTA/CSV for complete spacer sequences.

## Minimum analysis bundle

Keep original accessions/FASTA/JSON, sample identities, a separate settings record, processing report, processed CSV, numbered arrays, original and any rerooted Newick, optional array-tree ZIP, figure mapping/project and final images. For target searches add original spacers, database/hit limit/filter settings, all candidates, filtered TSV and search report.

Record analysis date and software version when available, method/profile, repeat-distance value, processing thresholds, alignment/clustering, color seed and relevant figure edits. Server reports omit some input-related fields by design; do not rely on a result link alone to reproduce an analysis.
