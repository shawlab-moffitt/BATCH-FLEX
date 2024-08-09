# BATCH-FLEX-Shiny Tutorial
BatchFLEX: Comprehensive software for assessing and correcting batch effects, comparing batch correction methods, and exporting corrected matrices for downstream analysis

# Introduction
BatchFLEX is an intuitive Shiny App that can be viewed as a web interface through shiny.io or accessed locally by downloading and installing an app.R or docker container, or by installing an R-package. BatchFLEX is designed with a clean user interface and easy to follow steps to prompt users to input a data matrix and meta file, to select and implement the desired method of batch correction, to assess the impact of the batch correction method using side-by-side comparisons of pre and post corrected graphs and statistics, and finally to export a corrected matrix and accompanying diagnostics for downstream analysis. The intuitive user interface allows for hands-on evaluation and selection of the most optimal batch correction method for any dataset and for users with any background. 

BatchFLEX is the most comprehensive batch correction and evaluation tool and can easily be implemented into any pipeline using the R-package wrapper. Notable features of BatchFLEX include implementation of a wide variety of batch correction methods such as ComBat, ComBatSeq, Quantile Normalization, Harman, LIMMA, RUVg, and Mean centering, incorporation of multiple different types of evaluation methods such as PCA, cluster analysis, gene-variable association analysis, scree plots, SVA, UMAP, RLE, boxplots, and heatmaps, and inclusion of a simple and comprehensive export feature for use of corrected matrices in downstream analysis and for record keeping to justify the correction method selection for publications. 

BatchFLEX can be accessed through shiny.io at (https://shawlab-moffitt.shinyapps.io/batch_flex/). Additionally, a companion R package can be found at (https://github.com/shawlab-moffitt/BATCHFLEX), which includes source code, example data, and an installation guide.

![BatchFLEX_Figure_1_JD_V8_20240809](https://github.com/user-attachments/assets/34044761-e757-46f6-bba7-30401bfe8be2)

# Installation
The BatchFLEX suite can be downloaded (cloned) and installed through the GitHub repository. The downloaded file can be unzipped to a destination folder, which should be set as the working directory or file path. Of note, some of the example files (e.g., gene set files) use relative paths, so the program may fail to identify the file if a working directory is not properly set. The suite was developed in R version 4.1. 

*	**Install BatchFLEX suite GitHub repository**
    * git clone https://github.com/shawlab-moffitt/BATCHFLEX

*	**Download and unzip repository https://github.com/shawlab-moffitt/BATCHFLEX**
    * Set working directory to BatchFLEX folder
    * Install required R packages
    * Suite of tools was built on R version 4.2
    * R script for package installation is provided in the “1-Getting_Started” folder

# Load in Data

## Load Example Data
BatchFLEX includes two example datasets within the Shiny app. The micro array dataset was generated through harmonizing expression data originating from the ImmGen project, study is considered the batch effect and cell type is considered the variable of interest. The RNASeq count dataset was derived from merged study datasets exploring Thapsigargin treated samples, study is considered the batch effect and cell line is considered the variable of interest. Users can access the sample datasets by simply selecting either of the action buttons.

## Input User Data
BatchFLEX provides an intuitive method for users to input and preview a matrix and meta file. If the matrix has not been logged prior to upload, users can log2+1 transform the matrix at this step. 

![image](https://github.com/user-attachments/assets/4f48495e-96fc-43b5-acc9-334a6844b97d)

### Key Input Files
1. Required Files
    * Gene expression file where the gene symbols are in the first column and the sample names are in the first-row header.
    * Meta information file that consists of a matrix with the first column the sample name. The remaining columns should contain the batch information and any variables of interest.
    	
2. Optional Files
    * Housekeeping Gene List is an optional housekeeping gene list for the RUVg correction method. The gene list should be a tab separated file with a single column of genes. 
    * Gene set of Interest is an optional list of genes for a pathway of interest that can be used with the gene set enrichment analysis. 

Matrix and meta files should be formatted similarly to the example datasets shown below. The matrix file should contain genes in the first column and sample IDs in the first row. The meta file should contain sample IDs in the first column and any accompanying meta information in subsequent columns. The sample IDs should match between the matrix and meta files. The meta file should include at least one column of known technical effects and at least one column of known biological effects. BatchFLEX defaults to selecting the second column of the meta file as the batch effect and the third column as the variable of interest. This can easily be changed by the user by simply selecting a different column using the dropdown menus, however, if only a single batch effect is being corrected, having this in the second column can save the user a lot of unnecessary clicking. 

![image](https://github.com/user-attachments/assets/be47fa34-a3bd-41ca-b762-e72ab8c1c16d)

# Selecting a Batch Correction Method

Once the data is loaded into the application, users can proceed to Step 2 where they will select one or two methods for batch correction. Different correction methods will provide different parameters for correction depending on their functionality. Method 1 refers to the matrix previewed on the left and Method 2 refers to the matrix previewed on the right. Above both matrices a text box will contain the processing that have been done to the data. This information is save and can be downloaded in the Step 3 tab. The "Correction Method" panel in the sidebar is collabsible if the user would like to hide it while performing downstream analyses in the app.

![image](https://github.com/user-attachments/assets/5c5d7865-20a1-47f8-812e-3e40eefdd70a)

# Assessment of the Batch Effect

## PCA & UMAP
The input data can be visualized using **PCA** and **UMAP** functions, which can be annotated by assigning color to any criteria in the meta file or can be viewed with a user specified number of clusters generated by k-means. Users can use this feature to assess whether the samples are clustering according to batch or according to the variable of interest. In the example below, a clear batch effect can be seen as the samples are largely clustered according to batch with very little mixing of colors (batch).  BatchFLEX also generates a **multiple components PCA plot**, a **scree plot**, and a table displaying the contribution of variance for a user specified condition. The multiple components PCA plot can be colored by a user specified condition. By default, the multiple components plot displays the top 5 principal components, however, this can be adjusted by the user. These plots provide more details about the PCA analysis, which is important because the batch effect may not be found primarily in PC1 and PC2.

![image](https://github.com/user-attachments/assets/479294c0-4509-4299-b1c2-b667cfc7a14a)

![image](https://github.com/user-attachments/assets/c138e809-0d70-4c89-ad32-170982d6acdc)

![image](https://github.com/user-attachments/assets/c8d9ff3c-a185-4e76-83aa-82cbe736e145)

![image](https://github.com/user-attachments/assets/bcbb47a3-fb05-4df6-862a-d90b55a23053)

## Unsupervised Clustering
BatchFLEX runs cluster analysis of k-means generated clusters using **Elbow, Silhouette, and Dunn plots**. The optimal number of clusters is indicated on both the Silhouette and Dunn plots by a dashed vertical line and is indicated by selecting the cluster number at the elbow of the curve in the Elbow plot. This analysis can help users determine if the optimal number of clusters correlates well with the biological variable of interest. Occasionally, the elbow plot can be difficult to interpret when the pivot point is more gradual. BatchFLEX avoids this issue by including multiple methods of cluster analysis. In BatchFLEX, clustering can also be analyzed using a **heatmap**, which can be annotated according to the meta data. By default, BatchFLEX plots the top 2000 genes by mean absolute deviation (MAD). The user can choose to plot more genes if desired, however, this may affect loading time. Additionally, the user can choose to use the coefficient of variance (CV) or the variance (VAR) as alternatives to the mean absolute deviation. The heatmap of the example data shows that the batch effect is driving a significant amount of clustering. 

![image](https://github.com/user-attachments/assets/3b309512-7011-467c-b24c-58896df8f618)

![image](https://github.com/user-attachments/assets/3d4b9f1e-8ccc-4251-98b5-52e0cfae0ba0)

![image](https://github.com/user-attachments/assets/343d1587-6ef1-492e-a47b-786c6f4abf47)

## Visualize Unwanted Variation
Users can also visualize unwanted variation derived from the batch effect using the sample-wise **relative log expression plot**, which can be colored according to the meta data. This plot shows the median (dot) and interquartile range (grey line) for each sample. The whiskers are set at 1.5 times the interquartile range and any outliers are indicated as a dot outside the whiskers. Users should use this plot to assess whether the batch effect changes the overall median of the data and whether the batch effect introduces significant variability. In the uncorrected RLE plot shown below, the median of GSE15907 (orange) differs significantly from the other studies in this dataset. GSE112876 (blue) and GSE37448 (green) also appear to be much more variable compared to the other studies in the dataset. These details hint that a batch effect is present in the dataset and may need to be removed to help improve the detection of biological differences in downstream analyses. Additionally, the **explanatory variables plot**, and the probability density plot are two methods included in BatchFLEX to broadly assess whether each gene is associated more with the batch effect or with the variable of interest. The explanatory variables plot displays the distribution of R-squared values across all genes for each user selected variable and the probability density plot displays the posterior probability that each gene is associated with the latent variable or the user selected variable of interest as measured by **SVA**. By default, the explanatory variables plot displays the top 10 variables by percent variance explained. The user can select the method that SVA uses to generate the latent variables.  In the explanatory variables plot shown below, a significant amount of variance is being explained by the batch effect, which further supports the need for batch correction. Although the probability density plot appears to show a much more muted impact by the batch effect, it does appear to show that a few genes are highly associated with the batch. In our example dataset, each of these plots support the need for batch correction.

![image](https://github.com/user-attachments/assets/61aa6e4b-0a1e-4eb6-87d0-624a5669da5d)

![image](https://github.com/user-attachments/assets/c07fd720-296e-495f-b104-099d76d0a965)

![image](https://github.com/user-attachments/assets/9ecc1b1e-bbbd-4ff8-ba3f-9951895f7168)

![image](https://github.com/user-attachments/assets/a8b7bf16-1b15-4fce-94a7-9d83fe7051d0)

## Batch Effect Impact of Biological Variable
BatchFLEX can determine the impact of a batch effect or biological variable of interest at an individual gene level using a **boxplot** and using statistical tests such as the Wilcoxon rank-sum test, the t-test, the Kruskal Wallis test, and ANOVA. The boxplot also allows users to assess if outliers are present at the gene level. Users can inspect genes of interest that are expected to remain relatively constant across samples to identify a batch effect. In the example below, beta actin was selected as a housekeeping gene, which should have little variability across samples, however, according to the ANOVA test, beta actin differs significantly between batches, indicating a probable batch effect. Alternatively, users can use **GSEA, Immune Deconvolution, PCA, and SVA** to assess the batch effect and to ensure that differences are still detectable in the variable of interest. BatchFLEX comes preloaded with approximately 100,000 gene sets, but also includes a user upload option for any gene sets not included within BatchFLEX. 

![image](https://github.com/user-attachments/assets/b4e1f39b-c229-4144-b0e5-ddb8a9fc898b)

## Zip File Export
Before the user leaves the application, they have the option to save all the data they processed (1), a log of how it was processed (2), and selected figures that were derived throughout the app using the "Add to Zip File Export" buttons. Initially, all of the files will be ready to be zipped and downloaded, but the user may select certain files they want to download, if not all. If multiple plots of the same data are derived, for example chosing a different color scheme or feature, the plot will be named with sequential numbers for ever press of the "Add to Zip File Export" button. The workflow log transcrip file will be a simple text file of what normalization and or correction methods were performed on the data.

![image](https://github.com/user-attachments/assets/2e7ffa60-04fa-4c35-a24d-f6acbfebf04e)

# Assessment of the Batch Correction
The corrected and uncorrected visualizations and statistical analyses are rendered side-by-side for easy comparison. With BatchFLEX, it is easy to determine whether the batch effect has been removed and to ensure that the biological variable is maintained. In Figure 19, a comparison PCA plot with the study (batch) data annotated by color, the samples are clustered by batch in the uncorrected PCA plot but are more intermingled in the corrected PCA plot. This indicates that batch is having a less profound impact on the variance in the corrected dataset. This this further emphasized in Figure 20, which is colored according to cell type. In this figure, similar cells are not grouping together in the uncorrected PCA plot but are grouping together better in the corrected PCA plot. 

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/0335ad8b-255c-4cdc-adea-aca05ae9ea83">

<img width="465" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/fd179604-7d07-41ec-863e-c24333383a49">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/38a9efcc-de06-4262-8e73-b93b7dab67b0">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/3a2295d4-0bc8-438e-bf27-8be4f295b14f">

Because this dataset is complex, the cluster analysis is less informative and does not consistently align well with the biological variable of interest. However, the silhouette plot changing from an optimal cluster of 6 (the number of studies included in the example data) to 2 (lymphoid and myeloid) is a step in the right direction. Also, the comparison heatmap appears to show less clustering based on the study with a more diverse group of studies within each cluster.

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/91893032-9781-4318-94a5-0202cfca9a74">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/2f881064-ca6f-4045-9272-2e4c3deb8715">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/671e45eb-64f9-48d2-8f48-8d81811223be">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/23f089f1-05f9-40b5-8ec8-1652aefa4136">

Figure 25 is a comparison relative log expression plot that is colored by study (batch) and shows that the median and variability effects from the batch have been removed following correction. GSE112876 (blue) and GSE37448 (green) are much less variable and have medians that more closely match the remaining studies in the dataset. The effect of batch correction is also shown in Figure 26, which shows a significant reduction in the variance explained by the batch. Importantly, the variance explained by the variable of interest is still maintained. This indicates that the batch correction performed well in this instance and differences resulting from the variable of interest should still be detectable in downstream analyses. These results are further echoed in Figure 27, which shows that the probability that a gene is associated with the latent variable is reduced, but the probability that a gene is associated with the variable of interest is maintained. 

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/aaba0d13-2f20-4d95-b71d-3ea9fba5c11e">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/70a1a39d-62ce-4644-bb98-43557441edc5">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/3d5f11dc-9932-4032-baaa-6f940eecc2fd">

In Figure 28, the median expression of the housekeeping gene, beta actin, has been corrected and no longer differs significantly between batch (ANOVA p = 1). In Figure 29, gene set enrichment analysis was performed using the HSIAO housekeeping gene set, which showed a significant difference in enrichment in the uncorrected dataset, but no significant difference in the corrected dataset. The expression of KIT is shown in Figure 30, which is highly expressed in stem cells. A significant difference in expression is seen in both the uncorrected and corrected data sets (ANOVA p < 2.2e-16) with stem cells expressing the highest levels of KIT. Additionally, in Figure 31, the expression of MYB is shown, which should be upregulated specifically during T and B cell development. After correction, precursor T and B cells still display the highest levels of MYB expression. These results highlight that changes due to the variable of interest are maintained post correction. Cumulatively, these results show that the batch correction method was successful at removing the batch effect and maintaining the variable of interest, indicating that an appropriate level of correction was obtained and over or under correction did not occur.  

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/27f50b2c-6e9c-476d-8cbc-4201e4dc388b">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/71ad59f3-141c-42b7-a9bf-98a4d37fbb26">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/daf93dd3-756d-43f5-aaaf-8438344b2007">

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/56aa67b9-6e1f-4b04-874b-14c2d09aa196">

# On-the-fly Comparisons
Users can easily switch between batch correction methods by simply selecting a different method from the drop-down menu. BatchFLEX will automatically update, allowing on-the-fly comparisons of batch correction methods to ensure that the most optimal method is chosen for a particular dataset. 

<img width="468" alt="image" src="https://github.com/shawlab-moffitt/BATCH-FLEX-ShinyApp/assets/89986836/0091a1bd-11d9-4f3e-a0d4-f8a6c737f0c5">

# Disclamer

Copyright 2024 Moffitt Cancer Center
Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:
1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
3. Neither the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.
THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.






























































