# Input formats

Use this page to choose the correct input and preserve sample identities. Save text files as UTF-8. Examples are illustrative, not biological validation datasets.

## Which file goes where?

| File/data | Tool |
| --- | --- |
| NCBI sequence accessions or UIDs | SeqFetcher; nucleotide IDs also work in CRISPRPrep’s NCBI accession mode |
| GCA_/GCF_ assembly accession | SeqFetcher in Nucleotide mode, or CRISPRPrep’s NCBI accession mode |
| Genome/contig FASTA from one sample | CRISPRPrep detection |
| CRISPRCasFinder JSON | CRISPRPrep; SpacerMap and SpacerTarget accept it with different filtering rules |
| Processed spacer CSV | SpacerMap, CRISPRPhylo, SpacerTarget |
| Spacer FASTA | SpacerMap or SpacerTarget, with different grouping conventions below |
| Numbered arrays | SpacerMap and ArrayPhyloViz; not SpacerTarget or CRISPRPhylo |
| Newick tree plus arrays | ArrayPhyloViz |
| Saved Composer project JSON | ArrayPhyloViz **Open saved project** |

## Genome FASTA

Every record begins with a unique `>` header followed by IUPAC DNA sequence. CRISPRPrep accepts one file with multiple contigs per detection run. Treat that input as one sample; process different strains separately before batch-importing their JSON results.

```fasta
>sample_A_contig_1
ATGCGTACGTTAGCTAGCTAGCTAGCTAGC
>sample_A_contig_2
GGCATCGATCGATCGATCGATCGATCGTTA
```

These short sequences demonstrate syntax only; they are not expected to produce CRISPR arrays.

## NCBI ID list

Use one or more nucleotide IDs such as `NC_012920.1` or `NM_000546.6`, or protein IDs such as `NP_000537.3` in a separate Protein run. Newlines, commas, spaces and semicolons separate IDs. Uploaded TXT/CSV/TSV must contain IDs only, with no column header. Sequence UIDs are accepted. GCA_/GCF_ assembly accessions are accepted in **Nucleotide** mode, for example `GCF_000339015.1`. They identify an assembly whose downloaded FASTA preserves separate chromosome/contig records. Gene, Taxonomy, BioProject and SRA IDs are not supported. CRISPRPrep accepts up to 100 nucleotide/assembly IDs and combines successful records into one detection input; SeqFetcher accepts up to 500 IDs. Both submitted ID lists are limited to 16,000 characters.

## CRISPRCasFinder JSON

Provide the detector's result object containing `Sequences`, each sequence's `Crisprs`, and each array's `Regions`. Keep the original JSON for reprocessing.

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
| `Seq_Name` | Array ID. SpacerMap groups by this field alone, so different arrays need globally unique names. |
| `Organism_Name` | Description-derived label, used by CRISPRPrep's organism-wide N filter; not a substitute for strain ID. |

SpacerMap accepts pasted CSV as well as uploaded CSV when its header includes `Spacer_Sequence`. Its minimum custom CSV header is `Seq_Name,Orientation,Spacer_Sequence`. It can compute a missing negative-strand reverse complement. CRISPRPhylo requires all seven columns and a usable reverse-complement spacer value for reverse rows. `Effective_DR` in CRISPRPrep output is the orientation-appropriate repeat.

## Numbered arrays

Use one array per line. A tab separates the unique name from its spacer IDs; commas, spaces or semicolons separate IDs.

```text
strain_A	1,2,3,6,7,9
strain_B	1,2,4,6,8,9
strain_C	10,2,4,6,8
```

[Download this example](../examples/example-arrays.txt). IDs identify types within the dataset, not globally across runs. Generate IDs for all arrays together in SpacerMap, then reuse the numbered TXT. DNA arrays in uploaded TXT/TSV can be converted automatically by SpacerMap; pasted array-format text treats tokens as labels; a recognized spacer CSV is converted through the CSV route.

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

Use **Save project** in ArrayPhyloViz to produce `crisprevo-composer` version 1 JSON, then **Open saved project** to restore it. Do not manually change node IDs; annotations depend on them. The array-upload control also accepts supported `crisprevo-spacermap` version 1 metadata JSON and SVG files carrying supported SpacerMap/Composer metadata. Arbitrary JSON and image screenshots are not substitutes. **Save private link** stores an ArrayPhyloViz snapshot for seven-day recovery; **Save project** downloads the local reusable file.
