# Input formats

Use this page to choose the correct input and preserve sample identities. Save text files as UTF-8. Examples are illustrative, not biological validation datasets.

## Which file goes where?

| File/data | Tool |
| --- | --- |
| NCBI sequence accessions or UIDs | SeqFetcher; nucleotide IDs also work in CRISPRPrep’s NCBI accession mode |
| GCA_/GCF_ assembly accession | SeqFetcher in Nucleotide mode, or CRISPRPrep’s NCBI accession mode |
| Genome/contig FASTA | CRISPRPrep detection: one sample per uploaded file, or explicit sample fields in pasted headers |
| CRISPRCasFinder JSON | CRISPRPrep; SpacerMap and SpacerTarget accept it with different filtering rules |
| Processed spacer CSV | SpacerMap, CRISPRPhylo, SpacerTarget |
| Spacer FASTA | SpacerMap or SpacerTarget, with different grouping conventions below |
| Numbered arrays (one array per line) | SpacerMap and ArrayPhyloViz; not SpacerTarget or CRISPRPhylo |
| SpacerMap `Spacer_ID` / `Spacer_Sequence` mapping | Output lookup table; does not contain array membership and is not array input |
| Newick tree plus arrays | ArrayPhyloViz |
| Saved ArrayPhyloViz project JSON | ArrayPhyloViz **Open saved project** |

## Genome FASTA

CRISPRPrep accepts up to 100 uploaded `.fa`, `.fasta` or `.fna` files, 100 MB combined. Each file is one sample; its basename without the extension becomes `Folder`. Multiple contigs in that file share the sample. Uploaded FASTA uses ordinary headers:

```fasta
>contig_1 Example organism strain A
ATGCGTACGTTAGCTAGCTAGCTAGCTAGC
>contig_2 Example organism strain A
GGCATCGATCGATCGATCGATCGATCGTTA
```

Each file needs unique, nonempty sequence IDs. IDs may contain letters, digits, underscores, dots, colons, pipes, `+` and `-`. Sequences must use IUPAC DNA letters.

**Pasted FASTA has a different header contract:** `>Sample Seq_Name Organism_Name`. The first two fields cannot contain spaces; all remaining header text is the organism description. Repeat Sample for contigs belonging to one strain and keep Seq_Name unique within that sample:

```fasta
>sample_A contig_1 Example organism strain A
ATGCGTACGTTAGCTAGCTAGCTAGCTAGC
>sample_B contig_1 Example organism strain B
GGCATCGATCGATCGATCGATCGATCGTTA
```

These short sequences demonstrate syntax only. Pasted input is limited to 100 MB. FASTA sequence IDs are restored after any detector-only renaming; CSV `Seq_Name` instead follows the detector's CRISPR array `Name`. Duplicate sample/array names receive suffixes.

## NCBI ID list

Use one or more nucleotide IDs such as `NC_012920.1` or `NM_000546.6`, or protein IDs such as `NP_000537.3` in a separate Protein run. Newlines, commas, spaces and semicolons separate IDs. Uploaded TXT/CSV/TSV must contain IDs only, with no column header. Sequence UIDs are accepted. GCA_/GCF_ assembly accessions are accepted in **Nucleotide** mode, for example `GCF_000339015.1`. They identify an assembly whose downloaded FASTA preserves separate chromosome/contig records. Gene, Taxonomy, BioProject and SRA IDs are not supported. CRISPRPrep accepts up to 100 nucleotide/assembly IDs and combines successful records into one detection input while preserving the requested ID as Sample / `Folder`; assembly contigs share that ID and aliases use the first successfully retrieved ID; SeqFetcher accepts up to 500 IDs. Both submitted ID lists are limited to 16,000 characters.

## CRISPRCasFinder JSON

Provide the detector's result object containing `Sequences`, each sequence's `Crisprs`, and each array's `Regions`. Keep the original JSON for reprocessing. In CRISPRPrep, sample names come from JSON basenames without `.json`, not parent directories: `sample_A/result.json` becomes `result`. Rename it to `sample_A.json` to preserve that sample name. A combined multi-sample detector JSON is treated as one sample on re-import; retain the original processed CSV and input sample manifest.

| Import destination | What happens |
| --- | --- |
| **CRISPRPrep** | Adjustable evidence/count filters plus optional unknown-direction and organism-wide N exclusion. Default minimum evidence is 1 and minimum spacer count is 2. |
| **SpacerMap** | Fixed evidence 3–4 filtering, unresolved `ND` removal and organism-wide N exclusion. |
| **SpacerTarget** | Retains all spacer regions regardless of evidence/direction; each JSON file is one sample. Every sequence must still pass its 15–100 A/C/G/T validation. |

Use CRISPRPrep if filtering needs to be explicit and documented. A processing report, target-search report or figure-project JSON is not interchangeable with detector JSON. CRISPRPrep’s **CRISPRCasFinder JSON** download supplies generated detector output; keep any JSON you upload separately, because saved jobs do not retain those uploads.

## Processed spacer CSV

Each row is one spacer occurrence. CRISPRPhylo requires this complete header; CRISPRPrep output includes additional useful columns:

```csv
Folder,Seq_Name,Orientation,Spacer_Sequence,DR_Consensus,Reverse_Complement_Sequence,Reverse_Complement_DR
strain_A,strain_A_CRISPR1,+,ACGTACGT,TGCA,,
strain_A,strain_A_CRISPR1,+,GATTACAA,TGCA,,
strain_B,strain_B_CRISPR1,-,ACTTAGCA,GCAT,TGCTAAGT,ATGC
strain_B,strain_B_CRISPR1,-,GCCATGGA,GCAT,TCCATGGC,ATGC
```

This is a format illustration; its short spacers are not suitable SpacerTarget input. Use actual processed data for analysis. Keep rows from each array in their intended order, and retain reverse-complement columns supplied by CRISPRPrep.

| Field | Identity rule |
| --- | --- |
| `Folder` | Strain ID for CRISPRPhylo and SpacerTarget. Do not merge different strains under the same value. |
| `Seq_Name` | Array ID. For CSV, SpacerMap combines nonempty `Folder` and `Seq_Name` as `Folder_Seq_Name`; without Folder it uses Seq_Name alone. Keep the resulting labels unique. |
| `Organism_Name` | Description-derived label, used by CRISPRPrep's organism-wide N filter; not a substitute for strain ID. |

SpacerMap accepts pasted CSV as well as uploaded CSV when its header includes `Spacer_Sequence`. Its minimum custom CSV header is `Seq_Name,Orientation,Spacer_Sequence`. It skips `Orientation=ND`, rejects conflicting orientations within one combined array label, and can compute a missing negative-strand reverse complement. It preserves CSV row order for both strands, so supply the already normalized CRISPRPrep Processed CSV rather than reversing negative arrays again. CRISPRPhylo requires all seven columns and a usable reverse-complement spacer value for reverse rows. `Effective_DR` in CRISPRPrep output is the orientation-appropriate repeat.

## Numbered arrays

Use one array per line. A tab separates the unique name from its spacer IDs; commas, spaces or semicolons separate IDs.

```text
strain_A	1,2,3,6,7,9
strain_B	1,2,4,6,8,9
strain_C	10,2,4,6,8
```

[Download this example](../examples/example-arrays.txt). IDs identify types within the dataset, not globally across runs. Generate IDs for all arrays together in SpacerMap and preserve the array-map SVG or normalized array text. Its **Download numbered file** output is the sequence lookup below, not array-per-line TXT. DNA arrays in uploaded TXT/TSV can be converted automatically by SpacerMap; pasted array-format text treats tokens as labels; a recognized spacer CSV is converted through the CSV route.

## Spacer ID–sequence mapping

SpacerMap's **Download numbered file** writes a tab-separated `.txt` with this header:

```text
Spacer_ID	Spacer_Sequence
1	ACGTACGT
```

The header is literal and case-sensitive. Each distinct `(ID, sequence)` pair is retained; similarity-grouped variants can share an ID and occupy separate rows. Negative-strand CSV spacers use the reverse-complement sequence used for numbering. This table has no array names, memberships or order. **Do not upload it as numbered-array input** to SpacerMap or ArrayPhyloViz; use the array-map SVG for the latter, or preserve the original array-per-line text. Number-only input has no sequence mapping. Rerun older saved results from original sequence input if the mapping is needed.

## Spacer occurrence outputs

**Download statistics CSV** in SpacerMap produces a result table with this header:

```csv
Spacer ID,Number of arrays
"2",3
"6",3
```

Each row counts the arrays containing one ID, counting repeated copies within an array once. This is an output summary, not a numbered-array input or a sequence table for any downstream tool. Use the [complete example CSV](../examples/SpacerMap-occurrence.csv) to inspect the format. Preserve the numbered arrays to retain array membership and order.

The occurrence SVG contains a bar chart and presence/absence matrix. It does not contain editable array-map metadata. For ArrayPhyloViz, use the array-map SVG or numbered arrays instead of occurrence or cluster-network figures.

## Spacer FASTA and direction folders

For **SpacerMap**, one uploaded FASTA represents one array, each record one spacer. Numeric record headers are used directly as IDs; other headers trigger sequence-based numbering. For multiple arrays, choose a folder such as:

```text
dataset/
  pos/
    strain_A.fasta
    strain_B.fasta
  neg/
    strain_C.fasta
```

Folder names containing `pos`/`forward` or `neg`/`reverse` identify direction. Either side can be absent. Relative paths contribute to array names, so you may need manual matching to tree leaves later. Supply spacer records, not whole contigs.

For **SpacerTarget**, use `>strain|array|spacer` to encode sample identity:

```fasta
>strain_A|CRISPR1|spacer_1
CGCCAGGGTTTTCCCAGTCACGACGTTGTAAA
```

Each sequence must be 15–100 A/C/G/T bases. [Download an example](../examples/example-spacers.fasta). Alternatively use a header such as `>spacer_1 strain=strain_A array=CRISPR1 source=NC_000000.1`, replacing the placeholder source accession. Without strain metadata, matches appear in the table but not the strain network.

SpacerTarget CSV/TSV requires `sequence` or `Spacer_Sequence`. Supported metadata includes `strain_id` (aliases `strain`, `Folder`), `strain_name`, `array_id` (alias `Seq_Name`), `spacer_id`, `orientation` (alias `Orientation`) and `source_accession`. Sequence-only tables are searchable but lack strain mapping.

## Newick

ArrayPhyloViz accepts one Newick tree with unique leaf names and a final semicolon:

```text
((strain_A:0.12,strain_B:0.08)95:0.05,strain_C:0.18);
```

[Download this illustrative tree](../examples/example-tree.nwk). Colon values are branch lengths; `95` is an internal label supplied by the input. Quoted labels are accepted. Bracket comments are ignored. Matching uses original leaf names, not edited display labels.

## Array-tree ZIP

Enable **Also build array trees** in CRISPRPhylo before running. Each `group_*.nwk` contains arrays from one repeat group. `index.csv` lists built and skipped groups; `labels.csv` maps each `(group_id, tree_label)` to an original array UID. Use both columns to recover labels correctly. This ZIP is an output bundle, not an input accepted directly by the tree builder or ArrayPhyloViz. Extract the intended Newick and prepare array names/mapping for that specific tree.

## Editable figure data

Use **Save project** in ArrayPhyloViz to produce `crisprevo-composer` version 1 JSON, then **Open saved project** to restore it. Do not manually change node IDs; annotations depend on them. The array-upload control also accepts supported `crisprevo-spacermap` version 1 metadata JSON and SVG files carrying supported SpacerMap/ArrayPhyloViz metadata. Arbitrary JSON and image screenshots are not substitutes. **Save changes** stores an ArrayPhyloViz snapshot for seven-day recovery; **Save project** downloads the local reusable file.
