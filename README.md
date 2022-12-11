# Unsupervised Cell Population Identification

Different cells can have various behavior during their life spans, and these behavioral differences are represented in their molecular landscapes, such as their transcriptomics. Thanks to the high-throughput sequencing of whole transcriptomes (RNA-seq), single-cell transcriptomics is now emerging as a powerful approach to studying cell variations on genomic level. Single-cell RNA-seq (scRNA-seq) has enabled the study of different compositions in complex tissues, such as cell types and states. However, processing scRNA-seq data is still a challenge due to high dimensionality, heterogeneity, low signal-to-noise ratio (SNR), artifacts, batch effects, dropouts, and sparsity (See this [link](https://github.com/HH197/Deep-Generative-Modeling-and-Probabilistic-Dimension-Reduction#challenges-in-analyzing-single-cell-rna-eq-data)).

Cell type identification usually is one of the critical goals of scRNA-seq data analysis. This identification is typically a clustering problem. Groups identified in an unsupervised manner are annotated to cell types. However, clustering single cells based on their gene expression level is complicated considering the curse of high dimensionality, low SNR, and artifacts. Consequently, scRNA-seq data analysis frequently involves pre-processing, feature selection, and dimension reduction before the clustering step.

This study aims to identify different cell types and cell states using publicly available single-cell data sets. Here, we propose various approaches consisting of three steps, pre-processing, dimension reduction, and clustering. 

## Data

We used the Mouse Cortex count matrix (GSE accession number [GSE60361](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60361)) as our dataset. It contains the cell types (classes) as well as the gene expression of ~20,000 genes (features) for 3,005 distinctive cells (samples). 

## Pre-processing

Since genes (features) expressed in less than a certain number of cells (samples) might not be effective in identifying cell types, we have chosen genes that are expressed in more than 25 cells. In addition, we log-transformed the data and then selected highly-variable genes (features), i.e., genes with high variance. Finally, we applied random permutation to samples and features.  

## Dimension Reduction

For dimension reduction, we have used principal component analysis (PCA) and two different types of autoencoders.

### PCA 

We have chosen 1,024 high variable genes among the ~15,000 and 3,005 cells as the input of the PCA. When applying the PCA, the number of components is selected based on the explained variance. In other words, the number of components is the elbow of the accumulated explained variance. 

### Autoencoder

We have used vanilla and Deep autoencoders. The vanilla autoencoder has 3000 input features (genes), one hidden layer with a size of 32, and an output of the same size as the input. In the Deep autoencoder, the size of the input features is the same as in the vanilla autoencoder. The Deep autoencoder consists of 2 hidden layers with sizes 1,024 and 16, respectively. 

In all autoencoders, the activation functions used in the encoding and decoding layers are the rectified linear unit (ReLU) with leak = 0.1. A linear activation was used for the embedding layer of our models. The structure of our designed autoencoders is as follows: 




We split the data into the train (80%) and test (20%) sets with a batch size of 128 for training. The training processes of our models are quite similar. ADAM was chosen as the optimizer with a learning rate of 0.001 and mean root squared error as the loss function. We used early stopping to avoid overfitting with a patience of 4, i.e., if the test loss is not decreased after 4 epochs, it stops the training procedure. 


## Clustering

The CORTEX data set ([GSE60361](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60361)) is called a gold-standard data set since we know the categories of the samples, which is not the case in most of the data sets. Therefore, we can use the CORTEX data set as a benchmark of clustering performance.

As clustering is an unsupervised task, the number of clusters is unknown. The Silhouette score is used to evaluate the quality of clustering, which takes a value between -1 and 1. We used the elbow point, i.e., the point at which the silhouette score afterward drops dramatically, as the number of clusters. 

We performed clustering on the latent space of PCA and autoencoders. We used K-means and three different clustering metrics: Normalized Mutual Information (NMI), Adjusted Rand Index (ARI), and Average Silhouette Width (ASW).

## Results

### PCA

To decide how many eigenvalues/eigenvectors to keep (i.e., how many components to keep), we used the accumulated explained variance plot. The plot will provide a good indication of when one hits the point of diminishing returns (i.e., negligible variance is gained by retaining additional eigenvalues). This point will be considered as the number of components. In other words, the elbow point of the accumulated explained variance is considered as the number of components. 

The following figure shows the ratio of the explained variance. Since the elbow point of the plot is around 10 components, we kept 10 components.

Then we applied K-means to the PCA's latent space. Using the following plot, which shows the Silhouette score for a specific number of clusters, we detected 7 clusters. The NMI, ARI, and ASW scores are 0.56, 0.47, and 0.31, respectively. 


These findings show that we have not achieved high accuracy in the task of cell identification when applying PCA followed by K-means. Therefore, we tried other methods of dimension reduction.

### Vanilla AutoEncoder

We applied K-means to the latent space. The NMI, ARI, and ASW scores are 0.48, 0.31, and 0.27, respectively, showing that we have not achieved high accuracy in the task of cell identification. Therefore, we tried a deep autoencoder for dimension reduction. 

### Deep AutoEncoder

We applied K-means to the latent space. The NMI, ARI, and ASW scores are 0.79, 0.73, and 0.24, respectively, showing that we have achieved high accuracy in the task of cell identification. 

  
### Comparison

We compared the ability of PCA, vanilla autoencoder, and deep autoencoder latent spaces. We applied K-means with same paramters on their latent variables. We found that deep autoencoder has the best performace in emphasizing the differences of cell populations in its latent space.   


## Discussion

Cell-type and cell-state identification are one of the crucial parts of the single-cell analysis. Different approaches implemented in this study show promising results in identifying several cell-types in the mouse cortex data set. The deep autoencoder achieved high scores since its latent space captures complex biological information, which leads to the distinction between the cell types. We also tried wider and deeper models; still, the results did not outperform our deep autoencoder approach. Finally, before performing dimension reduction, we transformed the data to logarithmic scale, which is not encouraged in some cases. 
