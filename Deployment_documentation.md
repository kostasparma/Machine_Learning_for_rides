## Deployment Process

To test how well the final Logistic Regression model performs in the real world, we deployed it on an independent, external dataset collected for bike-lane quality assessment. This dataset was kept completely separate from the start and wasn't used for training, feature selection, or tuning any parameters.

We began by loading the calibrated accelerometer, gyroscope, and gravity data, aligning them using their timestamps. The preprocessing matched our training pipeline step-for-step: we trimmed the start and end of the recording, combined accelerometer and gravity data to compute total acceleration, and extracted derived signals (vertical acceleration, filtered vertical acceleration, horizontal acceleration, gyroscope magnitude, and total acceleration magnitude).

Next, we split the ride into 4-second windows with a 2-second step (50% overlap). Applying feature extraction to each window generated 66 candidate features covering time- and frequency-domain properties across the sensor signals.

For feature selection, we strictly stuck to the setup established during training—using the top 24 features chosen via Mutual Information (MI). Crucially, these features were fixed during training; we didn't re-run feature selection on the external data.

The final model was then trained on the 324 labeled training windows using these 24 features, with a StandardScaler for normalization and Logistic Regression (max_iter=5000, random_state=42).

Finally, we ran the fitted model on all 503 windows from the external ride without fitting anything new. The model flagged each window as either smooth or bumpy. We then mapped these predictions back to their original timestamps along the route to visualize the road quality as a sequence of smooth and bumpy sections.

In total, the model predicted 466 smooth and 37 bumpy windows. Because this external dataset lacks ground-truth labels, we can't calculate a formal accuracy or error rate. Instead, these outputs represent the model's estimated road quality along the unseen route.