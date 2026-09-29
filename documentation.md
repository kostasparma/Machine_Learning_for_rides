## DBSCAN


We ran DBSCAN on the PCA-transformed features without giving the algorithm access to the surface labels during clustering. After tuning the parameters, we settled on eps = 4 and min_samples = 5, which split the data into two main clusters alongside a group of noise points. We then mapped these clusters back to the actual smooth/bumpy labels to see if the unsupervised structure naturally reflected different road conditions. To help interpret the results, we used t-SNE purely to visualize the DBSCAN clusters and original labels in 2D—it wasn't used to feed data into DBSCAN itself.

# DBSCAN – Model Justification and Evaluation

## Model Justification

### Problem Suitability

DBSCAN (Density-Based Spatial Clustering of Applications with Noise) was selected as one of the unsupervised learning methods for the bike-lane quality classification problem.

The main advantage of DBSCAN for this problem is that it does not require the number of clusters to be specified in advance. Instead, it identifies groups of observations based on their local density. This is relevant because the purpose of the unsupervised analysis is to investigate whether the sensor data naturally form groups that may correspond to different road-surface conditions.

Another useful property of DBSCAN is that it can identify observations as noise. In sensor-based activity recognition, some windows may contain unusual or less representative measurements. Instead of forcing every observation into a cluster, DBSCAN can leave such observations unassigned.

The clustering was performed without using the known `Smooth` and `Bumpy` labels. The labels were only used afterwards to investigate whether the clusters found by DBSCAN corresponded to the actual road-surface conditions.

---

### Data Requirements

The final dataset used for DBSCAN contained 570 windows. From each window, 66 features were extracted from the sensor signals.

The extracted features included both time-domain and frequency-domain information. Time-domain features described characteristics such as variability, range, distribution shape and changes between consecutive samples. Frequency-domain features described how the signal power was distributed across different frequency bands.

Before applying DBSCAN, the features were standardized using zero-mean and unit-variance scaling. This step was important because DBSCAN is distance-based. If different features had very different numerical scales, features with larger values could have a disproportionate influence on the distances between observations.

A correlation analysis showed that several features were highly correlated. Features with an absolute correlation above 0.95 were identified. Instead of directly removing features arbitrarily, PCA was applied to reduce the dimensionality of the feature space.

The 66 original features were reduced to 18 principal components while retaining approximately 95.48% of the total variance.

Therefore, DBSCAN was finally applied to a feature matrix with:

- 570 observations
- 18 dimensions

This representation provides a more compact input space while preserving most of the variance contained in the original features.

---

### Interpretability

DBSCAN is relatively interpretable at the clustering level because its behaviour is based on the concept of local data density.

The two main parameters are:

- `eps`: defines the radius of the neighbourhood around each observation.
- `min_samples`: defines the minimum number of observations required within a neighbourhood for an observation to be considered part of a sufficiently dense region.

DBSCAN also explicitly identifies noise observations using the cluster label `-1`.

For the final analysis, the selected parameters were:

```python
dbscan = DBSCAN(
    eps=4,
    min_samples=5
)

clusters = dbscan.fit_predict(X_pca)

The resulting distribution was:

Cluster	Number of windows
Noise (-1)	98
Cluster 0	443
Cluster 1	29

The known surface labels were then compared with the resulting clusters.

Cluster	    Bumpy	Smooth
Noise (-1)	75   	23
Cluster 0	210	    233
Cluster 1	0	    29

Cluster 1 consisted entirely of Smooth windows, while Cluster 0 contained both Smooth and Bumpy windows. The noise group contained mostly Bumpy windows.

This comparison was performed after clustering and therefore did not influence the DBSCAN model itself.

## Computational Efficiency


The final DBSCAN input consisted of 570 observations with 18 dimensions after PCA. Therefore, the clustering problem is relatively small in terms of dataset size.

Dimensionality reduction also decreases the number of dimensions on which the distance calculations are performed compared with using all 66 extracted features.

However, the computational suitability of DBSCAN for real-time or mobile deployment has not yet been directly measured. In particular, execution time and resource consumption on a smartphone have not been evaluated at this stage.

Therefore, no final conclusion about mobile deployment efficiency can yet be made.

## Performance

The current DBSCAN analysis focuses on examining the structure discovered by the unsupervised algorithm.

Different values of eps and min_samples were investigated to examine how the number of clusters and noise observations changed.

For eps values between 1 and 4, the following results were obtained:

eps	Clusters	Noise
1.0	 1       	534
1.5	 5	        464
2.0	 5	        345
2.5  4	        267
3.0	 6	        203
3.5	 5	        150
4.0	 2	        98

The effect of min_samples was also investigated while keeping eps = 4:

min_samples	Clusters	Noise
3	            4	     79
5	            2	     98
10	            2	     121
15	            5	     136

For min_samples = 3, two of the resulting clusters contained only three windows each. The configuration eps = 4 and min_samples = 5 was therefore used for the subsequent DBSCAN analysis.

The final model produced two clusters and a noise group. The comparison with the known labels showed that one of the clusters contained only Smooth windows, while the larger cluster contained a mixture of Smooth and Bumpy windows.

A direct comparison with the other supervised and unsupervised models cannot yet be made because their analyses have not been completed.

## Evaluation and Visualization

Several analyses were used to examine the DBSCAN results.

First, the number of clusters and noise observations was examined for different parameter configurations.

Second, a cross-tabulation between the DBSCAN clusters and the known surface labels was used to investigate the relationship between the discovered clusters and the actual road-surface conditions.

Third, the clusters were visualized using the first two principal components. This provides a two-dimensional representation of the PCA space and allows the spatial distribution of the clusters to be inspected.

Finally, t-SNE was used to create an additional two-dimensional visualization. t-SNE was used only for visualization and was not used as input to DBSCAN.

Two t-SNE visualizations were considered:

The observations coloured according to their DBSCAN cluster.
The observations coloured according to their actual Smooth/Bumpy label.

These visualizations provide a qualitative way to inspect whether the structure identified by DBSCAN is related to the actual surface conditions.

## Limitations and Further Evaluation

The current DBSCAN results provide an initial assessment of the structure present in the sensor-derived feature space. However, the final performance of DBSCAN cannot be determined independently of the other models in the assignment.

The final evaluation will require comparison between the four supervised and four unsupervised approaches using appropriate evaluation criteria.

In particular, the final model selection will consider not only predictive or clustering performance, but also problem suitability, data requirements, interpretability and computational efficiency.

The external dataset will not be used for training or testing. After the models have been finalized and the final model has been selected, the external dataset will be used for deployment and for assessing the quality of previously unseen bike-lane sections.

The final deployment will also include a visualization indicating which sections of the road are classified as Smooth or Bumpy.