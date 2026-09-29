## Model Justification — K-Nearest Neighbors
Problem Suitability

K-Nearest Neighbors (KNN) is suitable for the ride activity-recognition problem because the task is based on numerical features extracted from sensor measurements. KNN classifies a new ride window according to the labels of nearby samples in the feature space, making it capable of modelling non-linear decision boundaries without assuming a specific functional relationship between the features and the target.

For this application, feature scaling is particularly important because KNN relies on distances between samples. Therefore, StandardScaler is included directly in the KNN pipeline and is fitted only on the corresponding training data during cross-validation.

The use of grouped folds and leave-one-participant-out (LOPO) evaluation also allows us to investigate whether the learned feature-space structure generalizes beyond the exact windows used for training.

## Data Requirements

The dataset contains 768 windows, 52 selected features and 3 participants. This is a relatively small dataset for a machine-learning problem, making KNN computationally feasible.

KNN does not require an explicit training procedure for estimating model parameters. However, its performance can be affected by the number and quality of features because irrelevant or poorly scaled features can influence the distance calculation. For this reason, the existing feature-selection procedure and standardization are important parts of the preprocessing pipeline.

The number of neighbours, k, was treated as a hyperparameter and evaluated using the candidate values:

1, 3, 5, 7, 9, 11, 15, 21

Rather than selecting one value using the complete dataset, nested cross-validation was used to select k independently inside each outer training set.

## Interpretability

KNN is relatively easy to understand at the prediction level: a prediction is based on the labels of the nearest training samples. This provides an intuitive explanation of why a particular window is classified as bumpy or smooth.

However, KNN is less interpretable at a global level than a decision tree because it does not produce an explicit set of decision rules. The prediction depends on the local neighbourhood of each sample.

For the current application, the recording-level majority vote provides an additional interpretable output by showing the fraction of windows within a recording that were predicted as bumpy.

## Computational Efficiency

KNN has very little computational cost during model fitting because it mainly stores the training data. However, prediction requires calculating distances between a new sample and training samples.

For the current dataset of 768 windows and 52 selected features, this computational requirement is small and KNN is practical for experimentation and potentially for deployment on a mobile or real-time system.

For a substantially larger training dataset, however, prediction cost could become more important because each new window needs to be compared with training samples. Therefore, deployment efficiency should be verified using the independent external dataset and the intended hardware rather than assumed from the current experiment.

## Performance

The nested cross-validation results show:

Evaluation	                         Accuracy	             Balanced Accuracy	           F1
Grouped folds — mean	              92.43%	             92.24%	                     91.81%
Leave-one-participant-out — mean	  81.94%	             82.66%	                     82.04%

The difference between the two evaluation schemes is relevant. The grouped-fold evaluation gives higher performance, while LOPO evaluates the model on participants that were not represented in the training data. The lower LOPO performance therefore indicates that generalization to unseen participants is more challenging.

At recording level, the LOPO predictions achieved 84.6% accuracy (22/26 recordings) using majority voting over the windows of each recording.

## Evaluation Metrics
Because the task is a binary classification problem, accuracy alone is not sufficient to describe model behaviour. We therefore evaluate accuracy together with balanced accuracy, precision, recall, specificity and F1 score. Balanced accuracy is particularly useful because it considers both the ability to detect bumpy windows and the ability to correctly identify smooth windows. Precision and recall provide complementary information about bumpy predictions, while specificity measures how well smooth windows are identified. F1 score combines precision and recall into a single measure.

## Deployment

After finalizing the models and selecting the best-performing one, the model will be applied to the independent external dataset. This dataset will not be used during model training, hyperparameter selection or model evaluation. The finalized model will classify the windows of the external rides as smooth or bumpy. These window-level predictions will then be mapped back to the corresponding road sections to create a visualization showing which sections of the road are classified as smooth or bumpy.