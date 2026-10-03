# Model-reliability-benchmark
# Model Reliability Benchmark

This project is a small experiment to see how a simple machine learning model behaves when some of its test inputs are changed.

## Dataset

I used the **TMDB 5000 Movies dataset**.

The features I used are:

* Budget
* Revenue
* Runtime
* Vote average
* Vote count

I created a binary target using the median popularity of the movies.

## Model

I used **Logistic Regression** as the baseline model.

The data was split into:

* 80% training data
* 20% test data
* `random_state=42`

The model was trained once on the training data.

The clean test accuracy was **93.24%**.

## Perturbations

After training the model, I changed two features in the test set and checked the accuracy again.

I used four perturbations:

* Runtime decreased by 20%
* Runtime increased by 20%
* Vote count decreased by 20%
* Vote count increased by 20%

The model was not retrained after making these changes.

## Results

| Condition       | Accuracy |
| --------------- | -------: |
| Clean           |   93.24% |
| Runtime −20%    |   93.76% |
| Runtime +20%    |   91.78% |
| Vote count −20% |   91.36% |
| Vote count +20% |   92.92% |

The model stayed above 91% accuracy for all four perturbations. However, the accuracy changed depending on which feature was modified.

The biggest decrease was when **vote count was reduced by 20%**, where accuracy went from 93.24% to 91.36%.

## What I learned

The clean accuracy by itself does not show how a model behaves when the inputs change.

In this experiment, some changes had very little effect, while others caused a noticeable drop in accuracy. The effect was also not exactly the same when a feature was increased versus decreased.

## Limitations

This is a small experiment using one dataset and one model.

The perturbations are artificial feature changes, so they may not represent a real-world distribution shift.

Also, the target is based on movie popularity, and some of the input features may be related to popularity. This is something that should be considered when interpreting the results.

The benchmark only uses accuracy, so it does not evaluate other aspects such as model calibration or confidence.

## How to Run

Install the required Python packages:

```bash
pip install -r requirements.txt
```

Then open the notebook and run the cells from beginning to end.

The dataset should be placed in the `data/` folder.

## Project Structure

```text
model-reliability-benchmark/
│
├── data/
│   └── tmdb_5000_movies.csv
│
├── notebook/
│   └── reliability_benchmark.ipynb
│
├── requirements.txt
└── README.md
```
