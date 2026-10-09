
# Model Reliability Benchmark

This is a small experiment I did to understand how a model behaves when its test inputs are changed.

The goal was to see whether a model with good accuracy on normal test data would still behave similarly when some of its input features were changed.

Instead of using a complicated model, I chose Logistic Regression so that I could focus more on understanding what happens during reliability testing.

## Dataset

I used the **TMDB 5000 Movies dataset**.

The features I used are:

* `budget`
* `revenue`
* `runtime`
* `vote_average`
* `vote_count`

### Dataset acquisition and provenance

The notebook originally referenced a local Windows file path. The original CSV is not included in this repository snapshot.

The exact acquisition source and SHA-256 hash of the original CSV have not been verified from the available artifact. They are therefore left undocumented rather than guessed. The reported notebook outputs are retained as recorded; the original dataset's identity and independent reproducibility have not been established by this documentation update.

To run the notebook, obtain the same original `tmdb_5000_movies.csv` file used in the experiment, if it can be recovered, and place it at:

`data/tmdb_5000_movies.csv`

Create the `data` folder in the repository if needed. If the original file cannot be recovered, a replacement dataset should not be assumed to reproduce the recorded results.

### Classification target

I created a binary classification target using the median popularity of the movies.

The median `popularity` threshold is calculated over the full retained dataset, after removing rows with missing runtime values and before the train/test split. Movies with popularity greater than or equal to this threshold were assigned `1`, and movies below it were assigned `0`.

This defines descriptive popularity groups using the full retained sample. The threshold is not calculated from the training split alone, and this experiment does not demonstrate future-popularity prediction.

## Data preprocessing

There were two missing values in the `runtime` column, so those rows were removed before the final train/test split.

I did not use a learned preprocessing step such as scaling or imputation.

After removing the rows with missing runtime values, I recreated the train/test split.

## Model

I used **Logistic Regression** as the baseline model.

I split the data into 80% training and 20% testing using `random_state=42`.

The model was trained once and then kept fixed for all the perturbation tests.

The input features include revenue and voting-related variables. These may contain information associated with the popularity-based target, so the results should not be interpreted as evidence of predicting future popularity.

The clean test accuracy was **92.51%**.

## Perturbation testing

I wanted to see what happens when some of the test input features are changed after the model has already been trained.

I changed selected feature values by multiplying them by fixed factors.

For example:

* `× 0.80` means the feature was reduced by 20%.
* `× 1.20` means the feature was increased by 20%.
* `× 1.70` means the feature was increased by 70%.

The model was **not retrained** after changing the test inputs.

### Main perturbations

The four main perturbations used for the final comparison were:

* Runtime × 0.80
* Runtime × 1.20
* Vote count × 0.80
* Vote count × 1.20

## Results

| Condition         | Accuracy |
| ----------------- | -------: |
| Clean             |   92.51% |
| Runtime × 0.80    |   92.72% |
| Runtime × 1.20    |   91.57% |
| Vote count × 0.80 |   91.36% |
| Vote count × 1.20 |   92.92% |

The results show that changing an input feature did not always decrease the model's accuracy.

For example, reducing runtime by 20% slightly increased the accuracy from 92.51% to 92.72%.

Increasing vote count by 20% also resulted in a slightly higher accuracy of 92.92%.

On the other hand, reducing vote count by 20% decreased the accuracy to 91.36%.

## Additional exploratory perturbations

During the analysis, I also explored some additional perturbations.

These were part of the existing exploratory work:

| Perturbation        | Accuracy |
| ------------------- | -------: |
| Budget × 1.10       |   92.72% |
| Vote average × 0.80 |   92.51% |
| Runtime × 1.70      |   84.08% |
| Vote count × 1.70   |   89.59% |

The larger runtime perturbation produced a more noticeable decrease in accuracy.

For example, the accuracy decreased from 92.51% on the clean test set to 84.08% when runtime was multiplied by 1.70.

These additional results are treated as exploratory rather than as additional final experiments.

The vote count × 1.70 perturbation was also used for prediction-level examples during the exploratory analysis. It is separate from the four main perturbations reported in the final comparison.

## What I noticed

The accuracy did not always decrease when the input features were changed.

For example:

* Clean → 92.51%
* Runtime × 0.80 → 92.72%

The accuracy slightly increased after reducing runtime by 20%.

Similarly, vote count × 1.20 produced an accuracy of 92.92%, slightly higher than the clean accuracy.

On the other hand:

* Vote count × 0.80 → 91.36%
* Runtime × 1.20 → 91.57%

both resulted in lower accuracy.

The larger runtime × 1.70 perturbation caused a bigger decrease in accuracy, resulting in 84.08%.

This made me realize that looking only at clean accuracy does not tell the whole story about how a model behaves when its inputs change.

## Evaluation plan

The perturbations were explored during the analysis rather than being completely fixed in a written evaluation plan before viewing the results.

Because I do not have an earlier evaluation-plan record showing that the four final perturbations were specified before seeing their results, I treat this analysis as **exploratory**.

For the final comparison, I report the existing ±20% runtime and vote-count results because they provide a consistent comparison across two features and both directions.

I do not claim that these perturbations were pre-specified before seeing the results.

## Limitations

This is a small experiment using one dataset and one model.

The perturbations are artificial changes to numerical feature values, so they do not necessarily represent real-world distribution shifts.

The classification target is based on movie popularity, and some of the input features may be related to popularity. The threshold is calculated over the full retained dataset before splitting, so the task should be understood as descriptive classification of popularity groups rather than future-popularity prediction.

I only used accuracy as the main evaluation metric in this version. I did not evaluate confidence or calibration.

## Reproducibility

The experiment uses Python and common machine learning libraries.

### Requirements

Install the listed dependencies from the repository root:

```bash
pip install -r requirements.txt
```

The original package versions have not been verified from the available artifact. The current requirements file should not be treated as a record of the historical environment unless those versions can be recovered.

### Dataset setup

If the original CSV can be recovered, place it at:

```text
data/tmdb_5000_movies.csv
```

The notebook must read the CSV from this relative path, or from a configurable path with the same default. The dataset is not bundled in this repository snapshot.

If the original CSV or its acquisition source cannot be recovered, the recorded outputs remain historical evidence only; independent reproduction of the original run remains an open item.

### Running the notebook

1. Clone or download this repository.
2. Install the dependencies using the command above.
3. Obtain and place the original CSV at the path described in **Dataset setup**, if available.
4. Open `LogisticRegression.ipynb` in Jupyter Notebook, JupyterLab, or Google Colab.
5. Run the notebook cells if the required dataset and environment are available.

This documentation update does not require a new model fit or perturbation sweep. The previously recorded model, split, exploratory scope, and accuracy outputs are intended to remain unchanged.
xt
