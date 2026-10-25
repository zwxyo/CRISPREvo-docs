# My Jobs: save, retrieve and download results

Use **My Jobs** (`/my-jobs`) to return to work from all six tools without an account. Each submitted analysis receives a full Job ID and private results link. Keep that link: the browser history is a convenience, not the result archive.

## Save access when submitting

1. Submit the input in the tool and wait until a Job ID and private link appear. Do not close the page while it is still uploading or submitting.
2. Select **Copy results link** and save it with your sample notes.
3. Once the job is accepted, you can close the page. Server processing continues.
4. Open the saved link later, or use **My Jobs → Retrieve a job** to paste the full ID or link and select **Retrieve**.

Individual tool pages also offer **Retrieve previous results**. If an ID belongs to another tool, the shared retrieval control directs you to that job's results page. Use the full 32-character ID, not a shortened display fragment.

![ArrayPhyloViz example2](examples/ArrayPhyloViz_example2.svg)

In ArrayPhyloViz, **Commit** composes the figure and attempts to save an initial project snapshot. After further edits, select **Save private link** again to create a new snapshot with a new ID. An older link does not automatically acquire later edits. **Save project** downloads JSON for local reopening and long-term storage.

## Understand status before interpreting a result

| Status                          | Meaning and action                                                                                                                        |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Queued**                | Accepted and waiting to start. Keep the link; do not repeatedly submit the same input.                                                    |
| **Waiting for resources** | Accepted, but server capacity is temporarily insufficient. Wait for the existing job; queue position may be shown.                        |
| **Running**               | Processing is in progress. Read the current phase; an activity bar is not a reliable completion-time estimate.                            |
| **Finished**              | Processing ended successfully. Open the results and check their actual content; an empty biological result can still be a successful run. |
| **Partially finished**    | Some inputs or steps failed while others produced output. Review the failed IDs/batches before interpreting coverage of the dataset.      |
| **Failed / Cancelled**    | Processing stopped. Available partial output can still be downloaded; a new analysis requires resubmitting input.                         |
| **Unavailable**           | The job could not be retrieved. Check the full link, service availability and expiry.                                                     |

A busy-service error **before a Job ID appears** means the submission was not accepted. Wait for the displayed retry interval, then submit again. This differs from an accepted job waiting in the queue.

## Browse the history

Filter by **Tool**, **Status** or job name/ID. Select the **Submitted** heading to switch newest/oldest first, and use **Previous / Next** for the six-row pages. Times are shown in your local time zone. **Refresh** checks current status.

| Action                     | What it does                                                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **View**             | Opens the job's results page.                                                                                       |
| **Copy link**        | Copies the private results link.                                                                                    |
| **Download**         | Downloads the saved result report when available; this is not every tool-specific export.                           |
| **Review results**   | Opens a failed, partial or cancelled job to inspect available evidence.                                             |
| **Remove**           | Removes the reference from browser history only. It neither cancels nor deletes the server job.                     |
| **Export job links** | Downloads all links in this browser history, including entries hidden by filters. It does not download result data. |

On the results page, use **Open results** to restore the interactive tool and access its exports. The page also offers **Download result report** and a primary output where supported: processed CSV, FASTA, Newick, SpacerMap SVG or project JSON. For CRISPRPhylo array-tree ZIP, reopen the interactive tool.

## Use another device

History is kept for the current browser tab by default. Enable **Remember job history on this device** to keep references across visits on that device. Turning it off removes the persistent history copy, not the server results.

History does not synchronize automatically. Open a saved link on the other device, or copy lines from **Export job links** into **Retrieve a job**. Up to 100 links/IDs can be imported at once. Anyone with a private link can access the job, so share links only with intended collaborators.

## Retention and reproducibility

Results and job records expire **seven days after the task ends**, including partial, failed and cancelled tasks. The results page displays an expiry time when available. Input workspaces are deleted at task completion; retrieving a result does not restore every original upload, parameter or run log.

Keep original inputs and a separate record of settings. CRISPRPrep results retain CSVs, counts, warnings and generated detection JSON when applicable, but uploaded JSON and processing options are not part of its saved result. CRISPRPhylo retains the original inferred tree and method fields, but not the input CSV or run log. SpacerTarget retains match evidence and query IDs; original full query sequences and search parameters are removed from terminal job reports. Other outputs, such as fetched FASTA and saved figure projects, remain downloadable until expiry.

Download important files before expiry. Neither **Remember job history**, **Export job links**, nor opening a link extends retention. For a failed or cancelled analysis, submit your input again. SeqFetcher's **Retry failed** and **Resume pending** submit selected sets of IDs as new jobs; they do not modify the previous job. SpacerTarget has no in-place retry after completion.
