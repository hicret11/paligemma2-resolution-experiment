# PaliGemma 2: Exploring the Effect of Image Resolution 🔬

A small exploratory experiment investigating how image resolution affects the performance of **PaliGemma 2 3B** on visual question answering tasks.

This experiment was inspired by the resolution-scaling analysis presented in the **PaliGemma 2** paper.

## Research Question

**How does increasing image resolution from 224×224 to 448×448 affect PaliGemma 2 3B on OCR, visual reasoning, and mixed read-and-reason tasks?**

## Experiment Setup

Two PaliGemma 2 checkpoints were compared:

- `google/paligemma2-3b-mix-224`
- `google/paligemma2-3b-mix-448`

The experiment used:

- **3 images**
- **3 task types:** OCR, reasoning, and mixed
- **3 questions per image**
- **9 questions per model condition**
- The same images, prompts, and expected answers for both resolutions

The experiment was run in **Google Colab using an NVIDIA Tesla T4 GPU**.

## Test Images

Three different types of visual input were used:

1. **English restaurant menu** — small text and prices
2. **Turkish invoice** — multilingual document and table
3. **Year-value chart** — numerical values and visual comparisons

The images used in the experiment are available in the [`images/`](images/) directory.

## Results

| Image | Task | Expected | 224 Answer | 448 Answer | 224 | 448 |
|---|---|---|---|---|---|---|
| Menu | OCR | $5.89 | $11.99 | $5.89 | ❌ | ✅ |
| Menu | Reasoning | Porterhouse ($5.69) | baby beef ribs | new zealand lamb | ❌ | ❌ |
| Menu | Mixed | 3 | 3 | 3 | ✅ | ✅ |
| Turkish Invoice | OCR | 003 | 14 | 003 | ❌ | ✅ |
| Turkish Invoice | Reasoning | Ana Yemekler | Main coure | toplam | ❌ | ❌ |
| Turkish Invoice | Mixed | 12 | 12 | 10 | ✅ | ❌ |
| Year-value Chart | OCR | 49 | 60 | 49 | ❌ | ✅ |
| Year-value Chart | Reasoning | 2016 | 2016 | 2016 | ✅ | ✅ |
| Year-value Chart | Mixed | 4 | 4 | 4 | ✅ | ✅ |

### Overall Strict Accuracy

- **224×224:** 4/9 — **44.4%**
- **448×448:** 6/9 — **66.7%**

### OCR Accuracy

- **224×224:** 0/3
- **448×448:** 3/3

The clearest change appeared in the OCR tasks. At 224×224, the model answered all three direct-reading questions incorrectly. At 448×448, it answered all three correctly.

However, higher resolution did **not** consistently improve reasoning or mixed tasks. For example, the 448×448 model still failed to identify the cheapest menu item and regressed on the Turkish invoice counting question.

This suggests that, in this small experiment, higher resolution helped PaliGemma 2 read fine visual details more accurately, but better visual input did not automatically produce better reasoning.

## Repository Structure

```text
.
├── README.md
├── paligemma2_experiment.ipynb
├── results.csv
└── images/
    ├── Image 01 – Menu.jpg
    ├── Image2 Turkish invoice.webp
    └── Image 3 Year-value chart.jpg
```

## Reproduce the Experiment

Open `paligemma2_experiment.ipynb` in Google Colab and use a GPU runtime.

The notebook:

1. installs the required libraries,
2. authenticates with Hugging Face,
3. loads the test images,
4. runs the same nine questions with PaliGemma 2 3B at 224×224,
5. clears GPU memory,
6. repeats the experiment at 448×448,
7. reports the results.

Access to the gated PaliGemma 2 checkpoints on Hugging Face is required.

## Limitations

This is a **small exploratory experiment**, not a benchmark.

Only three images and nine questions were tested. Therefore, the results should not be interpreted as evidence that 448×448 resolution always outperforms 224×224 resolution.

The experiment instead provides a small practical example of the resolution-related behavior explored more systematically in the PaliGemma 2 paper.

## Files

- `paligemma2_experiment.ipynb` — full Colab experiment
- `results.csv` — recorded experiment results
- `images/` — test images used in both conditions

## Reference

**PaliGemma 2: A Family of Versatile VLMs for Transfer**  
Google Research, 2024  
arXiv: 2412.03555

