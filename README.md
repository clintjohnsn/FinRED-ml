# FinRED-ml : SVM based approach to FinRED

This repo contains python implementation of an SVM based approach to relation extraction in Financial Domain. It uses the dataset from the paper "FinRED: A Dataset for Relation Extraction in Financial Domain", 2022, Soumya Sharma and Tapas Nayak and Arusarka Bose and Ajay Kumar Meena and Koustuv Dasgupta and Niloy Ganguly and Pawan Goyal. Performance is compared with the models in the paper.

## Data
Dataset published in paper "FinRED: A Dataset for Relation Extraction in Financial Domain"

Soumya Sharma, Tapas Nayak, Arusarka Bose*, Ajay Kumar* Meena, Koustuv Dasgupta, Niloy Ganguly, Pawan Goyal.

Accepted as poster in The 2nd Workshop on Financial Technology on the Web (FinWeb), In conjunction with The Web Conference 2022

FINRed is a dataset for relation extraction tasks. It focuses on the relations between financial entities and is sourced from financial news and earnings call transcripts. The inter-annotator agreement, measured with Cohen’s Kappa, is 82.1% for the dataset. Given the unique terminology and context inherent in the financial domain, relation extraction models trained on general
domains may not effectively capture the nuances in financial text. Consequently, specialised relation extraction models tailored to the financial domain are crucial.

The dataset focuses on 29 relations in the financial domain, with 5700 samples for training, 1068 for testing, and 1007 for validation. It is worth noting that within the dataset there could be more than one relation to extract for each sentence.

### Data Sources

Earning Call Transcipts (ECT):
ECT have been scraped from seekingalpha.com.
ECT data link: https://drive.google.com/drive/folders/1-MLGFMRxWXDSp6Io9z9kwDKjB2XI-OQq?usp=sharing

### Dataset

Data drive link: https://drive.google.com/drive/folders/1-k5H79NkqzLkkF4KcndqxAPojnBbmqa4?usp=sharing

Train, Dev and Test splits have been provided for the FinRED dataset. It also has a relations.txt file containing the list of relations.

### Presentation

You can find slides presented during WWW'22 on the paper here: https://docs.google.com/presentation/d/19QFgA1XfQPf4Fx2FsuFjWxr7-MJwvo8zKLHCSbcN-8Y/edit?usp=sharing
The video associated with the presentation can be found here: https://drive.google.com/file/d/1pFNzJRyVvjq_UrIfiyEzL4BGvUDHLJdP/view?usp=sharing

### Publication

If you use the source code or data from this work, please cite our paper:

```
@inproceedings{sharma2022finred,
	author    = {Soumya Sharma, Tapas Nayak, Arusarka Bose, Ajay Kumar Meena, Koustuv Dasgupta, Niloy Ganguly and Pawan Goyal},
	title     = {FinRED: A Dataset for Relation Extraction in Financial Domain},
	booktitle = {Proceedings of The 2nd Workshop on Financial Technology on the Web (FinWeb)},
	year      = {2022}
}
```
## Methodology

### Feature Engineering
 
 1. **Lexical features** : This involved capturing entity distances, by computing the number of tokens between entities and encapsulating the relative order of appearance through the sign of the distance. Additionally, term frequency-inverse document frequency (Tf-idf) vectorisation was used on the sentences after they had been tokenised, lemmatised, and stop words and punctuation were taken out. N-grams of up to three terms were generated, and to manage the resulting high-dimensional and sparse space, only the top 3000 most frequent terms were retained for further analysis. 
2. **Syntactic features**:  This involved tagging each sentence with its appropriate part of speech, and then vectorising the resulting dictionary. This feature did not prove informative during validation, and so was dropped. This might be due to the inability of SVMs to capture sentence structure and dependency graph information.
3. **Semantic features**:  
	- Words located between entities were embedded using a pre-trained 300-dimensional **word2vec** model trained on Google News, leveraging a corpus of approximately 100 billion words. The contextual information of the phrase was captured through the calculation of the mean vectors of these embeddings. 
	- Entity neighbourhoods were defined by creating context windows with a window size of 3 tokens around the entities. The same word2vec model was then used to embed the words in these neighbourhoods. Then, the mean vector of these embeddings was calculated to contain the contextual information.
### Training & Hyperparameter Exploration 
The linear SVM model is trained in one-vs-rest classifier mode, wherein a binary classifier is trained for each of the 29 classes. To finetune the hyperparameters of the model, a grid search was run on a small subset of 200 samples with 5-fold cross-validation. Once the optimal hyperparameters were obtained, the model was trained on the training
set data and validated using the ’dev’ dataset. 

### Evaluation
After training the model, evaluation on the train data achieved a micro-averaged F1-score of 92.1% and 62.8% on the validation data, which suggests that overfitting in the model did occur but that was moderate generalisation to the data. The model was evaluated via micro-averaging the F1-scores and achieved a score of 62.9% on the test data.

### Results
While SVMs are reasonably effective in handling high-dimensional data, they can be problematic when dealing with sparse data, which is common in text vectorisation tasks. This may require careful preprocessing or dimensionality reduction techniques. They also inherently lack the capability to handle sequential data, such as text sequences, and graph representations of data, like dependency trees, without specialised feature engineering.
