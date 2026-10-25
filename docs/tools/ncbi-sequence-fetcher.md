# SeqFetcher

Use **SeqFetcher** (`/ncbi-fetcher`) to download nucleotide or protein records from NCBI by sequence accession, assembly accession or sequence UID. FASTA and metadata let you verify what was retrieved before continuing.

## Prepare identifiers

Paste IDs or upload UTF-8 `.txt`, `.csv` or `.tsv` containing **IDs only, without a header**. Separate IDs with newlines, commas, spaces or semicolons. Duplicate input IDs are removed.

```text
NC_012920.1
NM_000546.6
NM_001126112.3
```

These are nucleotide retrieval examples, not a bacterial CRISPR dataset. Use sequence records corresponding to your study samples. A version suffix such as `.1` identifies the requested record version.

Select **Nucleotide** for DNA/RNA or **Protein** for amino-acid records such as `NP_000537.3`. Numeric UIDs must belong to that database. For whole-genome assemblies, select **Nucleotide** and supply a `GCA_` or `GCF_` accession, for example `GCF_000339015.1`. Assembly IDs are not accepted in Protein mode. Gene, Taxonomy, BioProject and SRA IDs are not supported inputs.

## Retrieve sequences

1. Select the paste or upload input tab and enter IDs.
2. Check the unique-ID count and any validation message.
3. Select the database; keep nucleotide and protein requests in separate runs.
4. Choose **5 · standard** under **IDs per request**, or **1 · large sequences** for large genomes. If the list contains an assembly accession, requests automatically use batch size 1.
5. Select **Commit** and follow result statuses.
6. Check organism, description and length against your sample list.

**Stop after this batch** requests cancellation; the active batch may finish and later IDs remain pending. **Resume pending** submits pending IDs as a new job. **Retry failed** submits all failed IDs as a new job, independently of row selection. The displayed result is replaced by the new job result; earlier successes remain accessible through the previous private link. Save that link before retrying or starting another run.

## Read results

Use the row statuses to follow retrieval. The downloaded run report records requested, succeeded, failed and pending counts; selected records can resolve to fewer distinct accessions.

| Item | Meaning |
| --- | --- |
| Requested | Unique input IDs in this run. |
| Retrieved | Input IDs resolved successfully. |
| Failed | IDs that could not be retrieved; inspect the error. |
| Pending | IDs not completed, for example after stopping or a service interruption. |
| Unique sequences | Distinct retrieved accessions. Different IDs can resolve to one accession, so this can be lower than the retrieved-record count. This is not similarity clustering. |
| Length | Nucleotide or amino-acid residues, depending on the database. |

Search by ID, accession, organism or description, and use the status filter to locate unresolved records. A preview shows the FASTA header and first **2,000 characters**; downloads contain the full sequence. For sequence-accession rows, **View on NCBI** opens the source record. For an assembly, use its GCA_/GCF_ accession to locate the corresponding NCBI assembly record.

Successful retrieval does not establish that the record is the intended strain, a complete genome or suitable CRISPR input. Verify metadata before analysis.

## Read an assembly result

An assembly appears as one accession row, but its FASTA can contain many chromosome/contig records. The reported length is the sum of their lengths. The downloader preserves separate FASTA headers and does not join contigs into one artificial sequence. Thus a count of one exported assembly is not necessarily one FASTA record.

The preview still shows only the first 2,000 characters; use the full FASTA download to inspect every contig. Check the assembly accession/version and metadata. An assembly package must match the requested version and its reported total length; incomplete or inconsistent downloads are reported as failures.

## Select and download

Retrieved rows are selected automatically. Row checkboxes change the export selection. The header checkbox affects **visible retrieved rows** only; previously selected hidden rows can remain selected. Check the selected-record count before export. Searching alone does not limit downloads to visible rows.

| Download | Contents / use |
| --- | --- |
| **FASTA** | Full sequences for selected records, deduplicated by accession, in one file. |
| **Metadata TSV** | Selected input IDs, accessions, organism, description, length and source metadata. Reconcile it with your sample list. |
| **Download ZIP** | Combined and individual FASTA, metadata TSV/JSON, run report, and failed/pending ID lists. |
| **Download failed IDs** | Unresolved IDs to check or retry later. |
| Preview **Download** | Complete FASTA for that accession, including all component contigs for an assembly. |

Copy the private results link after submission. Retrieval continues on the server, and results can be restored through [My Jobs](../my-jobs.md) for seven days after the task ends. Download FASTA/ZIP for longer storage. For several bacterial strains, preserve separate sample identities when continuing to [CRISPRPrep](crispr-processing.md); a combined FASTA is not automatically separated into strains there. Protein FASTA cannot be used for genome CRISPR detection.

## Limits and recovery

A job accepts up to 500 unique IDs and 16,000 characters of submitted ID text; ID files are limited to 1 MB. Each request contains at most 5 IDs, or 1 when assemblies are included, and returns at most 8 million residues. The saved FASTA-text budget is 25 million characters per job, including headers and line breaks. Batch size 1 does not remove the per-response limit. Assembly packages also have a 64 MiB download/unpacked-size limit.

| Symptom | Action |
| --- | --- |
| Record not found | Check accession spelling/version and database. |
| WGS master has no sequence | Retrieve component records or assembly FASTA. |
| Batch exceeds residue limit | Set batch size to 1 and retry; a single record over the limit needs another retrieval workflow. |
| Temporary NCBI failure | Save the previous result link and successful outputs; retry failed/pending IDs as a new job after recovery. |
| Download disabled | Wait for retrieval to stop and select a retrieved record. |
