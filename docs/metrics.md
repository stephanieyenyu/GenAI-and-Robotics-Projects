# Metrics

Every figure quoted in the root README is derived here. All of it comes from the committed
notebook files themselves — there is no database, no export, and no external log behind this
repository. The purpose of this document is to let a reader verify each number rather than accept
it, which matters more here than in most projects, because most of the numbers a reader would
expect to find are absent and the reason for each absence is checkable.

**Snapshot date** 2026-09-06 · updated 2026-09-21 (HW6 removed, see
[`known-issues.md`](known-issues.md) C-2; scale figures below reflect the five remaining
notebooks)

**Observation window** 2025-11-10 to 2025-12-08, dated from `nvidia-smi` timestamps preserved in
notebook outputs

**Raw export** None. The notebooks are the primary source; every command below runs against the
repository working tree

---

## Sources

| Source | What it contains |
|---|---|
| `HW*/hw*_code.ipynb` | Cell structure, source, and any outputs preserved at the last save. The only evidence of what executed |
| `HW*/README.md` | Per-assignment descriptions written after the fact, not generated from the notebooks |
| Cell output of `nvidia-smi` | Device model, driver version, CUDA version, total memory, and the wall-clock date of the run |
| Traceback text in error outputs | Exception type, message, and in the CUDA case the exact allocation figures |

Nothing in this repository was produced by an instrumented run. Every figure below is either a
structural property of the files or a value a notebook happened to print.

---

## Scale

```bash
python3 - <<'PY'
import json, os, glob
tot = {'cells':0,'code':0,'md':0,'out':0,'err':0}
for f in sorted(glob.glob('HW*/hw*_code.ipynb')):
    try: nb = json.load(open(f))
    except Exception:
        print(f'{f:44} UNPARSEABLE  {os.path.getsize(f)} bytes'); continue
    cs = nb['cells']
    code = [c for c in cs if c['cell_type'] == 'code']
    out  = [c for c in code if c.get('outputs')]
    err  = [o for c in code for o in c.get('outputs',[]) if o.get('output_type') == 'error']
    print(f'{f:44} cells={len(cs):3} code={len(code):3} with_output={len(out):3} errors={len(err)}')
    tot['cells'] += len(cs); tot['code'] += len(code)
    tot['out'] += len(out);  tot['err'] += len(err)
print(tot)
PY
```

| Figure | Value | Derivation |
|---|---|---|
| Notebooks in repository | 5 | `ls HW*/hw*_code.ipynb \| wc -l` |
| Notebooks that parse as JSON | 5 | The script above; all of them |
| Total cells | 193 | Sum of `len(nb['cells'])` across the five notebooks |
| Code cells | 90 | Cells with `cell_type == 'code'` |
| Markdown cells | 103 | 193 minus 90 |
| Code cells with recorded output | 44 | Code cells whose `outputs` array is non-empty |
| Error outputs | 7 | Output entries with `output_type == 'error'` |
| Project READMEs | 5 | One per folder |

**What these figures do not claim.** The cell counts include scaffold cells supplied by the course
alongside cells written by the author. No separation between the two was attempted, and none is
recoverable from the files, because the scaffold was edited in place rather than extended. A cell
count is a size measurement of the artefact, not of the work.

---

## Per-Notebook Breakdown

| Notebook | Bytes | Cells | Code | With output | Errors |
|---|---|---|---|---|---|
| `hw2_code.ipynb` | 1,242,503 | 36 | 15 | 10 | 4 |
| `hw4_code.ipynb` | 74,917 | 2 | 1 | 1 | 0 |
| `hw5_code.ipynb` | 84,483 | 23 | 11 | 7 | 1 |
| `hw7_code.ipynb` | 977,271 | 93 | 45 | 19 | 2 |
| `hw8_code.ipynb` | 187,709 | 39 | 18 | 7 | 0 |

**Byte size does not track work.** `hw2_code.ipynb` is sixteen times the size of `hw5_code.ipynb`
and holds two thirds as many code cells. The difference is embedded progress-bar output and
tokeniser download logs, not content.

**HW8 has no error outputs and no results.** Zero errors is the expected reading of a clean run.
It is not one here: the fine-tuning cell completed and the two evaluation cells were never
executed, so nothing had the opportunity to fail. Error count and completion are independent in
this repository and should not be read together.

---

## Recorded Model Behaviour

```bash
grep -o '"Epoch[^"]*"' HW5-Regression/hw5_code.ipynb
grep -o '"formatted_gsm8k filtered[^"]*"' HW8-Catastrophic-Forgetting/hw8_code.ipynb
```

| Figure | Value | Derivation |
|---|---|---|
| HW5 training loss at early stop | 0.0279 | Printed at epoch 400 in cell 20 output, the last logged epoch before the stop |
| HW5 validation loss at early stop | 0.553 | Same output line |
| HW5 early-stop epoch | 557 | `Early stopped at epoch 557` in cell 20 output |
| HW5 configured epoch ceiling | 7,000 | `n_epochs` in cell 18 source |
| HW5 early-stop patience | 500 | `early_stop_patience` in cell 18 source |
| HW8 subset retained | 2,491 of 7,472 | Printed by cell 21 |

**Current versus configured.** The 7,000-epoch ceiling and the 557-epoch stop describe the same
run. Quoting the ceiling alone would overstate training by more than a factor of twelve; quoting
the stop alone loses the fact that the schedule was built for far longer. Both belong together.

**The loss pair is a single observation, not a curve.** Only three epoch lines survive in the
output — 0, 200, and 400 — plus the stop notice. No loss history was persisted, so nothing here
supports a convergence plot.

---

## Failure Figures

```bash
python3 - <<'PY'
import json, glob
for f in sorted(glob.glob('HW*/hw*_code.ipynb')):
    try: nb = json.load(open(f))
    except Exception: continue
    for i, c in enumerate(nb['cells']):
        for o in c.get('outputs', []):
            if o.get('output_type') == 'error':
                print(f'{f} cell{i}: {o["ename"]}: {o["evalue"][:120]}')
PY
```

| Figure | Value | Derivation |
|---|---|---|
| HW2 baselines aborted | 4 of 4 | Cells 25, 27, 29 raise `KeyboardInterrupt`; cell 31 raises `OutOfMemoryError` |
| HW2 allocation requested at failure | 934 MiB | `evalue` of the cell 31 traceback |
| HW2 device memory free at failure | 546.12 MiB | Same traceback |
| HW2 device memory held by process | 14.21 GiB | Same traceback |
| HW2 device total capacity | 14.74 GiB | Same traceback |
| HW5 inference failure | `ValueError` | Cell 22 — 210 features supplied against 212 expected |
| HW7 errors | 2 | Cell 62 `NameError`, cell 71 `KeyboardInterrupt` |

**Three of the four HW2 aborts are not failures.** `KeyboardInterrupt` is a manual stop. Only the
boss baseline hit a genuine resource limit. Counting all four as failures would overstate what the
system could not do; counting only one would understate how little of the assignment produced
output. Both readings are needed and neither alone is correct.

---

## Absences

Each row below is a figure a reader would reasonably expect this repository to contain. The
Derivation column records how the absence was established, so that it can be rechecked rather than
taken on trust.

| Missing figure | How the absence was established |
|---|---|
| HW2 answers, any baseline | No generation cell has a non-error output; `STUDENT_ID` in the export cell is the empty string and that cell has no output |
| HW5 test-set score | Inference cell terminates on `ValueError` before any prediction is written |
| HW7 SFT training metrics | Training cell holds output, but no cell prints a loss or an evaluation figure |
| HW7 DPO result | Cells 78 to 92 have empty `outputs` arrays; `student_id` is still the template value |
| HW8 GSM8K accuracy | Cell 31 has an empty `outputs` array |
| HW8 AILuminate result | Cell 34 has an empty `outputs` array |
| HW4 defence prompt | The notebook holds two cells, neither containing a prompt; its own markdown states the prompt is submitted elsewhere |
| Any course grade | No submission receipt, score file, or leaderboard capture exists anywhere in the tree |

---

## Interpretation

### "No recorded result" is a statement about this repository, not about the coursework

Every assignment here was submitted and graded. What is absent is the record, not the work. A
reader who takes the empty Evaluation table as evidence that the assignments were not completed is
reading the wrong claim, and a reader who fills the gap in the other direction — assuming the
results were good — is reading a claim that is equally unsupported. The repository is silent, and
silence is what should be reported.

### Loss numbers are the most likely figures to be misread

The two HW5 losses are the only model-behaviour numbers in this repository, which makes them the
ones a skimming reader will latch onto. A training loss of 0.0279 looks like a strong result in
isolation. Against a validation loss of 0.553 it is the opposite: it is the signature of a model
that memorised a small training set through two hundred PCA dimensions. Quoted alone, that figure
would misrepresent the work in the author's favour, which is the specific reason both appear
together everywhere they appear.

### Empty output is ambiguous between two very different histories

A cell with no output was either never executed, or executed in a session whose outputs were lost
before the notebook was saved. Colab destroys its filesystem on disconnect, and the missing cells
cluster at the end of each notebook, which is consistent with the second explanation. Consistent is
not the same as established. This document reports the ambiguity rather than resolving it, and a
rerun is the only thing that would.

### The model identifiers are the figures most worth rechecking

Three model names in the previous documentation did not match the notebooks, including a 1B model
described as 8B. Model identity is checkable in one line and was wrong in three places, so any
reader auditing this repository should start there:

```bash
grep -oE '(pretrained_model_name_or_path|base_model_name|SentenceTransformer)\S{0,60}' \
  HW*/hw*_code.ipynb
```
