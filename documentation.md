## DBSCAN


We ran DBSCAN on the PCA-transformed features without giving the algorithm access to the surface labels during clustering. After tuning the parameters, we settled on eps = 4 and min_samples = 5, which split the data into two main clusters alongside a group of noise points. We then mapped these clusters back to the actual smooth/bumpy labels to see if the unsupervised structure naturally reflected different road conditions. To help interpret the results, we used t-SNE purely to visualize the DBSCAN clusters and original labels in 2D—it wasn't used to feed data into DBSCAN itself.