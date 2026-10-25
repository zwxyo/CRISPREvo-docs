# CRISPRPrep

Use **CRISPRPrep** (`/crispr-processing`) to detect CRISPR arrays in genome sequences or prepare existing CRISPRCasFinder results. It produces a filtered spacer CSV for [SpacerMap](spacermap.md), [CRISPRPhylo](crisprphylo.md) and [SpacerTarget](spacer-target.md), plus a report explaining what was retained or excluded.

## Choose an entry point

### Detect from sequence

1. Select **Detect from sequence**.
2. Choose **Upload FASTA** and provide one `.fa`, `.fasta` or `.fna`, or choose **Paste sequence** and paste FASTA text.
3. Include a unique `>` header for every contig and use IUPAC DNA letters. Do not paste a protein sequence or raw DNA without headers.
4. Check detection-service availability. **Refresh status** checks it again.
5. Configure processing below, then select **Run**.

One file can contain multiple contigs. Keep one biological sample per detection run: the tool does not automatically turn every FASTA record into a separate strain. For multiple strains, detect each separately, save each JSON, then import them together with distinct filenames or sample folders.

Detection requires an available server detector. If it is unavailable, use CRISPRCasFinder results generated elsewhere and follow the JSON route. Detection can run for up to 2 hours, in addition to time spent waiting for resources. Once a Job ID appears, detection and processing continue after closing the page. Save **Copy results link** and return through [My Jobs](../my-jobs.md) or **Retrieve previous results**. Results expire seven days after the task ends.

### Fetch from NCBI and detect automatically

1. Select **Detect from sequence → NCBI accession**.
2. Paste up to 100 unique nucleotide accessions, nucleotide sequence UIDs, or assembly accessions (`GCA_`/`GCF_`). Separate IDs with newlines, spaces, commas or semicolons.
3. Set the processing filters, select **Run**, and save the private results link.
4. Follow retrieval, detection and processing progress. Successful records proceed even when some IDs fail.
5. Review **Accession IDs needing attention** and the retrieval summary before interpreting the arrays.

For example, `GCF_000339015.1` retrieves an assembly with its component contigs, whereas `NC_000913.3` requests one nucleotide record. Assembly contig boundaries and accessions are preserved. Repeated component accessions are included once; this is accession deduplication, not sequence-similarity clustering. A single assembly can therefore produce more than one unique sequence in the summary.

Successful sequences are combined into one detector FASTA, within a 20 MB limit. This automated route produces one detection result and does **not** automatically create one strain per requested accession. For multi-strain inference, submit each sample separately, download its generated JSON, name it by sample, and import those JSONs together. If the task is partially finished, retry failed IDs in a separate submission; do not interpret missing retrievals as genomes without CRISPR arrays.

### Import JSON results

1. Select **Import JSON results**.
2. Upload one or more CRISPRCasFinder JSON files, or select **Choose a results folder** containing sample subfolders and `result.json` files.
3. To try the workflow without a detector, select **Load example JSON**.
4. Configure the filters and select **Run**.

Each JSON file supplies a sample identity. When importing `sample_A/result.json`, the parent folder supplies `Folder`; for a standalone `sample_A.json`, the filename supplies it. Duplicate identities receive suffixes, but descriptive sample names are easier to audit. The same rule applies to generated JSON re-imported for a multi-strain analysis: use names that identify the source sample.

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

Search, status filters and pagination affect the preview only. Downloads include **all rows from the applied settings**. To change filtering, reselect your original JSON files, or download **CRISPRCasFinder JSON** from a detection result and import it again, set the options, and select **Run**. Saved results omit the original uploaded files and processing options. The current form values must not be assumed to describe a restored result; record your chosen settings separately.

## Choose downloads and next steps

| Download | Contents | Next use |
| --- | --- | --- |
| **CRISPRCasFinder JSON** | Generated `result.json`, available for detection jobs including accession-based detection. | Download it for reprocessing or multi-sample import. Keep uploaded JSON separately; it is not retained as a saved job output. |
| **Unprocessed CSV** | `Folder`, `Organism_Name`, `Seq_Name`, `Orientation`, `Spacer_Sequence`, `DR_Consensus`. | Inspect rows after evidence/count filtering. |
| **Processed CSV** | Extracted fields plus `Reverse_Complement_Sequence`, `Reverse_Complement_DR`, `Effective_DR`; applies direction/N rules. | Input for SpacerMap and CRISPRPhylo; original spacer sequences also support SpacerTarget. |
| **Download processing report** | Counts, warnings, array outcomes and retrieval summary where applicable. Saved reports omit processing options. | Explain exclusions; retain a separate settings record for reproducibility. |

Select the **CRISPRPhylo**, **SpacerMap** or **SpacerTarget** link below the downloads to transfer the processed CSV into that tool in the same tab. Review the imported input, then submit it there; transfer does not start another analysis. If browser storage prevents transfer, download **Processed CSV** and upload it manually.

For tree building, confirm that `Folder` represents the intended strains. For array plotting, preserve unique `Seq_Name` values. See [Input formats](../reference/input-formats.md) for their different roles.

## Limits and common problems

Up to 20 MB total input, 100 imported JSON files, and 100,000 parsed arrays or extracted spacer rows are accepted. Empty results still download as CSV with headers.

| Symptom | Action |
| --- | --- |
| JSON rejected | Supply a CRISPRCasFinder result containing `Sequences`, `Crisprs` and `Regions`, not a saved figure project or processing report. |
| Many arrays unexpectedly excluded | Review evidence, direction, count and organism-wide N filtering. |
| Only one strain appears downstream | Check that multiple genomes were not combined into a single sample; inspect `Folder` values. |
| Refreshed job cannot be recovered | Retrieve the full ID/private link in My Jobs. After seven-day expiry, rerun with your saved input. |
