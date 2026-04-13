# DUAL Benchmark

**DUAL** (**D**ual **U**nlearning Ev**al**uation across Learning stages) is a benchmark and dataset introduced in the paper:

> **Anatomy of Unlearning: The Dual Impact of Fact Salience and Model Fine-Tuning**  
> Borisiuk Anna, Andrey Savchenko, Alexander Panchenko, Elena Tutubalina  
> *Accepted at ACL 2026 and ICLR 2026 Workshop*  
> [[Paper]](https://arxiv.org/abs/2602.19612)

DUAL provides a systematic framework for studying how **fact salience (popularity)** and **model training stage** (pretrained vs. supervised fine-tuned) jointly influence the effectiveness of machine unlearning in large language models (LLMs).

The benchmark comprises **28.6k Wikidata-derived question–answer pairs** annotated with fact popularity scores and organized into forget/retain splits at three scales (1%, 5%, 10%) to enable reproducible experiments.

---

## Quickstart

```bash
conda create -n dual python=3.11
conda activate dual
pip install .[lm_eval]
pip install --no-build-isolation flash-attn==2.6.3
```

### Dataset Setup

```bash
python setup_data.py --eval
# Populates saves/eval with evaluation logs and data.
# Run `python setup_data.py --help` for available dataset variants.
```

---

## Benchmark Structure

| Axis | Description |
|---|---|
| **Fact salience** | Rare vs. popular facts, grouped by Wikipedia sitelinks and LLM-based salience scores |
| **Training regime** | Same model architecture evaluated under pretrained and SFT conditions |
| **Metrics** | ROUGE-L, accuracy, and composite Δ-scores for forgetting and retention |
| **Baselines** | GradAscent, GradDiff, NPO, and additional unlearning algorithms |

---

## Scripts

**Fine-tuning**
```bash
bash dual/scripts/dual_finetune.sh
```

**Unlearning**
```bash
bash dual/scripts/dual_unlearn.sh
```

**Forgetting & retention evaluation**
```bash
bash dual/scripts/dual_eval_metrics.sh
```

**General capability benchmarks** (MMLU, HellaSwag, etc.)
```bash
bash dual/scripts/dual_llm_eval.sh
```

---

## Evaluation Example

Running DUAL evaluation on the `city_forget_rare_10` split:

```bash
model=gemma-7b-it
python src/eval.py \
  --config-name=eval.yaml \
  experiment=eval/dual/default \
  model=${model} \
  model.model_args.pretrained_model_name_or_path=saves/finetune/dual_${model}_full \
  retain_logs_path=saves/eval/dual_${model}_retain/city_fast_retain_500.json \
  task_name=RARE_FORGET_10
```

---

## Reproducing Baseline Results

```bash
bash scripts/dual_unlearn.sh
bash scripts/dual_eval.sh
```

Expected metrics and plots are documented in [`docs/repro.md`](docs/repro.md).

---

## Citation

```bibtex
@misc{anna2026anatomyunlearningdualimpact,
  title={Anatomy of Unlearning: The Dual Impact of Fact Salience and Model Fine-Tuning},
  author={Borisiuk Anna and Andrey Savchenko and Alexander Panchenko and Elena Tutubalina},
  year={2026},
  eprint={2602.19612},
  archivePrefix={arXiv},
  primaryClass={cs.CL},
  url={https://arxiv.org/abs/2602.19612}
}
```

---

## Acknowledgements

DUAL builds upon the [Open-Unlearning](https://github.com/locuslab/open-unlearning) framework and extends it with popularity-aware and training-stage-aware evaluation protocols.

---

## License

This project is licensed under the MIT License. See [`LICENSE`](LICENSE) for details.
