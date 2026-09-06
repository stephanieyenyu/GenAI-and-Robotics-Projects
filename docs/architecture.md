# Architecture

This repository has no architecture in the usual sense. There are no services, no deployment
target, no runtime, and no shared codebase. What follows describes the pipeline the six notebooks
have in common and the data flow inside each one, because that is the only structural claim this
repository can support.

Snapshot 2026-09-06.

---

## What This Repository Is Not

Stating this first avoids a reader looking for something that is not here.

There is no server, no container, and no deployment blueprint. Nothing in this repository listens
on a port, holds state between invocations, or has an operational mode. There is no database. No
notebook imports another notebook, and no module is shared between folders, so there is no
dependency graph to draw. Each `.ipynb` is a standalone document that a Colab session executes top
to bottom.

Consequently there is no failure mode that spans two components, no consistency question, and no
integration contract. Every failure recorded in `known-issues.md` is contained within one
notebook.

---

## The Shared Four-Stage Pipeline

Every assignment here follows the same shape, and the shape is worth naming because it is what
makes the six comparable.

| Stage | Mechanism | Persists after the session? |
|---|---|---|
| Acquire | `wget` or `git clone` pulls the dataset into the Colab working directory | No |
| Prepare | Chunking, tokenisation, embedding, or chat-template formatting builds an in-memory structure | No |
| Adapt | A quantised base model is prompted, or a LoRA adapter is trained against it | Adapter only, if explicitly copied to Drive |
| Emit | Predictions are serialised to a file named by student identifier for external judging | No |

The right-hand column is the structural fact that governs this repository. Three of four stages
leave nothing behind. Colab destroys its filesystem when a session ends, so unless a cell writes to
Google Drive — which only HW7 does, and only for its adapter — every artefact of a run exists
solely as the output text embedded in the saved notebook.

**This is why missing outputs are unrecoverable.** There is no cache, no checkpoint directory, and
no log to read a lost evaluation number out of. A figure absent from the notebook is absent
everywhere, and the only route to recovering it is executing the cell again.

---

## Data Flow by Assignment

### HW2 — retrieval augmented generation

```
course host ──wget──> queries (JSONL) ─────────────────────┐
                    └> novel full text ──chunk 850/500──> chunks ──┬──jieba──> BM25 index ──┐
                                                                   └──E5─────> dense matrix ─┴──RRF──> top-k passages
                                                                                                            │
                                                        Gemma-3-4B (4-bit NF4) <──system + user prompt <─────┘
                                                                    │
                                                                    └──> responses ──> {student_id}.json
```

Two indices are built over the same chunk list and both stay resident. The fusion step consumes
both rankings and emits one. The generator sees only the fused passages, never the scores, which
is what makes the fusion irreversible downstream.

### HW5 — regression

```
train.csv ──┬── 9 numeric columns (12 declared, 3 silently dropped) ──────────────┐
            ├── positive_ratio, price_per_hour (derived)                          │
            └── reviews, short_description ──mpnet──> 768-d ──PCA(100)──> 100-d ──┴──concat──> scaler ──> MLP ──> score
```

The scaler is fitted on the training concatenation. The inference path rebuilds the same
concatenation independently against `test.csv`, and the two constructions produce different column
counts. That divergence is C-3 and is the reason no prediction was emitted.

### HW7 and HW8 — adaptation

```
base model ──4-bit NF4 quantise──> frozen weights
                                        │
dataset ──chat template──> formatted ───┴──LoRA(r=8)──> adapter ──merge──> generation ──> {student_id}.txt
```

HW7 targets the query, key, and value projections with alpha 32; HW8 uses alpha 16 and additionally
filters its training set to 2,491 of 7,472 examples before formatting. HW7 defines a second stage,
direct preference optimisation over the fine-tuned adapter, which is present as code and was never
executed.

### HW4 — no pipeline

The repository holds a token counter. The artefact this assignment produces, a defence prompt, was
submitted to an external judging system and is not present. There is no data flow to draw.

---

## The Compute Envelope

Every run in this repository executed against the same device.

| Property | Value |
|---|---|
| GPU | Tesla T4 |
| Device memory | 15,360 MiB |
| Driver | 550.54.15 |
| CUDA | 12.4 |
| Persistence | None between sessions |

This single row of the table explains most of the design decisions recorded in the root README.
4-bit quantisation, low-rank adaptation, subset filtering, and passage-count limits are all
consequences of a 15 GiB ceiling rather than independent choices. The ceiling was reached exactly
once, observably: HW2's boss baseline requested 934 MiB against 546 MiB free with 14.21 GiB already
held.
