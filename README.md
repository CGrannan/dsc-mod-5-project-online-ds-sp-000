# Predicting Hits and Flops with Spotify

## Abstract

Marketing tracks is an expensive process and marketing failed singles can represent a huge loss for a recording label. For this project, I am looking to see if there are identifiable features of songs that indicate whether they will be hits or flops. The end goal of this project is to provide record labels a tool that will indicate whether tracks are worth heavy investment. I will be training five types of classifiers for this task: Logistic Regression, K-Nearest Neighbors, Support Vector Machine, Random Forest and Adaboost. In addition to the baseline for these models, I will also attempt some feature selection techniques using Principal Component Analysis and Permutation Importance.

The data for this project consists of 32,426 tracks obtained through the [Spotipy](https://spotipy.readthedocs.io/en/2.17.1/) wrapper. You can find the complete dataset on [kaggle](https://www.kaggle.com/theoverman/the-spotify-hit-predictor-dataset). Each record of a song contains information on the danceability , energy, key, loudness, mode, speechiness, acousticness, instrumentalness, liveness, valence, tempo, duration_ms, time_signature, chorus_hit, and sections. I won't go over what each column entails in detail, but there is a seperate README file inside the dataset that explains what each column entails and how some features were calculated.

When all models were tested and analyzed, two models were selected as the most reliable. The SVM model is able to separate hits and flops about 80% of the time and is therefore the most reliable model overall. However, all of our models had a tendency toward a high false positive rate (mislabelling flops as hits) representing a potentially high cost of using these models to recording labels. The model that had the lowest false positive rate was the random forest, showing that it is the best model in a conservative sense. Feature selection did not have a noticeable effect on the results of the models.


## Data Preprocessing & EDA

We made a few interesting decisions during data cleaning. We removed two instances where there was no time signature for a track, and we removed a few outliers from the duration_ms, chorus_hit, and sections columns. After removing these outliers we still had over 31,000 tracks. At this point, we explored some relationships within the data. First we looked at the distribution of hits by decade.

![](Images/hits_by_decade.png)

It seems like we have many more hits represented in the earlier decades. We also checked some correlations between variables. We had two strong negative correlations and two strong positive correlations. First, here are the negative correlations:

![](Images/accousticness_energy.png)

![](Images/accousticness_loudness.png)

We can see that 'accousticness' correlates negatively with both energy and loudness. This is understandable as acoustic music usually tends to be softer and quieter compared to music with electric imstruments. Here are the two positive correlations:

![](Images/loudness_energy.png)

![](Images/duration_sections.png)

Here we see two more relationships that make sense. Our definition of energy includes loudness, so naturally they would correlate. Similarly, a longer track is expected to have more sections. At this point we were ready to start building classifiers.

## Modeling

We decided to use a variety of classifiers for this project, and fine-tune each of them as much as possible before selecting our final model. We used logistic regression, k-nearest neighbors, support vector machine, random forest, and adaboost models. To begin modeling, we established a baseline for each of these types of classifiers by using the default parameters. Of these models, the random forest had the best accuracy at 79.9%. To take this further we attempted to use principal component analysis to reduce dimensionality. When we ran the models on the PCA transformed data, all models produced worse results. At this point we decided to drop the PCA approach. Next, we used GridSearchCV to fine-tune the parameters for each model to get the best result we could. Once we had the best possible model for each approach, we inspected the confusion matrixes and areas under the ROC curve. Here are the resulting matrices for each model.

Logistic Regression:

<p align="center">
  <img width="460" height="300" src="Images/logreg_matrix.png">
</p>

K-nearest neighbors:

<p align="center">
  <img width="460" height="300" src="Images/knn_matrix.png">
</p>

Support vector machine:

<p align="center">
  <img width="460" height="300" src="Images/svm_matrix.png">
</p>

Random forest:

<p align="center">
  <img width="460" height="300" src="Images/forest_matrix.png">
</p>

Adaboost:

<p align="center">
  <img width="460" height="300" src="Images/ada_matrix.png">
</p>

All models have good accuracy and precission, but they also all show a tendency to mislabel flops as hits resulting in a relatively high false positive rate. The random forest model has the lowest false positive rate, but even still shows this tendency.

And here is the training and testing ROC curves plotted:

![](Images/train_roc.png)

![](Images/test_roc.png)

The models all perform well, but with higher false positive rates than I would like. The SVM model is slightly more accurate with an AUC of .80, but the random forest model has a lower false positive rate. Ultimately, I think the SVM model is the better model unless we are looking to be conservative, in which case the random forest is better. Finally, we used permutation importance to determine the weights of our features. Here are the weights for our two best models:

SVM features:

<p align="center">
  <img src="Images/svm_features.png">
</p>

Random forest features:

<p align="center">
  <img src="Images/forest_features.png">
</p>

The two models have similar orders for features, especially at the top of the list, but ascribe fairly different weights. We should note that 'instrumentalness' and 'accousticness' are the two best features for both models.

## Summary

We built two models that we can use to predict if a song will be a commercial hit. Our SVM model has a higher overall accuracy, correctly classifying songs about 80% of the time. However, if we are trying to conservatively predict flops so as to avoid risky investments, then we should be using the random forest classifier. This model had the lowest rate of false positives, meaning we have a lower chance of investing in marketing campaigns that are destined to fail. Further more, when looking at what makes a commercially successful song, we were able to distinguish key features used by both models. Instrumentalness is key it seems as both models placed that as the strongest feature. Accousticness, danceability, energy and duration round out the rest of the top 5 in both models, though not in the same order. When deciding on which tracks to support, we should be keeping these key factors in mind.

## Future Work

There are a few ways that I think we can expand on this project. First, we can narrow the focus of our predictions down to just classifying tracks released in the last 5 or 10 years. This will allow us to focus on the market demands of the current generation, rather than looking at overarching trends over the last several decades. Second, I would look at testing this model by genre of music. Will we have similar results when comparing country music and opera music? Perhaps we can get a better accuracy if we train and use our model on individual genres, rather than all tracks.

## Files

Here is a brief overview of the structure of this repository.

Images contains several graphs that are saved as png files.

Archive is a zip folder containing all track information and a README file that includes column descriptions

Classifier_functions is a pyscript with several helper functions used in analyzing the data

Classifier_modeling is the notebook where we clean the data, model our classifiers and analyze results

Spotify_classifiers_presentation is an example non-technical presentation for this project