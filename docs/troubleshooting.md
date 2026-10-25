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

Keep the color seed unchanged and rerender. Archive the numbered spacer file because it fixes the relationship between spacer sequences and displayed IDs.

## CRISPRPhylo rejected the CSV

Confirm that the CSV contains the complete required header, at least two distinct nonempty `Folder` values, and at least two usable spacer rows. Negative arrays need a value in `Reverse_Complement_Sequence`.

## The tree pipeline timed out or is missing

The web adapter stops a CRISPRPhylo run after 15 minutes. Reduce the dataset or run the underlying research pipeline outside the website. A missing script, model, or output-tree error requires administrator action on the server.

## Arrays did not match tree leaves

ArrayPhyloViz matches original Newick leaf IDs rather than display labels or row order. Review exact spelling first, then try suffix removal or case/outer-space normalization. Ambiguous normalized names are deliberately left unmatched.

## An exported SVG cannot be reopened

Only SVG files exported by the current SpacerMap/ArrayPhyloViz version contain supported editable metadata. For older images, reopen the numbered array text and Newick source, or use a saved ArrayPhyloViz project JSON.

## A PNG export failed

Large composed figures are limited to 32 million pixels. Reduce the PNG scale or export SVG.

## Browser results disappeared after refresh

Retrieve the saved private link to reopen server results. Unsaved ArrayPhyloViz edits and CRISPRPhylo root changes are local: save a project/snapshot or rerooted Newick before leaving. A result link restores the saved version only. Keeping browser history does not extend the seven-day server retention.

## SpacerTarget shows no matches

Check whether all query batches completed. A partial/failed job is not evidence of no targets. Clear table filters, inspect **All candidates TSV**, and use **Explore** to inspect lower-coverage or lower-identity alignments. No returned matches refers only to the selected database, hit limit and search configuration.

## SpacerTarget table and network counts differ

Rows are occurrence–site matches; edge weight counts different supporting sequences. Unassigned strains are omitted from the graph and only 200 nodes are displayed. Network type checkboxes can hide nodes without changing table/export filters. Use the filtered TSV to inspect all rows.

## A SpacerTarget task was interrupted

Retrieve the full ID/link and review **Details** and any successful batches. Inputs are deleted at task end, so the current workflow requires resubmission of original spacers for a new search; there is no in-place **Retry failed** operation. Local waiting for resources and remote NCBI searching are separate stages. Use the displayed NCBI request link to inspect a submitted remote request, rather than repeatedly creating duplicate jobs.

## Fewer strains or arrays appear than expected

In processed CSV, `Folder` groups strains and `Seq_Name` groups arrays. A single CRISPRPrep detection input is one sample even if it contains several FASTA records. Detect different strains separately, then import their JSON together. For custom SpacerMap CSV, make every distinct array's `Seq_Name` unique across strains.

## SeqFetcher exported records hidden by search

Search filters the table, not the selection. The header checkbox affects visible retrieved records; hidden selected rows remain selected. Check the selection count before export. **Retry failed** submits all failed IDs as a new job independently of row selection; save the earlier private link to retain access to successes.

## A job is waiting for resources

If a Job ID is present, keep the link and wait for the accepted task. If submission returned a busy response without an ID, wait for its retry interval and resubmit. The progress indicator shows activity and phases, not a guaranteed finish time.

## Assembly retrieval failed or returned several FASTA records

Use **Nucleotide** for GCA_/GCF_ IDs. Several contigs under one assembly are expected; the length is their total. Check the failed-ID message for package/version/length validation and size limits. SeqFetcher uses one ID per request when assemblies are present; a single assembly exceeding the response limit still needs another workflow.

## CRISPRPrep cannot rerun with new settings after recovery

Saved results retain outputs, not uploaded source JSON or processing options. Upload your original JSON again, or download **CRISPRCasFinder JSON** from a detection result and re-import it. Set the filters explicitly and select **Run**. A form showing default values does not prove those values were used for the retrieved result.

## CRISPRPhylo shows no run log or forgets a new root

The saved report omits the run log and input CSV. Check the job status and tree rather than treating a blank log as failure. Rerooting is local to the current page; download the changed Newick. Restoring the job brings back the original tree. Midpoint rooting requires known nonnegative lengths with a positive tree diameter.

## The array-tree count is smaller than expected

Enable **Also build array trees** before submission. Inspect ZIP `index.csv`: a single-array group is marked `skipped_single_array` and has no Newick file. Use `(group_id, tree_label)` in `labels.csv` to recover original array identities.

## Reporting a problem

Include the tool name, browser, time of the run, selected settings, exact error message, and the smallest non-sensitive input that reproduces the issue. Do not attach confidential or unpublished sequence data to a public issue.

