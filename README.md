# PaliGemma 2: Exploring the Effect of Image Resolution 🔬

A small exploratory experiment investigating how image resolution affects **PaliGemma 2 3B Mix** on visual question answering tasks.

Inspired by the paper *PaliGemma 2: A Family of Versatile VLMs for Transfer*, this project explores a central question:

**Does higher image resolution help a vision-language model read more accurately, reason more effectively, or both?**

## Research Question

How does increasing image resolution from 224×224 to 448×448 affect PaliGemma 2 3B on OCR, visual reasoning, and mixed read-and-reason tasks?

## Experiment Setup

Two PaliGemma 2 checkpoints were compared:

- `google/paligemma2-3b-mix-224`
- `google/paligemma2-3b-mix-448`

The main experiment used three images: an English restaurant menu, a Turkish invoice, and a year-value chart.

Each image was tested with three questions covering OCR, reasoning, and mixed tasks, producing **9 questions per model condition**.

Both checkpoints received the same images and questions using the `answer en` task prefix.

The experiment was conducted in Google Colab using an NVIDIA Tesla T4 GPU.

## Test Images

Three different types of visual input were used:

1. **English restaurant menu** — small text and prices
2. **Turkish invoice** — multilingual document and table
3. **Year-value chart** — numerical values and visual comparisons

The images are available in the `images/` directory.

## Main Experiment Results

| Image | Task | Expected | 224 Answer | 448 Answer | 224 | 448 |
|---|---|---|---|---|---|---|
| Menu | OCR | 5.89 USD | 11.99 USD | 5.89 USD | ❌ | ✅ |
| Menu | Reasoning | Porterhouse | baby beef ribs | new zealand lamb | ❌ | ❌ |
| Menu | Mixed | 3 | 3 | 3 | ✅ | ✅ |
| Turkish Invoice | OCR | 003 | 14 | 003 | ❌ | ✅ |
| Turkish Invoice | Reasoning | Ana Yemekler | Üzerinde 2 tabak yemek | toplam | ❌ | ❌ |
| Turkish Invoice | Mixed | 12 | 12 | 2 | ✅ | ❌ |
| Year-value Chart | OCR | 49 | 49 | 49 | ✅ | ✅ |
| Year-value Chart | Reasoning | 2016 | 2016 | 2016 | ✅ | ✅ |
| Year-value Chart | Mixed | 4 | 4 | 4 | ✅ | ✅ |

### Accuracy Summary

| Task | 224×224 | 448×448 |
|---|---|---|
| OCR | 1/3 | 3/3 |
| Reasoning | 1/3 | 1/3 |
| Mixed | 3/3 | 2/3 |
| **Overall** | **5/9 (55.6%)** | **6/9 (66.7%)** |

Higher resolution improved OCR performance in this small experiment, but reasoning accuracy remained unchanged.

## Additional Analysis: Reading vs. Reasoning

To investigate whether incorrect answers were caused by visual reading errors or difficulties comparing information, we conducted nine additional menu-based tests with both checkpoints.

The tests covered individual price reading, simple numerical comparison, selection from the full menu, and reasoning with correct prices supplied in the question.

### Additional Results

| Task | 224×224 | 448×448 |
|---|---|---|
| Price Reading | 0/4 | 3/4 |
| Simple Comparison | 2/2 | 1/2 |
| Visual Reasoning | 0/2 | 0/2 |
| Text-Assisted Reasoning | 0/1 | 0/1 |
| **Overall** | **2/9** | **4/9** |

### Key Findings

**1. Higher resolution improved individual price reading.**

The 224 model answered all four additional price-reading questions incorrectly, while the 448 model answered three correctly.

**2. Better OCR did not guarantee correct menu selection.**

The 448 model correctly read Porterhouse at 5.69 USD and New Zealand lamb at 10.99 USD, yet incorrectly selected New Zealand lamb as the cheapest main dish.

**3. Numerical comparison performance depended on the question.**

The 224 model correctly answered both direct price-comparison questions. The 448 model correctly compared 5.69 USD with 10.99 USD but incorrectly selected 5.89 USD when comparing 5.69 USD with 5.89 USD.

**4. Supplying correct prices did not resolve the selection error.**

Both checkpoints selected New Zealand lamb instead of Porterhouse when the full list of correct prices was included in the question.

This text-assisted test still included the menu image, so it should not be interpreted as a pure text-only reasoning evaluation.

## Notable Failure Cases

### A Confident-Looking Incorrect Price

When asked for the price of Wagyu beef, the 224 model returned **11.99 USD**, although the correct price was **5.89 USD**.

The model produced a specific incorrect numerical answer without expressing uncertainty. This illustrates a potential risk when using visual question answering systems to extract prices or other precise values.

### Higher Resolution Did Not Always Help

For the Turkish invoice, the 224 model correctly answered that 12 items had been ordered, while the 448 model answered 2.

This example shows that improved OCR performance does not guarantee better results on every question.

## Repository Structure

```text
.
├── README.md
├── paligemma2_experiment.ipynb
├── results_v2.csv
├── additional_analysis_224.csv
├── additional_analysis_448.csv
├── additional_comparison.csv
└── images/
    ├── Image 01 – Menu.jpg
    ├── Image2 Turkish invoice.webp
    └── Image 3 Year-value chart.jpg
```

## Reproduce the Experiment

Open `paligemma2_experiment.ipynb` in Google Colab and select a GPU runtime.

The notebook:

1. Installs the required dependencies.
2. Authenticates with Hugging Face.
3. Loads the three test images.
4. Runs the main and additional experiments at 224×224 resolution.
5. Clears GPU memory.
6. Repeats the experiments at 448×448 resolution.
7. Exports the results as CSV files.

Access to the PaliGemma 2 checkpoints on Hugging Face may require accepting their model terms.

Both checkpoints use the `answer en` task prefix.

## Files

- `paligemma2_experiment.ipynb` — full Colab experiment
- `results_v2.csv` — main experiment results using the corrected prompt
- `additional_analysis_224.csv` — additional 224 model outputs
- `additional_analysis_448.csv` — additional 448 model outputs
- `additional_comparison.csv` — side-by-side additional analysis
- `images/` — experiment images

## Limitations

This is a small exploratory experiment, not a formal benchmark. The main experiment uses three images and nine questions per checkpoint, followed by nine additional menu questions per checkpoint.

Some questions labelled as reasoning also depend on reading visual information. Consequently, incorrect answers cannot always be attributed exclusively to reasoning failures.

The additional experiments help investigate this distinction, but they do not fully isolate visual perception from reasoning. In particular, the text-assisted test still includes the image and may be affected by prompt length and wording.

The study evaluates only the 3B Mix checkpoints at 224×224 and 448×448. The results should not be generalized to other PaliGemma 2 model sizes, checkpoints, or datasets.

The main takeaway is that **higher resolution improved fine-detail reading in this experiment, but did not consistently improve more complex question answering.**

## Reference

*PaliGemma 2: A Family of Versatile VLMs for Transfer*  
Google Research, 2024  
https://arxiv.org/abs/2412.03555
