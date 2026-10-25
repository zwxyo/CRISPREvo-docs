# Limits and retention

These limits reflect the current code. A hosting proxy can impose stricter upload limits. An accepted job can wait in a queue before its analysis begins; a busy response without a Job ID requires a later resubmission.

| Tool / area | Limit |
| --- | --- |
| SeqFetcher | 500 unique IDs; 16,000 submitted ID-text characters; ID-list file up to 1 MB |
| SeqFetcher requests | At most 5 IDs per request; assembly-containing lists automatically use 1 |
| SeqFetcher response | 8 million residues per batch, including all contigs of an assembly |
| SeqFetcher saved FASTA | 25 million characters per job, including headers/newlines |
| NCBI assembly package | 64 MiB downloaded or expanded package size |
| CRISPRPrep input | 20 MB combined; up to 100 imported JSON files |
| CRISPRPrep NCBI accession input | 100 unique nucleotide/assembly IDs and 16,000 text characters; combined retrieved FASTA up to 20 MB |
| CRISPRPrep extraction | 100,000 arrays or extracted spacer rows |
| CRISPRPrep minimum-spacer option | Integer 1–10,000; default 2 |
| CRISPR detection runtime | 2 hours of detector processing; resource waiting can add elapsed time |
| CRISPRPhylo runtime | 15 minutes for inference; queue waiting is separate |
| CRISPRPhylo repeat-distance option | Nonnegative integer; default 0 |
| SpacerTarget input | 2 MB combined, up to 100 files |
| SpacerTarget sequence format | Each spacer 15–100 A/C/G/T bases |
| SpacerTarget queries / occurrences | 100 distinct sequences / 10,000 occurrences |
| SpacerTarget search batches | At most 10 distinct sequences and 1,000 bases per batch |
| SpacerTarget target limit | 20, 50 (default), or 100 targets per spacer |
| SpacerTarget network | Up to 200 displayed nodes; tables/exports retain all filtered rows |
| SpacerTarget submitted search | A batch exceeding 24 hours is marked failed |
| ArrayPhyloViz Newick | One tree, up to 1 MB |
| ArrayPhyloViz tree | 1,000 leaves, 4,000 nodes, depth 250 |
| ArrayPhyloViz array text | 4 MB |
| ArrayPhyloViz arrays | 1,500 arrays; 2,000 spacers per array; 20,000 total instances |
| ArrayPhyloViz / CRISPRPhylo PNG | 32 million pixels and at most 16,000 pixels per side |
| SpacerMap raster DPI | 36–2,400; default 300 |
| My Jobs bulk link import | 100 links/IDs per import |
| Saved results and task records | Seven days after the task ends |

## What survives closing the page?

Once a Job ID appears, the accepted analysis continues on the server. Save its private link and retrieve it through [My Jobs](../my-jobs.md). The default browser history lasts only for the tab; **Remember job history on this device** preserves references locally but does not extend server retention.

Original input workspaces are deleted when a task ends. Saved outputs are not a complete archive of uploads, settings or logs. Keep those separately. Further ArrayPhyloViz edits need **Save private link** or a downloaded project; CRISPRPhylo rerooting changes only the current view/download, not the saved original tree.

For larger data, split analyses into biologically meaningful groups and use SVG for large figures. Download important results before expiry; opening a result link does not reset its expiry time.
