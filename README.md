# GenAI Coursework — Five Assignments in Retrieval, Fine-Tuning, and Alignment

Five assignments from a generative AI and machine learning course, run on Google Colab between
November and December 2025 and collected here as a coursework record. Each one adapts a
pretrained model to a task under a fixed compute budget: a single Tesla T4 with 15 GiB, shared
across model weights, retrieval indices, and generation.

Writing the model code is not the hard part; the course supplies a scaffold and marks the gaps
with TODOs. Three things took the work. **Choosing what to retrieve on**, because a fixed-width
chunk of classical Chinese respects no sentence boundary and the two retrievers scoring it
disagree about which chunk matters. **Choosing what to train on**, because a subset selected by
length is a different curriculum from the full set, and the compute budget does not permit the
full set. **Choosing what to record**, which is where this repository is weakest, and the reason
the sections below are written the way they are.

What is here is five parseable notebooks, five project READMEs, and the configurations that were
actually executed. **No assignment here has a recorded end-to-end result.**

---

## What It Does

**Builds a hybrid retriever rather than choosing between sparse and dense.** HW2 indexes the full
text of *Dream of the Red Chamber* twice — BM25 over jieba tokens, and dense vectors from
`intfloat/multilingual-e5-large` — and combines the two rankings by reciprocal rank fusion. Four
retrieval configurations of increasing strength share one index and one generator.

**Treats free text as a feature rather than dropping it.** HW5 predicts Metacritic scores for
Steam titles from tabular columns plus two text columns, `reviews` and `short_description`, each
encoded with `all-mpnet-base-v2` and reduced to 100 dimensions by PCA before concatenation.

**Adapts models it cannot afford to train.** HW7 and HW8 both apply LoRA to a quantised base:
4-bit NF4 with double quantisation and a bfloat16 compute dtype. Nothing here trains a full
parameter set, and nothing here needed to.

**Separates the capability being taught from the capability being lost.** HW8 fine-tunes on
GSM8K and evaluates on two axes — the mathematics task it was trained for, and the AILuminate
safety set it was not. The evaluation is the assignment; the fine-tuning is the setup.

**Records the configurations rather than the outcomes.** Every notebook preserves its
hyperparameters, model identifiers, and dataset filters. What most of them do not preserve is the
number the configuration produced. This is stated here rather than discovered later.

---

## Scope

Single-author coursework. Every notebook, every filled-in TODO, and every README in this
repository is my own work; the scaffolds, datasets, and evaluation harnesses were supplied by the
course.

| Folder | Scope |
|---|---|
| `HW2-RAG-System` | Hybrid retrieval over a classical Chinese novel — fixed-width chunking at 850 characters with a 500-character stride, BM25 over jieba tokens, dense retrieval with a multilingual E5 encoder, reciprocal rank fusion, four generation baselines against a 4-bit Gemma-3-4B |
| `HW4-LLM-Defense` | Defence prompting against prompt injection. The repository holds only the course-supplied token counter; the defence prompt itself was submitted to an external judging system and is not here |
| `HW5-Regression` | Metacritic score regression over Steam titles — feature selection, two derived ratio features, sentence-transformer embeddings of two text columns reduced by PCA, an MLP with a projection shortcut |
| `HW7-LLM-SFT-RL` | Supervised fine-tuning of a Gemma-3-4B chat model with LoRA, followed by a direct preference optimisation stage that was never executed |
| `HW8-Catastrophic-Forgetting` | LoRA fine-tuning of Llama-3.2-1B-Instruct on a length-filtered GSM8K subset, with evaluation harnesses for both GSM8K accuracy and AILuminate safety |

A sixth folder, `HW6-CNN-Classification`, was removed 2026-09-21 — its notebook was two bytes and
unparseable, and the source was unrecoverable; see [`docs/known-issues.md`](docs/known-issues.md)
C-2. My robotics coursework lives in two separate repositories,
[`LLM_RobotArm`](https://github.com/stephanieyenyu/LLM_RobotArm) and
[`AMR_System`](https://github.com/stephanieyenyu/AMR_System) — nothing in this one touches
robotics; see [`docs/known-issues.md`](docs/known-issues.md) D-7.

5 parseable notebooks · 193 cells · 90 code cells · 44 cells with recorded output ·
7 error outputs · 5 project READMEs.

Cell counts come from parsing each `.ipynb` as JSON and counting `cells` by `cell_type`; a cell
counts as having recorded output when its `outputs` array is non-empty. Every derivation is in
[`docs/metrics.md`](docs/metrics.md).

Assignment work ran 2025-11-10 to 2025-12-08, dated from the `nvidia-smi` timestamps preserved in
the notebook outputs. Documentation was written 2026-09-06.

---

## Test Setup and Success Criterion

Everything ran on Google Colab against a single Tesla T4, driver 550.54.15, CUDA 12.4, 15,360 MiB
of device memory. Datasets were downloaded from course-controlled hosts at execution time and are
not vendored here. No second machine, no second GPU, and no second person were involved.

The course defined success as clearing a graded baseline — simple, medium, strong, and boss tiers
in HW2, and equivalent thresholds elsewhere — with submission to an external judging system that
returned a score. That system is the authority on whether any of this worked.

**No score from that system is recorded in this repository.** Nothing here establishes whether
any assignment cleared any baseline. The notebooks preserve what was configured and, in most
cases, not what it achieved. Any reader treating this repository as evidence of performance is
reading something it does not contain.

---

## Measurement Basis

There is no measured result set in this repository, so the table below reports evidence strength
rather than value. The Evidence column states what backs each figure: *notebook output* means the
value is present in a committed cell output and can be read directly; *not recorded* means no such
value exists anywhere in the repository.

Snapshot 2026-09-06. Derivations in [`docs/metrics.md`](docs/metrics.md).

| Measurement | Value | Evidence |
|---|---|---|
| Notebooks in repository | 5, all of which parse | File inspection |
| Cells across parseable notebooks | 193 | Notebook output — JSON parse |
| Code cells with recorded output | 44 of 90 | Notebook output — JSON parse |
| Error outputs across all notebooks | 7 | Notebook output — JSON parse |
| Output cells by assignment | HW7 19 · HW2 10 · HW5 7 · HW8 7 · HW4 1 | Notebook output — JSON parse |
| HW5 training loss at early stop | 0.0279 | Notebook output, cell 20 |
| HW5 validation loss at early stop | 0.553 | Notebook output, cell 20 |
| HW5 early-stop epoch | 557 of 7,000 configured | Notebook output, cell 20 |
| HW8 fine-tuning subset retained | 2,491 of 7,472 | Notebook output, cell 21 |
| HW5 test-set score | not recorded | Inference cell raises `ValueError` |
| HW2 answers produced, any baseline | not recorded | All four generation cells aborted |
| HW7 supervised fine-tuning metrics | not recorded | Training ran; no metric cell was executed |
| HW7 preference optimisation result | not recorded | Stage never executed |
| HW8 GSM8K accuracy, before or after | not recorded | Evaluation cell has no output |
| HW8 AILuminate safety result | not recorded | Evaluation cell has no output |
| HW4 defence prompt and its score | not recorded | Artefact is not in this repository |
| Course-assigned grades, any assignment | not recorded | No submission receipt is committed |

Three qualifications govern how the table should be read.

**Loss values are training diagnostics, not results.** The two HW5 figures are the only numeric
outputs in this repository describing model behaviour, and they describe it on data the model was
fitted to and validated against. A training loss of 0.0279 against a validation loss of 0.553 is
a twentyfold divergence, which characterises overfitting rather than accuracy. Neither figure is
a test score and neither should be quoted as one.

**Absence of a number does not mean the assignment failed.** Every notebook here was submitted and
graded; the grades are simply not in this repository. The distinction matters because the two
readings — work that was never completed, and work whose record was not preserved — differ
substantially, and nothing committed here settles which applies.

**Cell counts describe the artefact, not the effort.** The 193-cell figure counts scaffold cells
supplied by the course alongside cells I wrote. No attempt has been made to separate the two, and
the count should not be read as a volume-of-work claim.

---

## Problem Statement

A pretrained language model that fits a research budget does not fit a student one. Gemma-3-4B in
bfloat16 is roughly 8 GiB before a KV cache, before a retrieval index, and before the activations
of generation. The device available across every assignment here holds 15 GiB in total, and each
course task requires the model plus something else resident at the same time — a BM25 index and a
dense embedding matrix over a full novel in HW2, a LoRA adapter and its optimiser state in HW7 and
HW8.

The naive approach fails in a specific and observable way. HW2 attempted its highest-context
retrieval configuration and terminated with a CUDA out-of-memory error, requesting 934 MiB against
546 MiB free on a device already holding 14.21 GiB. That is not a tuning problem; it is the budget
binding.

Every technical decision in this repository is downstream of that constraint. Weights are quantised
to 4 bits because they must be. Adaptation is low-rank because full fine-tuning has no room.
Retrieval is fused rather than dense-only because a fused ranking extracts more from a fixed number
of passages, and the number of passages is what memory limits.

The problem these five assignments address is therefore adaptation under a fixed and small memory
budget: how much task-specific behaviour can be obtained from a pretrained model when the model,
its index, and its training state must all fit in 15 GiB at once.

---

## Shared Pipeline and Data Flow

This repository has no services, no deployment target, and no runtime. It is five Colab notebooks
that share a pattern rather than a codebase; nothing here imports anything else here, and each
notebook runs standalone.

The shared pattern is four stages.

| Stage | What happens | Where it varies |
|---|---|---|
| Acquire | `wget` or `git clone` from a course-controlled host into the Colab working directory | HW2 and HW8 pull from an NTU host; HW5 and HW7 clone GitHub repositories |
| Prepare | Text is chunked, tokenised, embedded, or formatted into chat turns | HW2 chunks by character window; HW5 embeds and reduces; HW7 and HW8 apply a chat template |
| Adapt | A quantised base model is either prompted or LoRA-tuned | HW2 and HW4 prompt only; HW5 trains from scratch; HW7 and HW8 apply LoRA |
| Emit | Predictions are written to a file named by student identifier for external judging | Every assignment; no emit cell in this repository has output |

Data flows one way and never returns. Nothing written by one notebook is read by another, no
intermediate artefact is committed, and the only persistence between stages is the Colab session
filesystem, which is destroyed when the session ends. That is why a rerun is the only way to
recover any missing number: there is no cache to read them out of.

---

## Design

### Two retrievers disagree, so fuse the rankings rather than choose one

BM25 over jieba tokens and dense retrieval over a multilingual E5 encoder score the same
850-character chunks and return different orderings. The medium baseline used BM25 alone, taking
its top ten passages.

Sparse retrieval on classical Chinese matches characters that recur, which favours names and place
references and misses paraphrase. Dense retrieval matches meaning and loses the proper nouns a
question about a novel is usually anchored on. Choosing one discards the failure mode the other
covers.

The strong and boss baselines run both retrievers and combine their rankings by reciprocal rank
fusion, which needs no score calibration between two systems whose scores are not comparable.

The accepted cost is two indices resident at once — a BM25 object over every chunk and a dense
matrix over every chunk — on the device where the boss baseline subsequently ran out of memory.
The fusion is not free, and it was not free here.

### Text columns carry signal that tabular columns do not, so embed them

HW5 predicts a Metacritic score from Steam metadata. The declared numeric feature set is twelve
columns, of which nine survive a filter against the dataframe.

Nine numeric columns describe a game's commercial performance and say almost nothing about its
reception, which is what the target measures. The information distinguishing a well-reviewed title
from a badly-reviewed one sits in the review text and the description, both free text, neither
consumable by a linear layer.

Both text columns are encoded with `all-mpnet-base-v2` and reduced to 100 dimensions each by PCA,
then concatenated onto the numeric block.

The accepted cost is a feature construction path written twice, once for training and once for
inference, and the two diverged. The inference cell raises `ValueError: X has 210 features, but
StandardScaler is expecting 212 features`, so this design produced no test prediction at all. The
cost of the duplicated path exceeded the benefit of the embeddings, and that is the result rather
than an aside.

### A subset chosen by length is a curriculum, not a sampling convenience

HW8 fine-tunes on GSM8K. The course scaffold selects a third of the training set by letter count
and marks the block as not to be modified.

Length on GSM8K correlates with reasoning-chain depth: a longer answer is one with more
intermediate steps. Selecting by length therefore selects by difficulty, and the direction of the
selection determines what the model is taught.

The block in this repository was modified to invert the sort, retaining the shortest third rather
than the longest — 2,491 examples of 7,472.

The accepted cost is a training distribution skewed toward shallow reasoning chains, against an
evaluation set that is not. The change is also undeclared in the notebook's own output, which
still prints that the longest examples were kept, and that discrepancy is a defect rather than a
cost — see C-7.

### Low-rank adaptation on a quantised base, because nothing else fits

HW7 and HW8 both load their base model in 4-bit NF4 with double quantisation and a bfloat16
compute dtype, then attach a LoRA adapter — rank 8 with alpha 32 over the query, key, and value
projections in HW7; rank 8 with alpha 16 in HW8.

Full fine-tuning of a 4B model requires the weights, their gradients, and two optimiser moments
resident simultaneously. On 15 GiB, that is not a tuning question.

The accepted cost is stated by the library itself: merging a LoRA adapter into a 4-bit linear
layer produces different generations due to rounding, a warning present in the committed output of
HW8 cell 23. The adapted model is not the model that would have resulted from training at full
precision, and no comparison between the two was made here.

---

## Evaluation

There is no results table, because there are no results. What follows is a record of what each
notebook executed and where it stopped.

| Assignment | Ran | Stopped at | Recorded output |
|---|---|---|---|
| HW2 | Index build, model load, all four baselines started | Three baselines interrupted manually; boss baseline out of memory | No answers, no submission file |
| HW4 | Token counter only | Nothing to stop | Token count widget, no defence prompt |
| HW5 | Training to early stop at epoch 557 | Inference — `ValueError` on feature count | Train 0.0279, validation 0.553 |
| HW7 | Supervised fine-tuning | Preference optimisation stage never started | Training cell output only |
| HW8 | LoRA fine-tuning | Both evaluation cells never executed | Training cell output only |

The negative results are more informative than the positive ones and are given the same space
here.

**The boss baseline in HW2 is the only genuine failure, and it is a useful one.** It requested 934
MiB against 546 MiB free on a device holding 14.21 GiB, having loaded a quantised 4B model, a BM25
index, and a dense embedding matrix over the entire novel. The three preceding baselines were
interrupted rather than failed, which means the memory ceiling was reached only when passage count
rose. That locates the constraint precisely: not the model, not the index, but the number of
retrieved passages placed in context.

**HW5's twentyfold train-validation gap is a result even without a test score.** Training loss fell
to 0.0279 while validation loss sat at 0.553 and early stopping triggered at epoch 557 of a
configured 7,000. Two hundred PCA dimensions over a few thousand training rows is a regime where
that gap is the expected outcome, and a test score would most likely have confirmed it rather than
contradicted it.

**HW7 and HW8 both stopped exactly where the assignment's question begins.** HW7 completed
supervised fine-tuning and never ran preference optimisation, so it demonstrates the setup for an
alignment comparison and not the comparison. HW8 completed fine-tuning and ran neither evaluation,
so it demonstrates the setup for a forgetting measurement and not the measurement. In both cases
the interesting half is the half that is missing.

---

## Threats to Validity

The complete issue set, classified by whether each was verified against source or against committed
output, is in [`docs/known-issues.md`](docs/known-issues.md).

**Construct validity — the documentation described different models from the ones that ran.**
Before this revision, three model identifiers in the READMEs did not match the notebooks, most
consequentially an eightfold overstatement of HW8's model size. That was documentation error
rather than measurement error, but it is the same failure in kind: a stated quantity the artefact
does not support.

**Internal validity — nothing here isolates a cause.** No assignment ran an ablation, a seed
sweep, or a comparison against an unmodified configuration. Where a design choice is argued above,
the argument is from the code and from general reasoning, not from a controlled contrast run in
this repository.

**Instrumentation — the record is the weak link, not the work.** Forty-four of ninety code cells
carry output. The missing outputs cluster at the end of each notebook, which is where evaluation
lives, and Colab sessions destroy their filesystem on disconnect. The most likely explanation is
that cells ran and their outputs were lost to a session restart rather than never running at all,
but that is a hypothesis and the repository cannot distinguish it from the alternative.

**Configuration — two failures here are silent.** HW8's Hugging Face login is invoked as `hhf`
rather than `hf`, so it never authenticated and the notebook proceeded as though it had. HW5's
feature filter drops any declared column absent from the dataframe without warning, silently
removing three of twelve including both a comment marks as assumed. Neither raises; both change
what ran.

**External validity — this is coursework and generalises to nothing.** Datasets, scaffolds,
baselines, and success thresholds were all set by the course. Nothing here characterises how any
of these techniques behaves outside the specific assignment it was written for.

---

## Open Problems

The first came out of HW8 and is the one I would most want to pursue. Fine-tuning on a mathematics
set is expected to degrade safety behaviour the model was aligned for, and the assignment measures
both axes to expose the trade-off. What the assignment does not address is whether the degradation
is a property of the fine-tuning objective or of the data distribution underneath it — whether a
model taught arithmetic forgets refusal because gradient updates overwrite the aligned behaviour,
or because the training set contains no instance of it. Those have different mitigations, and this
repository's data cannot separate them, because only one training distribution was ever run.

The second came out of HW2. Reciprocal rank fusion combines two rankings without needing their
scores to be comparable, which is exactly why it is used, and also why it discards the information
that would say which retriever was right. On a question about a classical novel, sparse and dense
retrieval fail in complementary ways, and a fused ranking hides which failure occurred on any given
query. Recovering that — knowing per query which retriever carried the answer — would require a
labelled relevance set the assignment did not provide and the fused output cannot reconstruct.

The third came out of the record itself. Every assignment here preserves its configuration and
loses its outcome, and the loss is systematic rather than incidental: it happens at the evaluation
cell, after a session restart, to the number that would have made the configuration interpretable.
A configuration without its outcome is not evidence of anything. Writing evaluation output to a
persisted file at the moment it is produced is a smaller problem than the two above, and it is the
one that has actually cost me something.

**The compute envelope shaped every technical decision here.** One T4, 15 GiB, no persistence
between sessions. Quantisation, low-rank adaptation, subset selection, and passage-count limits are
consequences of that envelope rather than independent design choices, and none of the conclusions
above transfers to a setting where the budget is not binding.

What I want to work on is the first of these: characterising what a model loses during adaptation,
and whether the loss can be attributed to the objective or to the data.

---

## Repository Layout

```
.
├── HW2-RAG-System/
│   ├── README.md                   Hybrid retrieval over a classical Chinese novel
│   └── hw2_code.ipynb              36 cells — BM25 + E5 dense retrieval, RRF fusion, 4 baselines
├── HW4-LLM-Defense/
│   ├── README.md                   Defence prompting; artefact lives in the course judge
│   └── hw4_code.ipynb              2 cells — course-supplied token counter only
├── HW5-Regression/
│   ├── README.md                   Metacritic score regression over Steam metadata
│   └── hw5_code.ipynb              23 cells — mpnet embeddings, PCA to 100, MLP with shortcut
├── HW7-LLM-SFT-RL/
│   ├── README.md                   SFT on Gemma-3-4B; DPO stage present but unrun
│   └── hw7_code.ipynb              93 cells — LoRA r8 alpha32 on q/k/v, DPO trainer subclass
├── HW8-Catastrophic-Forgetting/
│   ├── README.md                   GSM8K fine-tuning against AILuminate safety evaluation
│   └── hw8_code.ipynb              39 cells — Llama-3.2-1B, 4-bit NF4, LoRA r8 alpha16
├── docs/
│   ├── architecture.md             Stage-by-stage data flow shared across the notebooks
│   ├── metrics.md                  Derivation of every figure quoted in this README
│   └── known-issues.md             19 entries, classified and dispositioned
├── .env.example                    Credentials needed to obtain the data and the models
├── .gitignore
└── README.md
```

## Tech Stack

**Models** Gemma-3-4B · Llama-3.2-1B-Instruct · multilingual-E5-large · all-mpnet-base-v2

**Adaptation** PyTorch · Hugging Face transformers · peft · trl · bitsandbytes 4-bit NF4 · LoRA

**Retrieval** rank_bm25 · jieba · sentence-transformers · reciprocal rank fusion

**Classical ML** scikit-learn · PCA · pandas · numpy

**Environment** Google Colab · Tesla T4 · CUDA 12.4

## Running Locally

These notebooks were written for Colab and are not packaged to run elsewhere. To reproduce one:

```
Open the notebook in Google Colab.
Add a Hugging Face token as a Colab secret named HF_TOKEN — do not paste it into a cell.
Runtime > Change runtime type > T4 GPU.
Run cells in order.
```

A Hugging Face account with read scope is required for HW7 and HW8; the Gemma licence must be
accepted on the Hub before the HW7 base model will download. Datasets are fetched at runtime from
course-controlled hosts listed in [`.env.example`](.env.example), none of which is under my
control, so a notebook that runs today may stop running once a course account is retired.

Three configurations look correct and are not.

`hhf auth login` in HW8 is a typo for `hf` and fails silently at the shell level. The notebook
continues and downloads what it can anonymously, so the absent login is invisible until a gated
model is requested.

The feature filter in HW5 cell 12 removes any declared column the dataframe does not hold, without
warning. Three of twelve declared features are dropped this way. The printed list shows what
survived, not what was asked for, and the two are easy to conflate.

The subset filter in HW8 cell 21 prints that it kept the longest third of the training data. It
kept the shortest third. Reading the output rather than the code gives the opposite of what ran.

---

## Author

**Stephanie Lin, Yen Yu**

Industrial Engineering and Engineering Management, National Tsing Hua University · coursework
completed November – December 2025 · Hsinchu, Taiwan

Coursework submitted for credit. Scaffolds, datasets, and evaluation harnesses were supplied by
the course; the filled-in implementations, configurations, and all documentation in this
repository are my own. Shared for portfolio purposes. Not licensed for reuse.
