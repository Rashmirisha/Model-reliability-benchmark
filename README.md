
# Model Reliability Benchmark

This is a small experiment I did to understand how a model behaves when its test inputs are changed.

The goal was to see whether a model with good accuracy on normal test data would still behave similarly when some of its input features were changed.

Instead of using a complicated model, I chose Logistic Regression so that I could focus more on understanding what happens during the reliability testing.

## Dataset

I used the **TMDB 5000 Movies dataset**.

The features I used are:

- `budget`
- `revenue`
- `runtime`
- `vote_average`
- `vote_count`

### Classification target

I created a binary classification target using the median popularity of the movies.

Movies with popularity greater than or equal to the median were assigned `1`, and movies below the median were assigned `0`.

This means the model predicts whether a movie belongs to the higher-popularity or lower-popularity group.

## Data preprocessing

There were two missing values in the `runtime` column, so those rows were removed before the final train/test split.

I did not use a learned preprocessing step such as scaling or imputation.

After removing the rows with missing runtime values, I recreated the train/test split.

## Model

I used **Logistic Regression** as the baseline model.

I split the data into 80% training and 20% testing using `random_state=42`.

The model was trained once and then kept fixed for all the perturbation tests.

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
|---|---:|
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

During the analysis, I also explored some additional perturbations.

These were part of the existing exploratory work:

| Perturbation | Accuracy |
|---|---:|
| Budget × 1.10 | 92.72% |
| Vote average × 0.80 | 92.51% |
| Runtime × 1.70 | 84.08% |
| Vote count × 1.70 | 89.59% |

The larger runtime perturbation produced a more noticeable decrease in accuracy.

For example, the accuracy decreased from 92.51% on the clean test set to 84.08% when runtime was multiplied by 1.70.

These additional results are treated as exploratory rather than as additional final experiments.

## What I noticed

The accuracy did not always decrease when the input features were changed.

For example:

- Clean → 92.51%
- Runtime × 0.80 → 92.72%

The accuracy slightly increased after reducing runtime by 20%.

Similarly:

- Vote count × 1.20 → 92.92%

was slightly higher than the clean accuracy.

On the other hand:

- Vote count × 0.80 → 91.36%
- Runtime × 1.20 → 91.57%

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

The classification target is based on movie popularity, and some of the input features may be related to popularity. This is important when interpreting the results.

I only used accuracy as the main evaluation metric in this version. I did not evaluate confidence or calibration.

## Reproducibility

The experiment uses Python and common machine learning libraries.

Install the required packages:

```bash
pip install -r requirements.txt
