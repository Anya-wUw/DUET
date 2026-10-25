<p align="center">
  <img src="DUET_cover.png" alt="DUET cover" width="800">
</p>

**DUET** is a benchmark for machine unlearning in LLMs. It comes from the paper:

> **Anatomy of Unlearning: The Dual Impact of Fact Salience and Model Fine-Tuning**  
> Borisiuk Anna, Andrey Savchenko, Alexander Panchenko, Elena Tutubalina  
> *ACL 2026 and ICLR 2026 Workshop*  
> [[Paper]](https://arxiv.org/abs/2602.19612) | [[Hugging Face collection]](https://huggingface.co/collections/SwetieePawsss/duet-anatomy-of-unlearning)

DUET tests how two factors affect unlearning:

- **Fact popularity.** Are popular facts harder to forget than rare ones?
- **Training stage.** Does unlearning work differently on a pretrained model and on an SFT model?

The dataset has **28.6k question-answer pairs** built from Wikidata. Each fact has a popularity score. Forget and retain splits come in three sizes: 1%, 5% and 10%.

Data and models are available in the [Hugging Face collection](https://huggingface.co/collections/SwetieePawsss/duet-anatomy-of-unlearning).

---

## Installation

```bash
conda create -n dual python=3.11
conda activate dual
pip install .[lm_eval]
pip install --no-build-isolation flash-attn==2.6.3
```

### Download data

```bash
python setup_data.py --eval
```

This fills `saves/eval` with data and evaluation logs. Run `python setup_data.py --help` to see all options.

---

## What the benchmark covers

| Axis | Description |
|---|---|
| **Fact popularity** | Rare vs. popular facts, based on Wikipedia sitelinks and LLM-based scores |
| **Training stage** | The same model evaluated as pretrained and after SFT |
| **Metrics** | ROUGE-L, accuracy, and Δ-scores for forgetting and retention |
| **Baselines** | GradAscent, GradDiff, NPO and other unlearning methods |

---

## Scripts

| Step | Command |
|---|---|
| Fine-tuning | `bash dual/scripts/dual_finetune.sh` |
| Unlearning | `bash dual/scripts/dual_unlearn.sh` |
| Forgetting and retention metrics | `bash dual/scripts/dual_eval_metrics.sh` |
| General benchmarks (MMLU, HellaSwag, etc.) | `bash dual/scripts/dual_llm_eval.sh` |

---

## Example: evaluation on `city_forget_rare_10`

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

## Reproducing the baselines

```bash
bash dual/scripts/dual_unlearn.sh
bash dual/scripts/dual_eval_metrics.sh
```

Expected metrics and plots are in [`docs/repro.md`](docs/repro.md).

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

DUET is built on [Open-Unlearning](https://github.com/locuslab/open-unlearning). It adds evaluation by fact popularity and by training stage.

## License

MIT. See [`LICENSE`](LICENSE).
