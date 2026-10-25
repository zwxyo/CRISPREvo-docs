# SpacerTarget

Use **SpacerTarget** (`/spacer-target`) to search original CRISPR spacer sequences against NCBI **core_nt** and inspect candidate matches in a table and strain–target network. It helps identify sequence associations worth investigating. The search does not evaluate PAMs or experimentally validate targets, infection relationships or host range.

## Prepare spacer sequences and sample identities

Each spacer must contain **15–100 A/C/G/T bases**. Resolve ambiguous bases before searching. Supply spacer sequences, not whole genomes or SpacerMap numbers. Use pasted FASTA/table text or upload files; do not combine pasted text with uploads.

### FASTA

Use `>strain|array|spacer` so matches retain their strain and array assignments:

```fasta
>strain_A|CRISPR1|spacer_1
CGCCAGGGTTTTCCCAGTCACGACGTTGTAAA
>strain_B|CRISPR2|spacer_1
CGCCAGGGTTTTCCCAGTCACGACGTTGTAAA
```

This illustrative input has **two occurrences but one distinct query sequence**. A match is linked back to both strains. It does not guarantee a particular NCBI result.

An alternative header is `>spacer_1 strain=strain_A array=CRISPR1 source=NC_000000.1`. Replace the placeholder with the real source accession if known. Source metadata enables source-record matching checks.

### CSV/TSV

A header is required. `sequence` or `Spacer_Sequence` supplies the original sequence. Add sample and array fields to make the network useful:

```csv
strain_id,array_id,spacer_id,sequence,source_accession
strain_A,CRISPR1,spacer_1,CGCCAGGGTTTTCCCAGTCACGACGTTGTAAA,
strain_B,CRISPR2,spacer_1,CGCCAGGGTTTTCCCAGTCACGACGTTGTAAA,
```

[CRISPRPrep](crispr-processing.md) CSV is accepted: `Folder` becomes strain ID, `Seq_Name` becomes array ID and `Spacer_Sequence` supplies the sequence. The search uses original sequences and searches both DNA strands. It does not require replacing negative-array spacers with their reverse complements.

### CRISPRCasFinder JSON

Each uploaded JSON is treated as one sample. All spacer regions are included, including low/missing evidence and unknown orientation. This differs from direct JSON import in SpacerMap. Use CRISPRPrep first when you need controlled evidence/count filtering, and use CSV to specify sample grouping explicitly.

Records without strain information remain searchable and appear in the table as unassigned, but are excluded from the strain network. Identical sequences share a search while retaining all occurrences. Similar sequences and reverse complements are not collapsed into the same query.

## Start a search

1. Open **SpacerTarget** and choose **Paste spacers** or **Upload files**. **Load example** provides trial input.
2. Check the input and select **Maximum targets per spacer**: 20, **50 · standard** (default), or 100.
3. Select **Search** and wait for a Job ID.
4. Select **Copy results link** and save it. Paste the link or full ID under **Retrieve previous results** or in [My Jobs](../my-jobs.md) to return. The current page does not offer email notification or a cache-selection control.

Spacers are sent to NCBI for the search. New submissions create new searches; there is no user-selectable cached-result reuse in the current workflow. Saved task results expire seven days after the task ends. Archive downloads and original sequences for long-term use. A higher target limit may return more candidates, but no setting guarantees an exhaustive search of phages, plasmids or all database matches.

## Follow progress and recover

Progress follows **Queued → Search → Annotate → Complete**, with **Waiting for resources** when local capacity is temporarily unavailable. A task is split into batches of at most 10 distinct spacers and 1,000 bases. Local queue waiting and remote NCBI searching are different stages. Searching finds sequence matches; annotation retrieves target metadata. Read **Details** for batch warnings and unresolved steps. The activity display is not an estimated time remaining. Once a batch has a remote request ID, its **NCBI request** link opens the NCBI request page; this remote ID is different from the CRISPREvo Job ID. Slow remote searching does not by itself mean no hits.

Closing the tab does not cancel a submitted task. Use the saved results link or ID to return. **Refresh** checks the current state; it does not start another search. **Cancel** stops future work while retaining successful results; a request already sent to NCBI may still run. After a task ends, the input is deleted and in-place retry is unavailable. Keep any successful results, then resubmit the needed original spacer sequences as a new task.

A partial, failed or cancelled task can still contain successful matches. Do not interpret those as a completed search of all input spacers. Anyone with the results link can access the task, so share it only with intended collaborators. The results link and local history do not extend the seven-day retention period.

## Choose match-quality filters

Results initially use **Strict**. Changing filters reuses retrieved evidence and does not launch another BLAST search.

| Preset | Keeps |
| --- | --- |
| **Strict** | Full spacer coverage, at most 2 mismatches, no gaps. |
| **Exact** | Full coverage, no mismatches and no gaps. |
| **Explore** | Custom minimum identity (default 90%), minimum coverage (90%) and maximum mismatches (100). Gapped alignments may pass. |

Filter further by **Target type**, **Strain** and search text. **Sort** changes ordering only. **Exclude source-accession matches** removes hits matching the supplied `source_accession`, ignoring version suffixes. Without a source accession, a row cannot be identified as a source-record match. This control does not detect every self-target or automatically exclude all CRISPR array regions.

Use **Reset filters** to clear table filters and any node/edge focus. Network **Show** checkboxes only hide target categories in the graph; they do not apply the table's **Target type** filter or restrict TSV exports.

## Read the match table

**One row is one spacer occurrence matched to one target site.** Repeated sequences in different strains, different sites on one accession, and different accessions can produce multiple rows. Row count is not a count of distinct spacer sequences or unique targets.

| Field | Interpretation |
| --- | --- |
| **Strain / spacer** | Original sample/array/spacer assignment. |
| **Target sequence** | Matched accession and description. Open its source record to check context. |
| **Type** | Virus/phage, plasmid, chromosome or unknown, inferred from available source annotations and taxonomy. Unknown can reflect missing or incomplete annotation. |
| **Identity** | Identical nucleotide columns divided by alignment length, including gap columns in the denominator. |
| **Coverage** | Aligned non-gap query bases as a percentage of the spacer's full length. 100% identity with partial coverage is not a full-length exact match. |
| **Mismatch / gap** | Different nucleotide positions and gap-containing alignment columns, reported separately. |
| **Target coordinates** | 1-based inclusive start/end on the target record; strand is relative to the submitted query. A minus-strand hit is not an error. |
| **E-value** | BLAST's expectation statistic. Use it alongside coverage, mismatches and alignment context; it is not a probability that the target is biologically valid. |
| **Alignment** | Opens aligned query and target sequences, match markers and an NCBI link for close inspection. |

For example, a 32-base spacer aligned over all 32 bases with one mismatch and no gaps has 100% coverage and 96.875% identity. It passes Strict but not Exact. Separate local alignments are not stitched together to claim full-length coverage.

A chromosome hit can include the source array or other genomic similarity. A virus/phage label does not establish host range. No displayed rows can mean no returned candidates, all candidates filtered out, or unfinished/failed queries. First check task completion, then **Explore**, **Reset filters** and **All candidates TSV** before interpreting absence.

## Read the strain–target network

Nodes represent strains and target accessions. Each edge joins one strain to one target and is weighted by the number of **different supporting spacer sequences** under the current table filters. Repeated copies or multiple alignments of one sequence do not increase that weight. A thicker line means more distinct spacer support, not stronger experimentally measured interaction.

Click a node to focus the table on that strain or target; click an edge to focus both. Drag nodes to arrange, drag background to pan, use zoom or **Fit network**, and choose **Clustered**, **Force directed** or **Circle** layout. Layout proximity and connected groups are visual summaries, not phylogenetic distances.

The graph displays up to **200 nodes** and reports hidden nodes/edges. Table and TSV exports retain all filtered matches. Unassigned spacers are omitted from the graph. An empty or smaller graph does not mean the table lost those results.

## Download and preserve evidence

| Download | What it contains |
| --- | --- |
| **Export filtered TSV** | All rows passing current table filters and node/edge focus, across every table page. |
| **All candidates TSV** | All returned candidate alignments, without Strict or current table filtering. This is not every possible match in the database. |
| **Search report** | JSON containing occurrence/query IDs, retained batch match evidence, annotation status, timestamps and errors. Terminal reports omit full original query sequences, search parameters and raw BLAST responses. |
| Network **PNG / SVG** | Current network view/layout. Preserve TSV/report as the underlying evidence. |

Keep original input sequences and separately record database, hit limit and filter thresholds. Save the search report, all-candidate TSV and a filtered TSV for the evidence used in your analysis. In terminal results, the exported `sequence` field can be blank because full input sequences were deleted; occurrence/query identifiers and alignment strings remain. Do not reconstruct a complete spacer from a partial alignment. Search text for a full original sequence may no longer work after completion; use strain/spacer IDs or target accessions instead.

## Limits and troubleshooting

A task accepts up to **100 distinct spacers**, **10,000 occurrences**, **100 files**, and **2 MB combined input**. Each spacer is 15–100 A/C/G/T bases. Both strands are searched. A submitted search batch exceeding 24 hours is marked failed; inspect task details and resubmit the needed original input in a new task.

| Symptom | Action |
| --- | --- |
| Search disabled | Check service status with **Refresh status**; the search service must be available. |
| Invalid spacer | Supply original sequence, check length, and resolve N or other ambiguous symbols. |
| No strain network | Add `strain_id` or three-part FASTA headers; confirm that retained matches exist. |
| Unknown target type | Inspect annotation status; unknown is not proof that the target is nonviral/nonplasmid. |
| Strict gives no rows | Check completion, inspect all candidates, then use Explore to examine partial/lower-quality matches. |
| Target limit reached | Treat candidate lists as truncated; consider a fresh search with a higher supported limit. |
