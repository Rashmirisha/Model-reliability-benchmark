# Model Reliability Benchmark

This is a small experiment I did to understand how a model behaves when its test inputs are changed.

The goal was to see whether a model with good accuracy on normal test data would still behave similarly when some of its input features were changed.

Instead of using a complicated model, I chose Logistic Regression so that I could focus more on understanding what happens during reliability testing.

## Dataset

I used the **TMDB 5000 Movies dataset**.

The features I used are:
- `budget`
- `revenue`
- `runtime`
- `vote_average`
- `vote_count`

### Dataset acquisition and provenance

The notebook reads the dataset from a local CSV file. The dataset is not bundled in this repository.

- **Source**: Download `tmdb_5000_movies.csv` from Kaggle's [TMDB 5000 Movie Dataset](https://www.kaggle.com/datasets/tmdb/tmdb-movie-metadata).
- **SHA-256 hash**: `1e0584a8dd374120e4ed4f2b81f0d2bd0eb95a554708e4a350d00ed5c46d2825`

I calculated the SHA-256 hash of my local `tmdb_5000_movies.csv` file using PowerShell. It matches the hash recorded above.

To run the notebook, download the CSV from the Kaggle source linked above and place it in the repository root, alongside the notebook. The notebook reads the file using the relative path `tmdb_5000_movies.csv`.

### Classification target

I created a binary classification target using the median popularity of the movies.

The median popularity threshold is calculated over the full retained dataset, after removing rows with missing runtime values and before the train/test split. Movies with popularity greater than or equal to this threshold were assigned `1`, and movies below it were assigned `0`.

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
- `× 0.80` means the feature was reduced by 20%.
- `× 1.20` means the feature was increased by 20%.
- `× 1.70` means the feature was increased by 70%.

The model was **not retrained** after changing the test inputs.

### Main perturbations

The four main perturbations used for the final comparison were:
- Runtime × 0.80
- Runtime × 1.20
- Vote count × 0.80
- Vote count × 1.20

## Results

| Condition | Accuracy |
| :--- | ---: |
| Clean | 92.51% |
| Runtime × 0.80 | 92.72% |
| Runtime × 1.20 | 91.57% |
| Vote count × 0.80 | 91.36% |
| Vote count × 1.20 | 92.92% |

The results show that changing an input feature did not always decrease the model's accuracy.

For example, reducing runtime by 20% slightly increased the accuracy from 92.51% to 92.72%.

Increasing vote count by 20% also resulted in a slightly higher accuracy of 92.92%.

On the other hand, reducing vote count by 20% decreased the accuracy to 91.36%.

## Additional exploratory perturbations

During the analysis, I also explored some additional perturbations:

| Perturbation | Accuracy |
| :--- | ---: |
| Budget × 1.10 | 92.72% |
| Vote average × 0.80 | 92.51% |
| Runtime × 1.70 | 84.08% |
| Vote count × 1.70 | 89.59% |

The larger runtime perturbation produced a more noticeable decrease in accuracy (from 92.51% down to 84.08%).

These additional results are treated as exploratory rather than as additional final experiments.

## What I noticed

The accuracy did not always decrease when the input features were changed:
- Clean → 92.51%
- Runtime × 0.80 → 92.72%
- Vote count × 1.20 → 92.92%

On the other hand:
- Vote count × 0.80 → 91.36%
- Runtime × 1.20 → 91.57%
- Runtime × 1.70 → 84.08%

This made me realize that looking only at clean accuracy does not tell the whole story about how a model behaves when its inputs change.

## Evaluation plan

The perturbations were explored during the analysis rather than being completely fixed in a written evaluation plan before viewing the results.

Because I do not have an earlier evaluation-plan record showing that the four final perturbations were specified before seeing their results, I treat this analysis as **exploratory**.

For the final comparison, I report the existing ±20% runtime and vote-count results because they provide a consistent comparison across two features and both directions.

## Limitations

This is a small experiment using one dataset and one model.

The perturbations are artificial changes to numerical feature values, so they do not necessarily represent real-world distribution shifts.

The classification target is based on movie popularity, and some of the input features may be related to popularity. The threshold is calculated over the full retained dataset before splitting, so the task should be understood as descriptive classification of popularity groups rather than future-popularity prediction.

I only used accuracy as the main evaluation metric in this version. I did not evaluate confidence or calibration.

## Reproducibility

### Requirements

Run the following commands from the repository root.

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```
### Environment record

The project was developed using Jupyter Notebook. The following versions are currently installed in my Anaconda `base` environment:

- Python 3.12.7
- pandas 2.2.2
- NumPy 1.26.4
- scikit-learn 1.5.1
- Jupyter Notebook 7.2.2

These versions were recorded from the current environment. I have not verified that this exact environment was used to generate the original results.

### Running the notebook

Download `tmdb_5000_movies.csv` from the TMDB 5000 Movie Dataset on Kaggle.

Place `tmdb_5000_movies.csv` in the repository root, alongside the notebook. The notebook reads it using the relative path `tmdb_5000_movies.csv`.

Open a terminal in the repository root and launch Jupyter Notebook:

```bash
jupyter notebook
```

Open `LogisticRegression.ipynb` and run the existing cells in order to reproduce the analysis.

The notebook uses the existing train/test split and model configuration documented above. No additional perturbation experiments are required.


