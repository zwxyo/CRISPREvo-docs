# CRISPREvo user guide

CRISPREvo helps you prepare CRISPR data, compare spacer arrays, infer strain relationships, search candidate spacer targets and make integrated tree–array figures. Start from the material you already have; you do not need to run every tool.

## Workflow overview

![CRISPREvo workflow: sequence input, CRISPR data preparation, analysis and visualization](assets/images/CRISPREvo_workflow.svg)

[View the workflow at full size](assets/images/CRISPREvo_workflow.svg).

## Choose a tool

| What you want to do                      | Tool guide                                  | What to provide                                                    | What you get                                          |
| ---------------------------------------- | ------------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------------------- |
| Retrieve sequence records                | [SeqFetcher](tools/ncbi-sequence-fetcher.md) | NCBI sequence/assembly accessions or sequence UIDs                 | FASTA and source metadata                             |
| Detect arrays or filter detector results | [CRISPRPrep](tools/crispr-processing.md)     | Genome FASTA, NCBI nucleotide/assembly IDs or CRISPRCasFinder JSON | Processed spacer CSV and filtering report             |
| Infer relationships among strains        | [CRISPRPhylo](tools/crisprphylo.md)          | Processed spacer CSV for multiple strains                          | Strain tree, optional array-tree ZIP and images       |
| Compare spacer order and sharing         | [SpacerMap](tools/spacermap.md)              | Numbered arrays or spacer sequence data                            | Array figure, numbered arrays and cluster network     |
| Display arrays beside a tree             | [ArrayPhyloViz](tools/figure-composer.md)    | Newick plus numbered arrays or SpacerMap SVG                       | Annotated figure and editable project                 |
| Find candidate sequence targets          | [SpacerTarget](tools/spacer-target.md)       | Original spacer sequences with sample identities                   | Match table, strain–target network and search report |

Each guide explains input preparation, exact page controls, parameter choices, result interpretation, downloads and common problems. Button names follow the current interface: CRISPRPrep uses **Run**, SpacerTarget uses **Search**, and the other tools use **Commit** (the CRISPRPhylo control may read **Build tree** after a run).

## First visit

Follow the [quick start](quickstart.md) for a small, reproducible plotting example, then the [end-to-end workflow](workflow.md) for real multi-strain data. The [input reference](reference/input-formats.md) explains which file belongs in which tool; [downloads](reference/downloads.md) explains what to keep.

## Before interpreting an analysis

Check sample identities, retained data and the settings applied to the result. Shared spacer IDs describe the chosen numbering scheme; a tree describes the selected inference; a target match describes sequence similarity under the selected search and filters. The tool guides explain these distinctions with examples.

Save **Copy results link** once a Job ID appears. Accepted analyses continue on the server after closing the tab, and [My Jobs](my-jobs.md) restores results from all six tools. Results expire seven days after the task ends; download important outputs and retain original inputs/settings. In ArrayPhyloViz, save a new private snapshot or project JSON after further edits. See [troubleshooting](troubleshooting.md) for recovery help.
