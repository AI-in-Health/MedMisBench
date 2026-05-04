<p align="center">
  <img src="medmisbench_overview.png" alt="MedMisBench overview" width="100%">
</p>

<h1 align="center">MedMisBench</h1>

<p align="center">
  <strong>Benchmarking LLMs Under Misleading Medical Context</strong>
</p>

<p align="center">
  <a href="https://huggingface.co/datasets/AI4HealthResearch/MedMisBench"><img alt="Hugging Face dataset" src="https://img.shields.io/badge/Dataset-Hugging%20Face-ffcc4d"></a>
  <img alt="Task" src="https://img.shields.io/badge/Task-Adversarial%20Robustness-2f80ed">
  <img alt="Format" src="https://img.shields.io/badge/Format-JSONL%20%7C%20Notebooks-4c9f70">
  <img alt="Use" src="https://img.shields.io/badge/Use-Evaluation%20Only-6f42c1">
</p>

<p align="center">
  <img src="Medmisbench example.png" alt="MedMisBench example" width="100%">
</p>

MedMisBench evaluates whether large language models preserve the correct medical judgment when targeted misleading clinical context is introduced into a multiple-choice task. The benchmark covers five medical QA sources, five content-corruption types, and three provenance framings such as neutral false statements, patient claims, and authority-framed misinformation.

This folder contains simple, standalone notebooks for reproducing the core evaluation workflow.

## Contents

| File | Purpose |
| --- | --- |
| [`medmisbench_no_harness_eval.ipynb`](medmisbench_no_harness_eval.ipynb) | Direct model evaluation with no search or browsing tools. |
| [`medmisbench_tool_harness_eval.ipynb`](medmisbench_tool_harness_eval.ipynb) | Tool-using evaluation with web search through Serper and page reading through Jina Reader. |
| [`medmisbench_overview.png`](medmisbench_overview.png) | Overview figure used in this README. |

## Dataset

The dataset is hosted on Hugging Face:

```python
from datasets import load_dataset

rows = load_dataset("AI4HealthResearch/MedMisBench", "MEDMISQA")["MEDMISQA"]
print(rows[0])
```

Released subsets:

| Subset | Items | Role |
| --- | ---: | --- |
| `MEDMISQA` | 3,112 | Medical reasoning |
| `MEDMISMCQA` | 3,986 | Medical reasoning |
| `MEDMISXPERTQA` | 1,544 | Expert medical reasoning |
| `MEDMISJOURNEY` | 2,197 | Patient-journey evaluation |
| `MEDMISHLE` | 93 | Agentic biomedical capability |

## Evaluation Modes

Each notebook runs the same three settings:

| Mode | Description |
| --- | --- |
| `baseline` | Original question and options only. |
| `type1` | Adds one targeted misleading context statement for a sampled wrong option. |
| `type2` | Adds all option-level context statements together, including support for the correct option and misleading context for wrong options. |

## Quick Start

1. Open one of the notebooks in Colab or Jupyter.
2. Run **Step 1** to install dependencies.
3. In **Step 2**, paste the required API keys and set `PROVIDER` plus `MODEL_ID`.
4. For a smoke test, use:

```python
HF_SUBSETS = ["MEDMISQA"]
END = 1
```

5. For a full benchmark run, keep all five subsets and set:

```python
END = None
```

6. Run the notebook from top to bottom.

## Supported Models

The no-harness notebook supports:

- OpenAI models through the OpenAI API
- Gemini models through `google-genai`
- Claude models through the Anthropic API
- local Hugging Face models served through an OpenAI-compatible SGLang endpoint

The tool-harness notebook supports OpenAI, Gemini, and Claude models, plus:

- `SERPER_API_KEY` for `search_web`
- `JINA_API_KEY` for `visit_web`

## Output Layout

Each run writes a model-specific output folder:

```text
medmisbench_no_harness_outputs/
  provider__model_id/
    MEDMISQA/
      baseline/
        raw/
        evaluations/
        raw_outputs.jsonl
        evaluations.jsonl
        results.csv
        summary.json
        run.log.txt
```

The tool-harness notebook uses the same layout under `medmisbench_with_harness_outputs/` and additionally saves tool calls and visited evidence in the raw outputs.

## Reading Results

The summary cell prints values in this format:

```text
[baseline, type1, type2]
```

For example, a row such as `[78.2, 45.1, 61.7]` means:

- `78.2`: accuracy on the original benchmark questions
- `45.1`: accuracy after one targeted misleading context injection
- `61.7`: accuracy when all option-level context statements are shown together

For detailed analysis, use `evaluations.jsonl` or `results.csv`. For prompt-level debugging, use the files under `raw/` and the readable `run.log.txt`.

## Safety Notice

MedMisBench intentionally contains false and misleading medical statements. It is intended for robustness evaluation and mitigation research, not clinical guidance or supervised training without safeguards.

## Citation

If you use MedMisBench, please cite the Hugging Face dataset release and the accompanying manuscript once available.
