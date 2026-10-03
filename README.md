
# Model Reliability Benchmark

This is a small experiment I did to understand how a model behaves when its test inputs are changed.

## Why I did this

I wanted to see whether a model with good accuracy on normal test data would still behave similarly when the inputs were slightly different.

Instead of using a complicated model, I chose Logistic Regression so that I could focus more on understanding what happens during the reliability testing.

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

I split the data into 80% training and 20% testing using `random_state=42`.

The model was trained once and then kept fixed for all the tests.

The clean test accuracy was **93.24%**.

## What I tested

I wanted to see what happens if two of the input features are changed after the model has already been trained.

I used four controlled perturbations:

* Runtime × 0.8
* Runtime × 1.2
* Vote count × 0.8
* Vote count × 1.2

The model was **not retrained** after changing the test inputs.

## Results

| Condition       | Accuracy |
| --------------- | -------: |
| Clean           |   93.24% |
| Runtime −20%    |   93.76% |
| Runtime +20%    |   91.78% |
| Vote count −20% |   91.36% |
| Vote count +20% |   92.92% |

## What I noticed

The result I found interesting was that the accuracy did not always decrease when I changed the inputs.

For example, reducing runtime by 20% slightly increased the accuracy from 93.24% to 93.76%.

On the other hand, reducing vote count by 20% decreased the accuracy to 91.36%.

So the model did not react to every feature change in the same way. This made me realize that looking only at the clean accuracy does not tell the whole story about how a model behaves.

## What I learned

Before doing this experiment, I mostly thought of accuracy as the main way to judge whether a model was working well.

This experiment made me think more about what happens when the data given to a model changes.

I also learned that a reliability test does not have to produce a dramatic failure to be useful. Even small changes in performance can tell us something about the model's sensitivity.

## Limitations

This is a small experiment with one dataset and one model.

The perturbations are artificial changes to the feature values, so they do not necessarily represent a real-world distribution shift.

Also, the target is based on movie popularity, and some of the input features may be related to popularity. This is important when interpreting the results.

I only used accuracy in this version, so I did not evaluate confidence or calibration.

## Reproducibility

Install the required packages:

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
