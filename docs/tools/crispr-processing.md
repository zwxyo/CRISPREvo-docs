# CRISPRPrep

Use **CRISPRPrep** (`/crispr-processing`) to detect CRISPR arrays in genome sequences or prepare existing CRISPRCasFinder results. It produces a filtered spacer CSV for [SpacerMap](spacermap.md), [CRISPRPhylo](crisprphylo.md) and [SpacerTarget](spacer-target.md), plus a report explaining what was retained or excluded.

## Choose an entry point

### Upload genome FASTA

1. Select **Detect from sequence → Upload FASTA**.
2. Add `.fa`, `.fasta` or `.fna` files: up to **100 files and 100 MB combined**. Each file represents one sample and may contain multiple contigs.
3. Use ordinary FASTA headers such as `>contig_1 organism description`, with unique sequence IDs within each file and IUPAC DNA sequence on following lines.
4. Use **Add files** or drag more files into the upload area to append files from other folders. Check the accumulated list; an invalid addition leaves the previous selection intact. **Reset** clears it.
5. Check detector availability with **Refresh status**, configure processing, then select **Run**.

The sample and CSV `Folder` are the basename **without** the final `.fa`, `.fasta` or `.fna` extension: `sample_A.fna` becomes `sample_A`. All contigs in that file share the sample. Duplicate sample names receive numeric suffixes. Give different strains different filenames; several files can be detected in one run. Current upload help text may still say that the extension is included; the parser removes it.

Sequence IDs that collide across samples are temporarily distinguished for detection and restored in the downloadable detector JSON. CSV `Seq_Name` comes from the detected CRISPR array `Name`, with suffixes added to distinguish duplicate array names; it is not the FASTA record ID alone.

Detection requires a configured Linux detector. If it is unavailable, import existing CRISPRCasFinder JSON. Detection can run for up to two hours, separate from queue waiting; the service may stop a run earlier. Once a Job ID appears, detection and processing continue after closing the page. Save **Copy results link** and return through [My Jobs](../my-jobs.md). Results expire seven days after the task ends.

The bundled detector enables Cas-gene analysis by default when configured with `CCF_ENABLE_CAS=1`. Available Cas annotations remain in **CRISPRCasFinder JSON**; the spacer CSV and array table are not Cas-system reports. Administrators can disable Cas analysis. Intermediate Cas files and run logs are not user downloads.

### Paste sequence with explicit sample names

Select **Detect from sequence → Paste sequence**. Every header must have three fields:

```text
>Sample Seq_Name Organism_Name
```

The first field supplies Sample / `Folder`, the second is the original sequence ID, and the remaining text is the organism description, which can contain spaces. Repeat the same Sample for contigs belonging to one strain; the `(Sample, Seq_Name)` pair must be unique. Sample and sequence ID cannot contain spaces. For example:

```fasta
>sample_A contig_1 Example organism strain A
ATGCGTACGTTAGCTAGCTAGCTAGCTAGC
>sample_A contig_2 Example organism strain A
GGCATCGATCGATCGATCGATCGATCGTTA
>sample_B contig_1 Example organism strain B
ATGCGTACGTTAGCTAGCTAGCTAGCTAGC
```

These short records demonstrate syntax only. Pasted FASTA is limited to 100 MB. Ordinary uploaded-file headers do not automatically satisfy the paste contract; add the explicit Sample field before pasting. Both modes preserve sample identity through CSV processing.

### Fetch from NCBI and detect automatically

1. Select **Detect from sequence → NCBI accession**.
2. Paste up to 100 unique nucleotide accessions, nucleotide sequence UIDs, or assembly accessions (`GCA_`/`GCF_`). Separate IDs with newlines, spaces, commas or semicolons; the text limit is 16,000 characters.
3. Set processing filters, select **Run**, and save the private results link.
4. Follow retrieval, detection and processing progress. Successful records proceed even when some IDs fail.
5. Review **Accession IDs needing attention** and the retrieval summary before interpreting the arrays.

For example, `GCF_000339015.1` retrieves an assembly with its component contigs, whereas `NC_000913.3` requests one nucleotide record. Each retrieved sequence is assigned to the **requested accession ID** in Sample / `Folder`; all contigs of an assembly share that requested ID. Aliases resolving to a previously retrieved accession use the first successful input ID and do not create another copy. This is accession deduplication, not sequence-similarity clustering.

Successful sequences are combined into one detector FASTA, within a **100 MB** total limit, while retaining their sample mapping for the processed CSV. Several requested contig IDs from the same biological strain still become separate sample IDs: use a single assembly accession or one uploaded FASTA per strain when that grouping is required. If the task is partial, inspect failures and retry needed IDs in a new submission.

### Import JSON results

1. Select **Import JSON results**.
2. Upload up to 100 CRISPRCasFinder JSON files, within 100 MB combined, or use **Choose a results folder** to select JSON files recursively.
3. Add further files through **Add files** or drag-and-drop if needed.
4. Configure filters and select **Run**.

The sample is the **JSON basename without `.json`**, even for a folder upload. `sample_A/result.json` becomes `result`, not `sample_A`; repeated basenames become `result_2`, `result_3`, and so on. Rename files to descriptive sample names such as `sample_A.json` before import. The currently displayed hint about keeping the extension is outdated.

While source JSON is loaded locally, **Original JSON → Download JSON** downloads the selected file before processing. Uploaded source JSON is not retained in saved results.

A generated multi-sample detection JSON does not embed the separate sample mapping used for that job's CSV processing. Re-importing it treats the whole file as one sample. Keep the initial processed CSV and sample manifest; to re-filter a multi-sample detection, rerun the original FASTA/accession input with the new settings, or prepare separate correctly named JSON files per sample.

### Try the supplied examples

Select **Load example**, then choose a format. Loading fills the corresponding input and resets processing options to their defaults; select **Run** to submit.

| Example option | Loaded data | Requires detection? |
| --- | --- | --- |
| **Upload FASTA** | Five genome files: 702, CFSAN134858, CFSAN134971, CS-54 and ES191; about 23 MB in total. | Yes |
| **Paste sequence** | The same genomes combined into 416 records with explicit Sample / sequence / organism headers. | Yes |
| **NCBI accession** | Five assembly IDs. | Yes, plus NCBI retrieval |
| **Import JSON results** | Five precomputed CRISPRCasFinder JSON files. | No |

With the bundled JSON examples and default filters, expect 95 inspected arrays, 10 retained arrays and 119 processed spacer rows. These are precomputed-example counts, not guaranteed counts from a new detection run.

The example replaces the current selection only after loading successfully. If a download fails, retry the desired format; the previous input remains available.

## Set processing options

| Setting | Default | Effect and when to change it |
| --- | --- | --- |
| **Minimum evidence level** | All evidence levels (1) | Inclusive threshold: 3 keeps levels 3 and 4. Use a higher threshold to restrict the result to higher-evidence detector calls. At level 1, missing evidence values are also retained; thresholds 2–4 exclude them. |
| **Minimum spacers per array** | 2 | Keeps arrays with at least this many `Spacer` regions. Increase to exclude shorter arrays; decrease to 1 if single-spacer arrays are relevant. |
| **Remove unknown orientation (ND)** | Checked | Excludes unresolved direction after trying `CRISPRDirection` as a fallback. Unchecking retains unknown-direction arrays without inferring an orientation. |
| **Exclude organisms with N-containing spacers** | Checked | Removes all extracted rows with the same organism name if any extracted spacer in that group contains `N`. It can affect multiple files in the batch. |

Evidence levels are detector categories, not probabilities or a percentage confidence. These options filter detector results; they do not change the detector's search parameters.

The N filter is **organism-wide**, not limited to the single spacer, array or sample. Identical organism descriptions across files are grouped together; empty descriptions are also grouped together. If many otherwise usable arrays disappear, examine this filter and the report before concluding that those samples lack arrays. Other ambiguous DNA letters can still remain; SpacerTarget requires A/C/G/T only.

## Read the results

Start with the summary: input files and detected arrays describe what was inspected; retained arrays and processed spacer rows describe what remains after filtering. Extracted spacer rows have passed evidence and minimum-count filters; processed rows have additionally passed the chosen direction/N rules.

The array table has one row per detected array:

| Column | How to read it |
| --- | --- |
| **Sample / array** | Sample grouping and array name; use these to trace the record to your input. Duplicate array names are made unique across the batch. |
| **Organism** | Name taken from the sequence description, not a new taxonomic identification. |
| **Evidence** | CRISPRCasFinder evidence category, or missing if not provided. |
| **Direction** | Resolved `+`/`-`, or unresolved `ND`. |
| **Spacers** | Number of spacer regions reported for that array before final filtering. |
| **Processing result** | Retained or the applicable reason for exclusion. |

Select **Filtered out** to investigate missing arrays. Typical reasons are **Below evidence threshold**, **Unknown evidence**, **Too few spacers**, **Unknown orientation**, and **Organism contains N**. An array may violate several rules; the displayed reason is not necessarily every possible reason. Zero retained arrays can be a valid filtering outcome, not a failed run.

Expand **Preview processed spacer rows** to inspect the first 200 rows. Original sequence and reverse complement are separate fields; blank reverse-complement cells for positive arrays are expected. **Effective repeat** is the orientation-appropriate repeat for that row. For negative arrays, processing reverses spacer row order within the array and adds reverse-complement sequence/repeat fields. It does not overwrite the original spacer sequence.

Search, status filters and pagination affect the preview only. Downloads include **all rows from the applied settings**. To change filtering, reselect your original JSON files or rerun the original detection input with new options and select **Run**. A single-sample detector JSON can also be re-imported under a descriptive filename. Re-importing a combined multi-sample JSON loses its original sample grouping. Saved results omit the original uploaded files and processing options. The current form values must not be assumed to describe a restored result; record your chosen settings separately.

## Choose downloads and next steps

| Download | Contents | Next use |
| --- | --- | --- |
| **CRISPRCasFinder JSON** | Generated `result.json`, available for detection jobs including accession-based detection. | Preserves detector annotations and restored sequence names. Re-import only with the sample-grouping caveat above. Keep uploaded JSON separately; it is not retained as a saved job output. |
| **Unprocessed CSV** | `Folder`, `Organism_Name`, `Seq_Name`, `Orientation`, `Spacer_Sequence`, `DR_Consensus`. | Inspect rows after evidence/count filtering. |
| **Processed CSV** | Extracted fields plus `Reverse_Complement_Sequence`, `Reverse_Complement_DR`, `Effective_DR`; applies direction/N rules. | Input for SpacerMap and CRISPRPhylo; original spacer sequences also support SpacerTarget. |
| **Download processing report** | Counts, warnings, array outcomes and retrieval summary where applicable. Saved reports omit processing options. | Explain exclusions; retain a separate settings record for reproducibility. |

Select the **CRISPRPhylo**, **SpacerMap** or **SpacerTarget** link below the downloads to transfer the processed CSV into that tool in the same tab. Review the imported input, then submit it there; transfer does not start another analysis. If browser storage prevents transfer, download **Processed CSV** and upload it manually.

For tree building, confirm that `Folder` represents the intended strains. For array plotting, preserve unique `Seq_Name` values. See [Input formats](../reference/input-formats.md) for their different roles.

## Limits and common problems

Up to 100 MB total input, 100 uploaded FASTA or imported JSON files, and 100,000 parsed arrays or extracted spacer rows are accepted. Empty results still download as CSV with headers.

| Symptom | Action |
| --- | --- |
| JSON rejected | Supply a CRISPRCasFinder result containing `Sequences`, `Crisprs` and `Regions`, not a saved figure project or processing report. |
| Many arrays unexpectedly excluded | Review evidence, direction, count and organism-wide N filtering. |
| Only one strain appears downstream | Check input grouping: one sample per uploaded FASTA, explicit Sample fields for pasted FASTA, requested IDs for NCBI, or basenames for JSON. Re-imported combined JSON becomes one sample. |
| Refreshed job cannot be recovered | Retrieve the full ID/private link in My Jobs. After seven-day expiry, rerun with your saved input. |
