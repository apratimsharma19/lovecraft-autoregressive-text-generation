# Autoregressive Text Generation on H. P. Lovecraft's Fiction

**Course:** Foundations of Generative AI (ICT4403) — Assignment 2  
**Student:** Apratim Sharma  
**Registration number:** 230911390

## 1. Objective

This project implements and compares four word-level recurrent neural network approaches for next-word prediction and autoregressive text generation: SimpleRNN, Bidirectional SimpleRNN (BiRNN), LSTM, and GRU. The models are compared using test loss, perplexity, top-1 and top-5 accuracy, training time, and generated-text metrics. Text is generated from the same three seed sequences at temperatures 0.5, 1.0, and 1.5.

## 2. Dataset

- **Dataset:** *The Complete Works of H. P. Lovecraft* (original solo fiction, 1917–1935).
- **Source:** [Internet Archive collection](https://archive.org/details/TheCompleteWorksOfHPLovecraft_201412).
- **Text file used:** `The Complete Works of H.P. Lovecraft_djvu.txt` from that collection.
- The notebook downloads the text at runtime; the raw corpus is not included in this repository.

The corpus was chosen because it is a large, stylistically distinctive collection by one author, making it useful for studying learned vocabulary, local phrasing, repetition, and style drift during generation. The README's dataset and licensing description follows the source notes in the original project files; users should consult the collection's own rights statement for reuse terms.

### Preprocessing and sequence construction

The notebook's documented pipeline removes front matter, lowercases the text, folds accents to ASCII, rejoins words hyphenated across line breaks, removes page numbers and URLs, tokenizes words while retaining apostrophes and selected punctuation, and collapses repeated punctuation. It uses a chronological 80/10/10 split of the token stream before windowing. The vocabulary contains the 6,000 most frequent training tokens; out-of-vocabulary tokens map to `<unk>`. Each input consists of 20 tokens and its target is the next token (stride 1).

### Dataset statistics

| Statistic | Value |
|---|---:|
| Total tokens (words + punctuation) | 506,674 |
| Word tokens | 461,038 |
| Unique words | 23,756 |
| Type-token ratio | 0.0515 |
| Words occurring once (hapax) | 8,944 |
| Mean sentence length | 25.2 words |
| Median sentence length | 24 words |
| Vocabulary size used by models | 6,000 |
| Train / validation / test sequences | 405,319 / 50,647 / 50,648 |
| Unigram-baseline perplexity | 393.8 |

![Dataset characteristics](outputs/dataset_characteristics.png)

## 3. Model architectures and training

Each model uses an embedding layer of size 128, a recurrent layer with 128 units, dropout of 0.3, and a dense softmax output over the vocabulary. Training uses Adam (learning rate 0.001), gradient clipping at 1.0, batch size 256, sparse categorical cross-entropy, a fixed random seed, and early stopping on validation loss (patience 2, maximum 15 epochs), as documented in the notebook.

| Model | Recurrent layer |
|---|---|
| SimpleRNN | `SimpleRNN(128)` |
| BiRNN | `Bidirectional(SimpleRNN(128))` over the known 20-token context |
| LSTM | `LSTM(128)` |
| GRU | `GRU(128)` |

The BiRNN processes the already-observed context in both directions; it does not receive future target tokens as input. Its larger parameter count is included in the comparison.

## 4. Results: held-out test set

| Model | Parameters | Test loss | Test perplexity ↓ | Top-1 accuracy | Top-5 accuracy | Epochs run | Best epoch | Training time |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| SimpleRNN | 1,574,896 | 4.9804 | 145.54 | 18.91% | 39.65% | 9 | 7 | 86.1 s |
| BiRNN | 2,375,792 | 5.0398 | 154.44 | 18.90% | 39.21% | 6 | 4 | 89.2 s |
| LSTM | 1,673,584 | 4.9675 | 143.67 | 19.94% | 40.39% | 12 | 10 | 189.0 s |
| **GRU** | **1,641,072** | **4.8698** | **130.29** | **20.48%** | **41.07%** | **10** | **8** | **154.6 s** |

![Training and validation comparison](outputs/training_comparison.png)

![Test accuracy comparison](outputs/accuracy_comparison.png)

GRU has the strongest test results among these runs: the lowest test loss and perplexity, and the highest top-1 and top-5 accuracy. Its test perplexity of 130.29 is lower than the other three models. SimpleRNN trained fastest (86.1 seconds), so it remains a reasonable option when training cost matters more than the best observed predictive performance. LSTM took the longest (189.0 seconds). BiRNN had the highest parameter count and the weakest test perplexity in this comparison.

These results come from a single run per model. Small differences should therefore be treated as observations from this experiment rather than proof of statistically significant superiority.

## 5. Temperature analysis and generated samples

The same three seed sequences were used with temperatures 0.5, 1.0, and 1.5. The complete generated samples are in [`outputs/generated_samples.md`](outputs/generated_samples.md), and the recorded metrics are in [`outputs/generation_metrics.csv`](outputs/generation_metrics.csv).

![Temperature effects](outputs/temperature_effects.png)

### Interpreting the metrics

- **Temperature 0.5:** sampling is more conservative. Across all four models, distinct-1 and distinct-2 are lower, while repeated-trigram rates and overlap with corpus 4-grams are higher. This indicates more repetition and greater reuse of familiar word sequences.
- **Temperature 1.0:** lexical diversity increases substantially and repeated-trigram rates fall close to zero in the saved samples. This is a more balanced setting in the reported metrics, although manual inspection still shows grammatical and contextual errors.
- **Temperature 1.5:** distinct-1 is about 0.90 and distinct-2 is close to 1.0, while corpus 4-gram overlap is almost zero. This indicates high novelty, but novelty alone does not mean the output is coherent; the saved examples contain awkward word combinations and semantic drift.

GRU has the highest distinct-1 and distinct-2 among the four models at temperature 0.5 and the lowest repeated-trigram rate at that temperature. At temperature 1.0, all models show very low repeated-trigram rates. At temperature 1.5, all models have high distinctness, but their samples are often less grammatical and less contextually consistent. The automatic seed-topic-retention metric is only a proxy and should be interpreted alongside manual reading of the samples.

## 6. Qualitative evaluation

The generated samples show that all models reproduce some vocabulary and atmosphere associated with the corpus, but none consistently maintains a coherent narrative over long generations. At lower temperature, familiar phrases recur more often. Higher temperatures produce more varied words but also more abrupt transitions, malformed phrases, and loss of the seed's topic. The GRU's lower test perplexity supports its next-token prediction performance, but qualitative generation remains imperfect even for the best-performing model.

For example, the temperature-0.5 samples repeatedly use phrases such as “the old ones” and “the great ones”; at higher temperatures, outputs include less natural combinations and drift away from the seed context. See the full samples in `outputs/generated_samples.md` rather than judging quality from a single excerpt.

## 7. Conclusion

**GRU is the best overall model in this experiment** based on the reported held-out test metrics: it achieves the highest top-1 accuracy (20.48%) and top-5 accuracy (41.07%), as well as the lowest test perplexity (130.29). SimpleRNN is the fastest to train, while LSTM takes the most time. Temperature has a strong effect on generated text: 0.5 encourages repeated familiar sequences, 1.0 provides a more balanced diversity/repetition profile, and 1.5 maximizes lexical diversity at the cost of coherence and contextual consistency. Consequently, GRU with temperature around 1.0 is a reasonable qualitative starting point, though the generated passages still show limitations.

## 8. Limitations

- A word-level model trained on this corpus learns local patterns better than long-range narrative structure.
- The 6,000-token vocabulary maps rare words to `<unk>`, limiting rare names and specialized vocabulary.
- Results are from a single training run per model; repeated runs would be needed to estimate variability.
- Diversity and overlap metrics do not directly measure semantic coherence. The seed-topic-retention metric is only an approximate proxy, so human inspection remains necessary.
- The temperature analysis is based on the saved generated samples and should not be treated as a general guarantee for every prompt.

## 9. Repository structure

```text
.
├── Lovecraft_Autoregressive_TextGen.ipynb
├── requirements.txt
├── README.md
└── outputs/
    ├── dataset_stats.csv
    ├── dataset_characteristics.png
    ├── model_comparison.csv
    ├── histories.json
    ├── training_comparison.png
    ├── accuracy_comparison.png
    ├── generation_metrics.csv
    ├── temperature_effects.png
    ├── generated_samples.md
    └── results_summary.md
```

## 10. How to run

### Google Colab (recommended)

1. Open Google Colab and upload `Lovecraft_Autoregressive_TextGen.ipynb`.
2. Select **Runtime → Change runtime type** and choose a GPU if available.
3. Run the notebook cells in order. The notebook downloads the dataset, preprocesses it, trains the models, evaluates them, and generates outputs.
4. Download the generated `outputs/` artifacts and compare them with the included experiment outputs.

### Local execution

Requires Python 3.10+ and internet access for the dataset download.

```bash
python -m venv .venv
# macOS/Linux:
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1
pip install -r requirements.txt
jupyter notebook Lovecraft_Autoregressive_TextGen.ipynb
```

## 11. Acknowledgements

The selected text compilation is hosted by the Internet Archive. Consult the collection page for its attribution and reuse notes. This project is for academic experimentation with next-word prediction and autoregressive generation.
