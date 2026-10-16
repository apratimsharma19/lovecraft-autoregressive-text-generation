## Dataset statistics

| Statistic | Value |
|---|---|
| Total tokens (words + punctuation) | 506,674 |
| Word tokens | 461,038 |
| Unique words | 23,756 |
| Type-token ratio | 0.0515 |
| Words occurring once (hapax) | 8,944 |
| Mean sentence length (words) | 25.2 |
| Median sentence length (words) | 24.0 |

Vocabulary size: 6,000 | train/val/test sequences: 405,319 / 50,647 / 50,648 | unigram-baseline perplexity: 393.8

## Model comparison (test split)

| Model | Parameters | Test loss | Test perplexity | Top-1 acc (%) | Top-5 acc (%) | Epochs run | Best epoch | Train time (s) | Sec / epoch |
|---|---|---|---|---|---|---|---|---|---|
| SimpleRNN | 1574896 | 4.9804 | 145.54 | 18.91 | 39.65 | 9 | 7 | 86.1 | 9.6 |
| BiRNN | 2375792 | 5.0398 | 154.44 | 18.9 | 39.21 | 6 | 4 | 89.2 | 14.9 |
| LSTM | 1673584 | 4.9675 | 143.67 | 19.94 | 40.39 | 12 | 10 | 189.0 | 15.8 |
| GRU | 1641072 | 4.8698 | 130.29 | 20.48 | 41.07 | 10 | 8 | 154.6 | 15.5 |

## Generated-text metrics by temperature

| Model | Temperature | distinct-1 | distinct-2 | repeated-trigram rate | corpus 4-gram overlap | seed-topic retention | punctuation rate |
|---|---|---|---|---|---|---|---|
| SimpleRNN | 0.5 | 0.517 | 0.824 | 0.058 | 0.331 | 0.028 | 0.102 |
| SimpleRNN | 1.0 | 0.737 | 0.979 | 0.002 | 0.038 | 0.023 | 0.092 |
| SimpleRNN | 1.5 | 0.905 | 1.0 | 0.0 | 0.001 | 0.037 | 0.064 |
| BiRNN | 0.5 | 0.498 | 0.799 | 0.072 | 0.311 | 0.023 | 0.1 |
| BiRNN | 1.0 | 0.728 | 0.975 | 0.002 | 0.043 | 0.034 | 0.1 |
| BiRNN | 1.5 | 0.907 | 0.999 | 0.0 | 0.001 | 0.057 | 0.06 |
| LSTM | 0.5 | 0.489 | 0.796 | 0.054 | 0.283 | 0.023 | 0.109 |
| LSTM | 1.0 | 0.748 | 0.982 | 0.0 | 0.042 | 0.035 | 0.082 |
| LSTM | 1.5 | 0.903 | 0.998 | 0.0 | 0.0 | 0.051 | 0.07 |
| GRU | 0.5 | 0.544 | 0.862 | 0.031 | 0.269 | 0.051 | 0.101 |
| GRU | 1.0 | 0.745 | 0.981 | 0.0 | 0.042 | 0.048 | 0.09 |
| GRU | 1.5 | 0.905 | 0.997 | 0.0 | 0.001 | 0.037 | 0.055 |
