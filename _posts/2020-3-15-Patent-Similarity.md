---
layout: post
title: Patent Clustering Experiment & Analysis 
---


> A summary of my research to develope a model identify similar patents to that owned by a chemical company. The goal of this experiment is to find a way to improve strategic business decision making  

## Introduction & Problem Statement 

Patent systems have existed for many years. In the United States, patents were introduced by the Patent Act of 1790 (Patent Act of 1790, Ch. 7, 1 Stat. 109, 1790). Since then, the work to identify similar patents has been a mostly manual process. In some cases, human judgement and a significant amount of manual work is required in order to find similar patents. These costs have and will continue to increase as the number of patents grows in the United States. By comparing existing patents to the patents companies already own, or to their industry secrets, companies can more effectively hone their competitive advantages by getting a better idea of the competitive landscape. Companies can also get a view on the uniqueness of their manufacturing processes and make strategic decisions on how and where to invest in order to create or maintain a competitive advantage. In this paper, we will review previous related research and evaluate the effectiveness of several natural language processing models to help identify patents that are like the company’s.

## Brief Review of Literature

Previous work shows that using machine learning to interact with patent data is not uncommon. One of the closest related works is a patent “landscaping” model that utilizes graph embedding and a diffusion graph to show how patents are interrelated to each other (Choi, Lee, Park, & Choi, 2019). The model utilizes all of the patent text and metadata in order to make a prediction. The model is then tested against other contemporary models and is shown to outperform them in both average precision and F1 scores (Choi, Lee, Park, & Choi, 2019). In addition to the work done by Choi, et al, models to evaluate the qualities of a patent for classification have also been utilized. By clustering patents together, reducing the dimensionality, and using a support vector machine, an efficient system was created that can assign classifications to patents that are on par with the traditional manual approach (Wu, Chang, Tsao, & Fan, 2016).

## Design & Methods

### Data Used

The corpus of “abstracts” consists of patent data owned by the United States Patent and Trademark Office (USPTO). The Patents View database contains this information and makes it available for bulk download (Office of Chief Economist, USTPO, 2020). One can download the specific tables containing the desired information. In this case, the abstract data contained millions of lines of text data, each focused on a patent. Each patent also had additional related metadata. In order to capture relationships between the data and patents, I created an ontology shown in Figure 1.

<div align="center">

**Figure 1**. Ontology of Patent Data

![Figure 1](/images/patent-similarity-figure1.png "Figure 1")

</div>

The initial dataset was over 60GB. This caused an interesting problem, as local resources and streaming were not enough to compile and analyze all the patents efficiently. A visualization of the distribution of the top 10 classification categories is shown in Figure 2 with the top frequent CPC subgroups for the company in Figure 3. As you can see from the visual, the top 3 categories contain over 2.5 million classifications. To compound the matter, each patent can be assigned multiple CPC classifications. In order to bring the data into a more manageable state, I pulled in the CPC classifications that only concern the company. A visualization of the top 10 most frequent CPC subsection classifications are shown in Figure 2 below. After initial testing, the dataset for analysis needed to be restricted further to one classification, Organic Chemistry, which was the company’s highest assigned subgroup, as seen in Figure 3. This additional restriction was necessary to enable and reduce processing time on the local computer as well as increase the quality of the results by getting rid of patents that would obviously have nothing to do with the company’s patents (like children’s swing set designs). In addition, this would help focus model vocabulary to words that would distinguish differences between chemical patents at a more granular level, which can be technical in nature. A sample of a patent abstract is shown in Figure 4 below.

<div align="center">

**Figure 2**. Overall Top 10 CPC Patent Classification Categories

![Figure 2](/images/patent-similarity-figure2.png "Figure 2")


<br>
<br>

**Figure 3**. the company Top 10 Frequent CPC Subgroup Classifications

![Figure 3](/images/patent-similarity-figure3.png "Figure 3")

<br>
<br>

**Figure 4**. Sample of Patent Abstract

</div>

> "This document describes biochemical pathways for producing 2-aminopimelate from 2,6-diaminopimelate, and methods for converting 2-aminopimelate to one or more of adipic acid, adipate semialdehyde, caprolactam, 6-aminohexanoic acid, 6-hexanoic acid, hexamethylenediamine, or 1,6-hexanediol by decarboxylating 2-aminopimelate into a six carbon chain aliphatic backbone and enzymatically forming one or two terminal functional groups, comprised of carboxyl, amine or hydroxyl group, in the backbone.”


### Modeling & Methods

Initially, I used a TF-IDF matrix to identify words that had the top TF-IDF scores. However, this did not prove as fruitful as I had hoped, as the resulting words identified were too-high level (i.e “ligand”) and were not as representative of the abstracts. The top 25 TF-IDF scores for the company patents and the corpus in Table 1, in the appendix. In order to capture more information about the words, I used Word2Vec in order to help capture the semantic meaning of the words. Word2Vec was selected because of the ability to contextualize individual words. This is a good fit as the abstracts are small at 3 or 4 sentences. In addition, many of the abstracts contain words that would be key in identifying similar applications, such as a chemical name. In this analysis, I experimented both large and a small sized embedding at 100 and 400 words, respectively. I also utilized a window of 5 words across the different texts. 

<div align="center">

**Table 1**. Top TF-IDF Words
|   index   |            words            |   tf-idf score  |
|:---------:|:---------------------------:|:---------------:|
|      0    |           compounds         |      2421.93    |
|      1    |           invention         |     2268.617    |
|      2    |            formula          |     1750.843    |
|      3    |            present          |      1667.46    |
|      4    |            methods          |     1647.592    |
|      5    |      present   invention    |      1566.68    |
|      6    |            relates          |     1470.858    |
|      7    |         compositions        |     1440.112    |
|      8    |           compound          |     1328.602    |
|      9    |            thereof          |     1278.827    |
|     10    |      invention   relates    |     1242.738    |
|     11    |          comprising         |     1167.615    |
|     12    |           provides          |     1088.059    |
|     13    |             acid            |     1049.196    |
|     14    |             group           |     1045.174    |
|     15    |              use            |     1032.593    |
|     16    |           treatment         |     1009.071    |
|     17    |             also            |     986.2028    |
|     18    |           provided          |     974.2748    |
|     19    |            method           |     946.0351    |
|     20    |           disclosed         |     927.8326    |
|     21    |           diseases          |     912.3672    |
|     22    |        pharmaceutical       |     908.0968    |
|     23    |            wherein          |     892.2472    |
|     24    |     invention   provides    |     878.1491    |

</div>

After the embeddings of the words were learned, a custom function was utilized in order to turn each sentence into a Word2Vec representative vector. The function summarizes the meaning of the words in the context of the abstract. Once the abstract-representative vectors were obtained for the corpus, the documents are ready to be clustered based on similarity. To perform the clustering, the K-Means algorithm was utilized with 8 clusters and performed updates to the centroid locations 500 times while training. After clustering, work began to create a visual representation of the documents in a 2D graph, which required dimensionality reduction from hundreds of dimensions. I utilized Multidimensional scaling (MDS) to reduce the dimensions while maintaining as much of a representation of the data as possible. An interesting twist to the process of analysis was that the cosine similarity could not be calculated in whole, as the dot product of combining the matrix together utilized too much memory. In order to correct for this, I utilized a custom function that calculates the cosine similarity in chunks (Sal, 2016).  Then, in order to capitalize on the efficiency gained from transforming the document space to much lower dimension, I calculated the distance of a sample of the company documents to the closest non-the company document in order to see if the closest documents were somewhat related to the the company documents.

In addition to the work performed above, Latent Dirichlet Allocation (LDA) modeling was performed in order to attempt to capture the overall topics in the abstracts and to group and classify documents by their overall topics. LDA analysis is performed on the corpus via bag-of-words methodology, instead of the semantic word embedding performed in the Word2Vec algorithms. For the LDA analysis, we utilized 15 and 25 different topics.


## Results

The results of both the Word2Vec/K-Means analysis and the LDA topic modeling were done with a manual review in order to judge the quality of the clustering algorithm. For the Word2Vec/K-Means analysis, most of the patents were focused around a particular cluster. The cluster groupings of the the company patents are in Figure 5 below. However, when evaluating the results of the cluster, regardless of the change in the size of the word embeddings, the quality of the matches was sub-par. An example of an the company patent against the closest match from both word embedding sizes is in Figure 6 below. In Figure 5, we can see that the closest matching records do not, in fact, relate to the original the company patent. The the company patent speaks to the creation of Paraxylene (an industrial chemical) from a parent chemical and the two sample matches relate to a salt used in treating diseases in the body and a fluid delivery mechanism. 

<div align="center">

**Figure 5.** Word2Vec/K-Means Cluster Results
|     Cluster    |     Count   of the company Patent in Cluster    |
|:--------------:|:-------------------------------------------:|
|        0       |                       9                     |
|        1       |                      180                    |
|        2       |                      85                     |
|        3       |                      41                     |
|        4       |                      51                     |
|        5       |                      26                     |
|        6       |                      51                     |
|        7       |                      12                     |

</div>
<br>

<div align="center">

**Figure 6.** Word2Vec/K-Means matching result of a Chemical Company Patent

***Selected Patent:***

>Title: Paraxylene extraction of purification mother liquor with heat integration

>Abstract: The present invention provides a process for the production of an aromatic dicarboxylic acid comprising the catalytic oxidation of a hydrocarbon precursor in an organic solvent, comprising the steps of: 
>
> i) separating a vent gas from an oxidation stage into an organic solvent-rich liquid stream and a water-rich vapor stream in a distillation stage; and 
>
> ii) separating an aqueous purification mother liquor comprising organic compounds from purified aromatic dicarboxylic acid crystals in a separation stage, characterized in that the process further comprises the steps of: 
>
> iii) transferring the aqueous purification mother liquor from the separation stage to an extraction stage; 
>
> iv) extracting said organic compounds from the aqueous purification mother liquor by contacting the aqueous purification mother liquor at a temperature of at least 90Â° C. with an organic liquid in the extraction stage to form an aqueous phase and an organic phase, wherein the concentration of said organic compounds in the aqueous phase is lower than the concentration of said organic compounds in the aqueous purification mother liquor; and v) transferring the aqueous phase to said distillation stage. The present invention further provides an apparatus for carrying out the process.

<br>
<br>
***Closest Matching Patent:***

> Title: Pyrimidine derivatives as CFTR modulators

>Abstract: A compound having the structure of Formula II, or a stereoisomer, tautomer, or a pharmaceutically acceptable salt thereof:The compound is useful as CFTR modulator. Further, a method of using the compound and pharmaceutical composition comprising the compound are provided for treating diseases in lungs, pancreas, gastrointestinal system, sinuses, reproductive system, and the sweat glands.
</div>



In the case of the LDA topic modeling, the results were more high-level but easier to identify and segment what kinds of documents to expect. The 25-topic model produced an acceptable level of specificity in topics, while the 15-topic model was too general and captured only chemicals for medical uses (which doesn’t pertain to the company). This result is better than the Word2Vec/K-Means analysis as we do not immediately match individual documents, but rather browse documents that relate most to key words in the topics that we are interested in. For example, in Figure 7, we can see the results of the topics and where the the company patents fall. For example, topic number 21 contains the most the company patents and the topic most directly pertains to the company’s core business. That is, the processing of acids to achieve a reaction and produce a mixture of chemicals for nylon production. 


<div align="center"> **Figure 7.** LDA 25-Topic Modeling Results, Sample of Topics 

![Figure 7](/images/patent-similarity-figure8.png "Figure 7") 

<br>
<br>

Topic: 0 

> Words: 0.029*"catalyst" + 0.021*"material" + 0.016*"process" + 0.010*"invention" + 0.010*"catalysts" + 0.010*"materials" + 0.009*"high" + 0.009*"present" + 0.009*"organic" + 0.008*"conversion"

Topic: 4 

> Words: 0.046*"invention" + 0.033*"present" + 0.033*"treatment" + 0.032*"diseases" + 0.030*"relates" + 0.018*"use" + 0.017*"disorders" + 0.016*"compounds" + 0.012*"novel" + 0.011*"methods"

Topic: 5 

> Words: 0.039*"peptide" + 0.022*"peptides" + 0.022*"invention" + 0.021*"amino" + 0.020*"acid" + 0.011*"present" + 0.010*"conjugates" + 0.009*"methods" + 0.009*"comprising" + 0.009*"method"

Topic: 6 

> Words: 0.020*"polymer" + 0.017*"composition" + 0.017*"compound" + 0.010*"method" + 0.009*"provided" + 0.008*"invention" + 0.008*"polymers" + 0.008*"resin" + 0.008*"material" + 0.007*"also"



</div>


## Analysis & Improvements

In retrospect, Word2Vec & K-Means may have focused too much on the specific words themselves. In order to provide a search of similar patents, I might want to reduce the focus on particular words and capture more of the general uses. The technical words involved in the abstracts caused the relationships to be more complex than Word2Vec embeddings were able to capture. In order to increase the quality of the results, we would most likely need to manually create an extremely large equivalence class to replace technical names with “chemical” or another placeholder. In addition, a strict limitation on the number of word occurrences would probably have improved the generalizability of the word embeddings and an increased number of clusters. Adding additional clusters would have been optimal for this paper, but computational time was high when re-running models. 

Within the context of this paper, the LDA modeling provides the most useful output for exploration of similar patents. The LDA model clearly captures which topics are medical, production, acids, etc. in the descriptions. In addition, the summarization would enable an end-user to explore areas where the company may not hold a patent and explore patents that could pertain to company trade secrets. In addition, a significant amount of time was focused on combining data at a large level, where the data should have been reduced to focus on a specific type of patents. Without the data exploration and engineering that was performed, I would not have had as good of an understanding on patent structure and meaning. This lack of knowledge could negatively affect the results of the analysis.

## Summary

From the analysis and experiment, I propose the complexity and technical names of the words hampered the effectiveness of the Word2Vec and K-Means algorithm in gathering similar documents. In addition, an analysis on more clusters could have been utilized. In the context of this experiment, the LDA analysis with 25 topics would provide the best immediate solution to evaluate patents based on their similarity by estimating overall topics instead of focusing on individual words. From the LDA analysis, we know that the majority of the company patents fall within 3 topics, which gives additional clarity into where similar patents to the company would reside. As I continue to explore this model beyond the scope of this assignment, I plan to incorporate a dramatically wider set of testing parameters and formulating chemical equivalence classes to reduce the technical nature of the words in the corpus and improve upon model generalization. 