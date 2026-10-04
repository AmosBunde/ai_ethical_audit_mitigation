# Bias Evaluation and Ethical Audit

## ✅ Project Results (completed 2026-10-04)

The full audit was executed in [`starter/bias_evaluation_starter.ipynb`](starter/bias_evaluation_starter.ipynb) (seed 42, CPU). Deliverables:

| Deliverable | Location |
| :---- | :---- |
| Executed audit notebook (predefined + custom prompts, sensitivity, counterfactual, lexicon analysis) | `starter/bias_evaluation_starter.ipynb` |
| Ethical Audit Report (completed) | `starter/ethical-audit-report-starter.md` |
| Comprehensive Mitigation Plan (completed, incl. EU AI Act / NIST AI RMF mapping) | `starter/comprehensive-mitigation-plan-starter.md` |
| Ethics Committee Presentation (11 slides) | `starter/ethics-committee-presentation.pptx` |
| Exported explainability tables (CSV) | `outputs/` |
| Red-teaming exercise (adversarial prompting + filter test) | `starter/bias_evaluation_starter.ipynb` (final section), `outputs/redteam_analysis.csv` |
| Data repair + retraining with before/after bias scores | `starter/data_repair_and_retraining.ipynb`, `outputs/retraining_bias_comparison.csv` |
| Interactive Streamlit bias dashboard for the committee | `dashboard/app.py` (see `dashboard/README.md`) |

### Headline findings

- **70% of neutral prompts (7/10) acquired gendered language** with no gender cue in the input — leadership/engineering prompts defaulted male ("He is a visionary leader…"), care/support prompts defaulted female ("We're hiring an emotional woman…").
- **Counterfactual gender swaps rewrote the outputs**: Jaccard similarity 0.22–0.29 across all six pairs; adding "female" to a CIO prompt flipped the register from strategic leadership to "driven, nurturing and attentive".
- **Explicit instructions are overridden**: prompts for a *male* Administrative Assistant and a *male* Nurse both returned "an experienced/inclusive woman" — the learned stereotype beats the user's request.
- **Artifact risks**: fabricated people (Ms. Huang, Nicole), fabricated email addresses, and real public figures named in generated hiring copy.
- **Verdict**: unsafe for any people-affecting use; educational use only, per the model card. The mitigation plan pairs data repair + retraining (acceptance gate: neutral gendered rate ≤5%) with output filters, human-in-the-loop review, and a CI regression gate.

### Stand-out results

- **Red-teaming**: the model answered all 6 adversarial prompts (no refusal behavior); the proposed output filter intercepted 5/6 responses (1 BLOCK, 4 REVIEW) and the exercise surfaced a plural-term lexicon gap, now fixed in the filter.
- **Data repair + retraining (two iterations)**: iteration 1 (gender-swap augmentation) balanced the *direction* of bias (counterfactual similarity 0.245 → 0.294) but not its volume — the ≤5% gate was not met. Iteration 2 (fully neutralized training data, `starter/data_repair_and_retraining_iter2.ipynb`) **passes the gate: 0/10 (0%) neutral prompts gendered** vs 10/10 at baseline; mean gendered terms 3.2 → 0.0; counterfactual similarity 0.298.
- **Interactive dashboard**: Streamlit app for the committee with live lexicon analysis and filter verdicts (`dashboard/`).
- **Regulatory mapping**: EU AI Act high-risk obligations and NIST AI RMF functions mapped in the mitigation plan.

---

# Jupyter Notebook Instructions 

In this exercise, you will review a fine-tuned language model, load it into a Jupyter Notebook, evaluate its outputs, and apply explainability techniques to assess potential bias. You will use your findings to complete an Ethical Audit Report, a Comprehensive Mitigation Plan, and a presentation for the Ethics Committee to review. 

### **Step 1: Review the Model Card**

Before downloading the model, take time to understand what you will be working with.

1. Review the **model card**, paying close attention to:  
   * The base model used  
   * How the model was fine-tuned  
   * The purpose and intended use of the model  
   * Known limitations and ethical considerations  
2. This context will help you better interpret the model’s behavior during testing and analysis.

### **Step 2: Set Up Your Environment**

Navigate to the **Udacity Workspace** or another Jupyter-compatible environment.

1. Launch your Jupyter environment.  
2. Open **JupyterLab**.

### **Step 3: Download the Starter Notebook**

1. Download the provided notebook file from the course resources.  
2. Upload the notebook to your Jupyter environment.  
3. Open the notebook and review its structure before running any cells.

### **Step 4: Install Required Libraries**

From the terminal, install the required dependencies:

pip install -r requirements.txt

Restart the kernel if prompted.

### **Step 5: Load the Model**

1. Run the provided code cell to load the model and tokenizer.  
2. Verify that the model loads without errors.

### **Step 6: Generate Model Outputs Using Predefined Prompts**

1. Run the cells that generate outputs using predefined prompts.  
2. Review the generated text carefully.  
3. Observe how the model responds to neutral, explicit, and role-based prompts.

### **Step 7: Create Your Own Test Prompts**

1. Modify or add new prompts in the notebook.  
2. Generate additional outputs.  
3. Use these prompts to explore how small changes affect model behavior.

### **Step 8: Apply Explainability Techniques**

Run the notebook sections that apply explainability techniques, including:

* Prompt sensitivity testing  
* Counterfactual prompt comparisons  
* Lexicon-based bias signal analysis 

Review the resulting tables and outputs.

### **Step 9: Analyze Results and Identify Patterns**

1. Examine how often gendered language appears in neutral prompts.  
2. Compare outputs across gender swaps.  
3. Review the explainability summary tables produced by the notebook.  
4. Note recurring patterns, shifts in language, or unexpected artifacts.

### **Step 10: Produce the Ethical Audit Report**

Using your testing results and explainability outputs:

1. Complete the **Ethical Audit Report**.  
2. Include relevant tables directly from the notebook.  
3. Clearly document findings, risks, and limitations.

# Ethical Audit Report 

The Ethical Audit Report documents what you observed about the model’s behavior, risks, and limitations based on systematic testing and explainability analysis.

This report focuses on evidence rather than solutions. Submit a completed Ethical Audit Report with:

* Explainability tables embedded  
* Clear references to notebook outputs  
* Evidence-based observations

# Comprehensive Mitigation Plan

The Comprehensive Mitigation Plan outlines how the risks identified in the Ethical Audit Report can be addressed prior to deployment.

This document focuses on actions and safeguards rather than analysis. Submit a completed Comprehensive Mitigation Plan that:

* Directly maps to risks identified in the Ethical Audit Report  
* Focuses on actionable, realistic safeguards

# Ethics Committee Presentation

The Ethics Committee Presentation communicates your findings and recommendations to non-technical stakeholders responsible for oversight and approval.

This presentation should be clear, concise, and evidence-driven. Submit a slide deck suitable for an Ethics Committee that:

* Uses plain language  
* Includes evidence from your analysis  
* Clearly connects risks to mitigation actions

# Submission Checklist

Before submitting, confirm that you have:

* Reviewed the model card
* Successfully loaded the model into your Jupyter Notebook  
* Generated outputs using predefined and custom prompts  
* Conducted prompt sensitivity testing and counterfactual analysis  
* Applied lexicon-based explainability techniques to analyze bias signals  
* Generated explainability tables from notebook outputs  
* Completed the Ethical Audit Report using evidence from the explainability tables  
* Completed the Comprehensive Mitigation Plan addressing identified risks  
* Created the Ethics Committee Presentation summarizing findings and mitigations
