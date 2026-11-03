# Troubleshooting

## The detector is unavailable

Sequence detection requires the configured CRISPRCasFinder service to be available. Select **Refresh status**. If it remains unavailable, import a `result.json` generated elsewhere or ask the administrator to configure the detector command and working directory.

## A detection job disappeared

Use **My Jobs → Retrieve a job** or **Retrieve previous results** and paste the full ID/private link. Default browser history is tab-local, but the accepted job remains on the server. Results expire seven days after the task ends; after expiry, submit your saved input again. See [My Jobs](my-jobs.md) for recovery and retention.

## JSON produced no spacer rows

Check the evidence threshold, minimum spacer count, orientation fields, and `N` filter. Direct JSON import in SpacerMap applies fixed evidence 3/4 and ND/N rules; use CRISPRPrep for adjustable filtering and a detailed report.

## SpacerMap rejected the input

Use one input method at a time. Numbered text must follow `array_name<TAB>spacer1,spacer2,…`. A direction-folder upload must contain FASTA files beneath a folder whose name includes `pos`/`forward` or `neg`/`reverse`.

## SpacerMap colors changed

Keep the color seed unchanged and rerender. Archive the ID–sequence lookup to record the sequence-to-ID assignment, and the array-map SVG or normalized array text to retain array membership/order. Reuse the latter for plotting; the lookup alone is not array input.

## SpacerMap occurrence chart is disabled or statistics seem incomplete

Charts require 1–50 distinct spacer IDs. Above 50, preview and image export are unavailable; this is not a failed array-map run. **Download statistics CSV** still includes every ID, while **View spacer counts** displays the first 100. To chart a smaller dataset, submit a meaningful subset and identify it explicitly in your analysis.

**Number of arrays** counts each array once per ID. Repeated copies in one array do not add counts, and multiple arrays from one strain are counted separately. Counts use the last completed run; select **Commit** after editing input. If an older saved result lacks statistics, rerun the original or saved numbered input. If the statistics panel could not load, reload the page and retrieve the job; the main array-map workflow can still function.

## Scrolling no longer zooms the array or tree preview

In SpacerMap's array/cluster previews and CRISPRPhylo, hold **Ctrl** while scrolling (**Command** on Mac). Ordinary scrolling moves the page. The occurrence chart has a scrollable preview instead of the array map's zoom/pan controls.

## CRISPRPhylo rejected the CSV

Confirm that the CSV contains the complete required header, at least two distinct nonempty `Folder` values, and at least two usable spacer rows. Negative arrays need a value in `Reverse_Complement_Sequence`.

## The tree pipeline timed out or is missing

The web adapter stops a CRISPRPhylo run after 15 minutes. Reduce the dataset or run the underlying research pipeline outside the website. A missing script, model, or output-tree error requires administrator action on the server.

## Arrays did not match tree leaves

ArrayPhyloViz matches original Newick leaf IDs rather than display labels or row order. Review exact spelling first, then try suffix removal or case/outer-space normalization. Ambiguous normalized names are deliberately left unmatched.

## SpacerMap's numbered download cannot reconstruct arrays

**Download numbered file** contains `Spacer_ID` and `Spacer_Sequence` pairs, not array-per-line data. Use the **array-map SVG** for ArrayPhyloViz. For reusable array TXT, retrieve the SpacerMap job and copy its restored input text, or use the saved report's `export_text`. A number-only input cannot produce a sequence mapping; keep the original sequence data.

## Pasted genome FASTA is rejected

CRISPRPrep paste mode requires `>Sample Seq_Name Organism_Name` for every record. A normal `>contig_1 description` file should be uploaded as a FASTA file or given an explicit sample field before pasting. Keep each sample/sequence pair unique and supply all three fields.

## An exported SVG cannot be reopened

Use the **array-map SVG** from SpacerMap or the integrated figure SVG from ArrayPhyloViz, which carry supported editable metadata. Spacer occurrence charts and cluster-network SVGs do not contain editable array-map data. For older images, reopen the numbered array text and Newick source, or use a saved ArrayPhyloViz project JSON.

## A PNG export failed

ArrayPhyloViz and CRISPRPhylo PNGs are limited to 32 million pixels and 16,000 pixels per side. Reduce PNG scale where available or export SVG. SpacerMap occurrence raster exports have a separate 40-million-pixel limit: reduce the statistics panel's DPI or use SVG/PDF. This does not bypass its 50-ID chart limit.

## Browser results disappeared after refresh

Retrieve the saved private link to reopen server results. Unsaved ArrayPhyloViz edits and CRISPRPhylo root changes are local: save a project/snapshot or rerooted Newick before leaving. A result link restores the saved version only. Keeping browser history does not extend the seven-day server retention.

## SpacerTarget shows no matches

Check whether all query batches completed. A partial/failed job is not evidence of no targets. Clear table filters, inspect **All candidates TSV**, and use **Explore** to inspect lower-coverage or lower-identity alignments. No returned matches refers only to the selected database, hit limit and search configuration.

## SpacerTarget table and network counts differ

Rows are occurrence–site matches; edge weight counts different supporting sequences. Unassigned strains are omitted from the graph and only 200 nodes are displayed. Network type checkboxes can hide nodes without changing table/export filters. Use the filtered TSV to inspect all rows.

## A SpacerTarget task was interrupted

Retrieve the full ID/link and review **Details** and any successful batches. Inputs are deleted at task end, so the current workflow requires resubmission of original spacers for a new search; there is no in-place **Retry failed** operation. Local waiting for resources and remote NCBI searching are separate stages. Use the displayed NCBI request link to inspect a submitted remote request, rather than repeatedly creating duplicate jobs.

## Fewer strains or arrays appear than expected

In CRISPRPrep, uploaded FASTA files define samples by basename without extension, pasted headers define explicit Samples, and NCBI input uses requested accession IDs. Imported JSON uses its basename without `.json`, not its parent folder. Re-importing one combined detector JSON treats it as one sample; rerun the original input to preserve multi-sample grouping. Inspect `Folder` in the original processed CSV. SpacerMap CSV labels combine `Folder_Seq_Name`; check the resulting labels for collisions.

## SeqFetcher exported records hidden by search

Search filters the table, not the selection. The header checkbox affects visible retrieved records; hidden selected rows remain selected. Check the selection count before export. **Retry failed** submits all failed IDs as a new job independently of row selection; save the earlier private link to retain access to successes.

## A job is waiting for resources

If a Job ID is present, keep the link and wait for the accepted task. Inputs remain available while queued and the seven-day result-retention clock starts only when the task ends. If submission returned a busy response without an ID, wait for its retry interval and resubmit. If a job keeps waiting because its estimated resource requirement exceeds available capacity, ask the administrator to check capacity or cancel and submit a smaller dataset. The progress indicator shows activity and phases, not a guaranteed finish time.

## Assembly retrieval failed or returned several FASTA records

Use **Nucleotide** for GCA_/GCF_ IDs. Several contigs under one assembly are expected; the length is their total. Check the failed-ID message for package/version/length validation and size limits. SeqFetcher uses one ID per request when assemblies are present; a single assembly exceeding the response limit still needs another workflow.

## CRISPRPrep cannot rerun with new settings after recovery

Saved results retain outputs, not uploaded source JSON or processing options. Upload your original per-sample JSON again or rerun the original FASTA/accession input. A single-sample detector JSON can be re-imported under a descriptive filename; a combined multi-sample JSON does not retain its sample mapping on re-import. Set the filters explicitly and select **Run**. A form showing default values does not prove those values were used for the retrieved result.

## CRISPRPhylo shows no run log or forgets a new root

The saved report omits the run log and input CSV. Check the job status and tree rather than treating a blank log as failure. Rerooting is local to the current page; download the changed Newick. Restoring the job brings back the original tree. Midpoint rooting requires known nonnegative lengths with a positive tree diameter.

## The array-tree count is smaller than expected

Enable **Also build array trees** before submission. Inspect ZIP `index.csv`: a single-array group is marked `skipped_single_array` and has no Newick file. Use `(group_id, tree_label)` in `labels.csv` to recover original array identities.

## ArrayPhyloViz changes disappear or mappings look wrong

Use **Save changes** to create a new private snapshot, or **Save project** to download editable JSON. Save again after editing merging/order, mapping, styles or layout. An earlier link keeps its earlier state. **Assign by leaf** uses only the text before the first underscore, so names such as `strain_A` require another matching rule or manual mapping. Undo an incorrect assignment before continuing.

## A PDF or example failed to load

For ArrayPhyloViz, save the project JSON, reload, restore it and retry PDF; SVG is an alternative. The PDF libraries and CRISPRPrep example files are served with the application. Repeated missing-file errors require an administrator to check deployed static assets. A failed example load does not submit an analysis; select its format again after the assets are available.

## Reporting a problem

Include the tool name, browser, time of the run, selected settings, exact error message, and the smallest non-sensitive input that reproduces the issue. Do not attach confidential or unpublished sequence data to a public issue.

