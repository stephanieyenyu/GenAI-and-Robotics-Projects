# Known Issues

Every issue found while auditing this repository is recorded here, whether or not it was
subsequently fixed. Entries are classified by what was actually established: A entries were
checked and found not to be defects, B entries could not be verified from what the repository
holds, C entries are defects, and D entries are documentation that does not match the code.
Resolved entries are not deleted — the classification is the point, not the count.

Snapshot 2026-09-06 · 6 project folders · 6 notebooks · 6 project READMEs plus 1 root README

## Summary

| ID | Issue | Class | Disposition |
|---|---|---|---|
| A-1 | `NameError` in HW7 cell 62 follows an intended session restart | Not a defect | No action |
| A-2 | Datasets are fetched from an external course host | Design limitation | Accepted, not fixed |
| B-1 | The institution attributed to the course is unverified | Unverified | Open |
| B-2 | HW5 feature list may not match what was intended | Unverified | Open |
| C-1 | Hugging Face access token committed in HW8 — revoked 2026-09-06 | Security | Fix recommended |
| C-2 | HW6 notebook contains no content | Defect | Fix recommended |
| C-3 | HW5 inference fails on a feature-count mismatch | Defect | Open |
| C-4 | HW7 DPO stage was never executed | Defect | Open |
| C-5 | HW8 evaluation cells produced no output | Defect | Open |
| C-6 | HW2 baselines all terminated without producing answers | Defect | Open |
| C-7 | HW8 protected block was modified and its message is now false | Defect | Fix recommended |
| C-8 | Student identifier committed in two notebooks | Security | Fix recommended |
| D-1 | Root README names models that no notebook uses | Documentation | Fix recommended |
| D-2 | HW2 README credits LangChain and a vector database; neither is used | Documentation | Fix recommended |
| D-3 | HW4 README describes work the repository does not contain | Documentation | Fix recommended |
| D-4 | Root README links a project folder that does not exist | Documentation | Fix recommended |
| D-5 | HW5 README omits the embedding and PCA stage | Documentation | Fix recommended |
| D-6 | Two project folders are absent from the root README table | Documentation | Fix recommended |
| D-7 | Repository name promises robotics content the repository does not hold | Documentation | Open |

---

### A-1　`NameError` in HW7 cell 62 follows an intended session restart

**Symptom.** `hw7_code.ipynb` cell 62 raises `NameError: name 'model' is not defined` while
executing `del model, tokenizer`.

**Verified.** The markdown immediately preceding cell 71 instructs the reader to restart the
Colab session after supervised fine-tuning before importing packages for the next stage. A
restarted kernel has no `model` binding, so the deletion fails by design rather than by fault.

**Assessment.** Not a defect. Recorded so that a reader who opens the notebook and sees a red
error cell does not treat it as evidence that the fine-tuning stage failed.

---

### A-2　Datasets are fetched from an external course host

**Observation.** HW2 and HW8 download their datasets from `www.csie.ntu.edu.tw/~b10902031/`;
HW5 clones `murphy-cthsu/GENAI-ML-2025-HW5-Data`; HW7 clones
`jaxon3062/GenAI-2025-HW7-Dataset`.

**Assessment.** Course-provided data, correctly not vendored into this repository. It is
recorded because it bounds reproducibility: none of these hosts is under the author's control,
and when a course account is retired the notebooks stop being runnable end to end.

> Accepted, not fixed. Mirroring course datasets into this repository would be a redistribution
> decision, not a maintenance one.

---

### B-1　The institution attributed to the course is unverified

**Symptom.** The root README attributes the course to NTU. The author is enrolled at NTHU.

**Observation.** HW8 credits the ML2025 HW6 Colab sample code, and HW2 and HW8 both draw data
from an NTU host, which is consistent with NTU course materials. That establishes the origin of
the materials, not the institution at which the course was taken or credited.

**Impact.** An institution named incorrectly in a portfolio README is a factual error about a
credential, which is the class of error a reader is least willing to excuse.

**Check.** Only the author can settle this from her enrolment record.

---

### B-2　HW5 feature list may not match what was intended

**Symptom.** `hw5_code.ipynb` cell 12 declares twelve numeric features, two of which — `owners`
and `average_playtime` — carry the inline comment that they are assumed to be the missing ones.
A filter on the following line drops any name absent from the dataframe.

**Observation.** The printed result lists nine features. Three of the twelve were silently
removed, including both of the assumed ones. Two derived features, `positive_ratio` and
`price_per_hour`, are computed but do not appear in the printed list either.

**Hypothesis.** The intended feature set was never the one that trained. Whether the omission
changed the result is not determinable without a rerun.

**Check.** Rerun cell 12 against `train.csv` and compare the printed list to the declared list.

---

### C-1　Hugging Face access token committed in HW8

**Location.** `HW8-Catastrophic-Forgetting/hw8_code.ipynb`, cell 9 — the `hf auth login`
invocation carries a literal token in its `--token` argument.

**Observation.** The repository is public. The cell as written failed at execution because the
command name is mistyped, but the failure is irrelevant to the exposure: the token string is in
the committed file and in the history of all 31 commits.

**Impact.** The token grants whatever scope it was issued with against the author's Hugging Face
account.

**Verified.** The token was revoked on 2026-09-06; the account's token list is empty as of that
date. The committed string no longer authenticates against anything. Hugging Face scans public
sources for leaked tokens and removes them, so it is not established whether the revocation was
manual or automatic, and the distinction does not change the outcome.

**Severity.** Was the highest in this document and the only time-sensitive entry. Now reduced to
code hygiene.

**Current exposure.** None from the credential. The literal remains in the working tree and in
the history of all 31 commits, where a reader reviewing the notebook sees a hardcoded token — a
practice that reflects on the author whether or not the value is live.

**Fix.** Replace the cell with a `getpass` prompt or a Colab secret before this notebook is
recommitted. Rewriting history is not worth it for a dead credential.

---

### C-2　HW6 notebook contains no content

**Symptom.** `HW6-CNN-Classification/hw6_code.ipynb` is two bytes — a carriage return and a
newline. It is not parseable as a notebook.

**Consequence.** `HW6-CNN-Classification/README.md` describes a CNN with convolution, pooling
and batch-normalisation layers, transfer learning from ResNet, rotation and flip augmentation,
and optimiser and scheduler experiments. None of it has code behind it in this repository.

**Current exposure.** The root README lists this project in its table, so a reader is directed
to a described project and arrives at an empty file.

**Fix.** Upload the notebook, or delete the folder together with its row in the root README.
Leaving a described project with no implementation is the worse of the two outcomes.

**Check.** `wc -c HW6-CNN-Classification/hw6_code.ipynb`

---

### C-3　HW5 inference fails on a feature-count mismatch

**Symptom.** `hw5_code.ipynb` cell 22 terminates with
`ValueError: X has 210 features, but StandardScaler is expecting 212 features as input.`

**Hypothesis.** The feature filter is applied a second time against `test.csv`, and the numeric
columns surviving that filter differ from the ones the scaler was fitted on. The training and
inference feature construction paths are not shared.

**Consequence.** No test-set prediction exists. The only recorded numbers are a training loss of
0.0279 and a validation loss of 0.553 at early stop, epoch 557. That gap is a twentyfold
divergence and is itself the more interesting finding, but neither figure is a test result.

**Fix direction.** Build the feature matrix once, in a function called by both paths, so that the
column set cannot diverge between fit and transform.

---

### C-4　HW7 DPO stage was never executed

**Symptom.** `hw7_code.ipynb` cells 78 through 92 — the Weights and Biases setup, the
`HW7DPOTrainer` subclass, the trainer configuration, adapter saving, merged-model saving and
aligned-model inference — carry no outputs.

**Observation.** The supervised fine-tuning stage did run; cell 44 holds 21 output entries.
`student_id` in cell 92 is still the template placeholder.

**Consequence.** The root README describes this project as implementing the full training
pipeline including reinforcement learning from human feedback. Only the supervised half is
evidenced. The preference-optimisation half is present as code and absent as a result.

---

### C-5　HW8 evaluation cells produced no output

**Symptom.** `hw8_code.ipynb` cell 31 evaluates 100 GSM8K public test items and cell 34
evaluates 80 AILuminate prompts. Neither cell carries any output.

**Consequence.** No accuracy figure exists for either the mathematics task or the safety task,
before or after fine-tuning. The project's stated subject is the trade-off between the two, and
the trade-off is exactly what was not recorded. The sub-README's claim to have visualised a
trade-off curve has nothing behind it.

**Observation.** Fine-tuning itself did run; cell 23 holds six output entries. The gap is
confined to evaluation.

---

### C-6　HW2 baselines all terminated without producing answers

**Symptom.** All four retrieval baselines in `hw2_code.ipynb` ended abnormally. Cells 25, 27 and
29 raise `KeyboardInterrupt`; cell 31 raises `torch.OutOfMemoryError`, having attempted to
allocate 934 MiB against 546 MiB free on a 14.74 GiB device.

**Observation.** The retrieval infrastructure did build — cell 16 encoded all chunks and cell 20
loaded the quantised model. What did not complete is generation.

**Consequence.** No answers were produced by any of the four configurations, so no comparison
between them exists. `STUDENT_ID` in the export cell is an empty string and the cell has no
output, so no submission file was written from this run either.

---

### C-7　HW8 protected block was modified and its message is now false

**Symptom.** `hw8_code.ipynb` cell 21 is headed with an instruction not to modify it. The sort
that selects the retained subset uses `reverse=False`, annotated in the file as a modification to
keep the shortest third rather than the longest.

**Consequence.** The printed confirmation reads that 2,491 of 7,472 longest examples were kept.
The examples kept were the shortest. Anyone reading the notebook output takes the opposite of
what happened, and length correlates with reasoning-chain depth on GSM8K, so the substitution is
not neutral with respect to the result.

**Fix.** Either restore `reverse=True`, or keep the change and correct the message to state what
the block now does. The present state — a modified protected block whose output contradicts it —
is the one configuration that should not persist.

---

### C-8　Student identifier committed in two notebooks

**Location.** `hw8_code.ipynb` cell 36 assigns a student identifier; `hw2_code.ipynb` cell 33
holds an empty identifier field with a real-format example in its comment.

**Impact.** Low relative to C-1, and a student number is not a credential. It is recorded because
it is personal data in a public repository serving a graduate application, and removing it costs
nothing.

**Fix.** Replace with a placeholder or read it from an environment variable.

---

### D-1　Root README names models that no notebook uses

**Symptom.** The root README credits Llama-3 to HW7 and to HW4. The sub-READMEs credit
Llama-3-8B to HW7 and to HW8, and Llama-3-8B-Instruct to HW2.

**Verified.** HW7 cell 22 loads `jaxon3062/gemma-3-4b-pt-chat`. HW2 cell 20 loads
`unsloth/gemma-3-4b-it`. HW8 cell 14 loads `unsloth/Llama-3.2-1B-Instruct`. Two of the three are
Gemma rather than Llama, and the third is a 1B model documented as an 8B model.

**Impact.** A 1B model documented as 8B overstates the compute involved by a factor of eight, and
it is checkable in one line by anyone who opens the notebook.

**Check.** `grep -rn "from_pretrained\|base_model_name" --include=*.ipynb .`

---

### D-2　HW2 README credits LangChain and a vector database; neither is used

**Symptom.** The root README lists LangChain and a vector database as the HW2 stack. The
sub-README qualifies LangChain as concepts only and names cosine similarity retrieval.

**Verified.** The notebook imports neither. Retrieval is BM25 over jieba tokens using
`rank_bm25`, plus dense retrieval with `intfloat/multilingual-e5-large`, with the two rankings
combined by reciprocal rank fusion. Chunking is fixed-width at 850 characters with a stride of
500.

**Assessment.** The implementation is a hybrid sparse-dense retriever with rank fusion, which is
a stronger thing to have built than what the README claims. The documentation understates the
work and misnames the tooling at the same time.

---

### D-3　HW4 README describes work the repository does not contain

**Symptom.** `HW4-LLM-Defense/README.md` claims attack simulation, defence implementation, and
evaluation of a defence success rate against an adversarial prompt dataset.

**Verified.** `hw4_code.ipynb` holds two cells. The first is course-provided instructions; the
second is a course-provided widget that counts the tokens in a defence prompt entered into a text
box. There is no attack set, no defence prompt, and no evaluation in the file. The notebook's own
markdown states that the defence prompt is submitted to a separate judging system.

**Consequence.** The defence prompt is the entire deliverable of this assignment and it is not in
this repository. As it stands the folder documents a submission the reader cannot see.

**Fix.** Commit the defence prompt itself and whatever score it received, or state plainly in the
README that the artefact lives in the course judging system and describe the approach instead of
claiming an evaluation.

---

### D-4　Root README links a project folder that does not exist

**Symptom.** The Featured Projects table lists a diffusion model and audio generation project
linking to `HW9-Diffusion-Model`.

**Verified.** No such folder exists at any commit reachable from `main`. The link resolves to a
404. The root README's skills section separately claims diffusion models, and that claim rests on
this absent folder.

**Check.** `git ls-tree -d --name-only HEAD`

---

### D-5　HW5 README omits the embedding and PCA stage

**Symptom.** The sub-README describes a multi-layer perceptron over preprocessed tabular
features, listing PyTorch, Pandas, Scikit-Learn and Matplotlib.

**Verified.** The notebook encodes two free-text columns, `reviews` and `short_description`, with
a sentence-transformer, reduces each with PCA, and concatenates the reduced vectors onto the
numeric features. The model itself carries a projection shortcut between its second and third
layers, which the README also does not mention.

**Assessment.** As with D-2, the documentation describes something simpler than what was built.
The task is also unnamed: the target is the Metacritic score of Steam titles, not an unspecified
continuous variable.

---

### D-6　Two project folders are absent from the root README table

**Symptom.** The table lists five projects. The repository holds six folders. HW5 and HW8 appear
in neither the table nor anywhere else in the root README.

**Consequence.** HW8 is the only project here framed as an investigation rather than an
implementation, and it is the one a reader is never pointed at.

---

### D-7　Repository name promises robotics content the repository does not hold

**Symptom.** The repository is named `GenAI-and-Robotics-Projects`. All six folders are
generative AI and machine learning coursework. No robotics code is present.

**Observation.** The author's robotics work is in `stephanieyenyu/LLM_RobotArm` and
`stephanieyenyu/AMR_System`, both public and both outside this repository.

**Fix direction.** Either rename the repository, which breaks any link already circulated, or
keep the name and link the two robotics repositories from the root README. The second is
reversible and the first is not.
