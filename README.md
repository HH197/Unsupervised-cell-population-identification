# Unsupervised Cell Population Identification

Different cells can have various behavior during their life spans, and these behavioral differences are represented in their molecular landscapes, such as their transcriptomics. Thanks to the high-throughput sequencing of whole transcriptomes (RNA-seq), single-cell transcriptomics is now emerging as a powerful approach to studying cell variations on genomic level. Single-cell RNA-seq (scRNA-seq) has enabled the study of different compositions in complex tissues, such as cell types and states. However, processing scRNA-seq data is still a challenge due to high dimensionality, heterogeneity, low signal-to-noise ratio (SNR), artifacts, batch effects, dropouts, and sparsity (See this [link](https://github.com/HH197/Deep-Generative-Modeling-and-Probabilistic-Dimension-Reduction#challenges-in-analyzing-single-cell-rna-eq-data)).

Cell type identification usually is one of the critical goals of scRNA-seq data analysis. This identification is typically a clustering problem. Groups identified in an unsupervised manner are annotated to cell types. However, clustering single cells based on their gene expression level is complicated considering the curse of high dimensionality, low SNR, and artifacts. Consequently, scRNA-seq data analysis frequently involves pre-processing, feature selection, and dimension reduction before the clustering step.

This study aims to identify different cell types and cell states using publicly available single-cell data sets. Here, we propose various approaches consisting of three steps, pre-processing, dimension reduction, and clustering. 

## Data

We used the Mouse Cortex count matrix (GSE accession number [GSE60361](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60361)) as our dataset. It contains the cell types (classes) as well as the gene expression of ~20,000 genes (features) for 3,005 distinctive cells (samples). 

## Pre-processing

Since genes (features) expressed in less than a certain number of cells (samples) might not be effective in identifying cell types, we have chosen genes that are expressed in more than 25 cells. In addition, we log-transformed the data and then selected highly-variable genes (features), i.e., genes with high variance. Finally, we applied random permutation to samples and features.  

## Dimension Reduction

For the purpose of dimension reduction, we have used principal component analysis (PCA) and two different types of autoencoders.

### PCA 

We have chosen 1,024 high variable genes among the ~15,000 and 3,005 cells as the input of the PCA. When applying the PCA, the number of components is selected based on the explained variance. In other words, the number of components is the elbow of the accumulated explained variance. 

### Autoencoder

We have used vanilla and Deep autoencoders. The vanilla autoencoder has 256 input features (genes), one hidden layer with a size of 32, and an output of the same size as the input. In the Deep autoencoder, the size of the input features is 3,000, which is the same as the output. The Deep autoencoder consists of 2 hidden layers with sizes 1,024 and 16, respectively. 

In all autoencoders, the activation functions used in the encoding and decoding layers are the rectified linear unit (ReLU) with the leak of 0.1. A linear activation was used for the embedding layer of our models.

We used a batch size of 256 for training. The training processes of our models are quite similar, with the difference in the number of epochs. For the vanilla and Deep autoencoders, we used 450 and 150 epochs, respectively. ADAM was chosen as the optimizer with a learning rate of 0.001 and mean root squared error as the loss function. 


## Clustering

The CORTEX data set ([GSE60361](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60361)) is called a gold-standard data set since we know the categories of the samples, which is not the case in most of the data sets. Therefore, we can use the CORTEX data set as a benchmark of clustering performance.

As clustering is an unsupervised task, the number of clusters is unknown. The Silhouette score is used to evaluate the quality of clustering, which takes a value between -1 and 1. We used the elbow point, i.e., the point at which the silhouette score afterward drops dramatically, as the number of clusters. 

We performed clustering on the latent space of PCA and autoencoders. We used K-means and spectral clustering, along with three different clustering metrics: Normalized Mutual Information (NMI), Adjusted Rand Index (ARI), and Average Silhouette Width (ASW).

## Results

### PCA + K-means
###


