# Interactive Bias Evaluation Dashboard

A Streamlit app that lets the Ethics Committee enter their own prompts and see the audit's
lexicon-based bias analysis in real time — including the mitigation plan's output-filter
verdict (BLOCK / REVIEW / PASS) and, once the retraining notebook has been run, a
side-by-side comparison of the biased and retrained models.

## Run it

```bash
cd project
pip install -r requirements.txt streamlit
streamlit run dashboard/app.py
```

The app loads the local model from `project/model/` (no internet required). If
`project/model_retrained/` exists (produced by `starter/data_repair_and_retraining.ipynb`),
a model selector and side-by-side comparison mode appear automatically.

## What reviewers see

- The raw model output for any prompt they type
- A traffic-light verdict mirroring the mitigation plan's output-side control
- Lexicon counts (male/female terms, leadership vs support traits, fabricated contact flags)
  identical to the audit notebook, so dashboard findings are directly comparable to the
  Ethical Audit Report tables
