# personalization-theory
 Group Members : **Archit Jain, Simran Lamba, Togzhan Sultan, TaeYoung Choi**

## Installation
```
pip install --user personalization-theory
```

## Dataset
Amazon product data: Video Games that contains 1,324,753 reviews for 50,953 products.


The video game categories that we decided to pick are PC, Mac, More Systems, Nintendo, PlayStation, multi support, Sony,
 Wii, and Xbox. The games that support different types of platforms were categorized as multi support. For simplicity,
 we did not distinguish different models for each platform. For example, Xbox means that a video game runs
 on one of the Xbox models such as Xbox 360, Xbox 360 S, Xbox 360 E, and etc. We eliminated all items that are outside
 of these categories, because they account for only a faction of data, and are not supported by any of the current
 platforms.

We assumed the reviews submitted after January 1st, 2011 were effective. The latest Nintendo model in 2009. The latest
Xbox model was released in 2010. Wii Family Edition was released in 2011, and the most recent version of PlayStation 3
was released in 2012, followed by PlayStation 4 in 2013. Any reviews submitted before these years seemed irrelevant, if
we were to give recommendations to current users.

We explored the distribution of users, who submitted reviews after January 1st, 2011. As seen in the histogram below, we have an extremely skewed dataset with 75% of users submitted only single review and 95% of users submitted less than two reviews. Furthermore, 99% of users submitted less than four reviews. If we were to sample 10,000 users using this highly sparse
 data, we weren't able to meet the requirement, which is the size of the dataset being less than 100 items.

<table class="image">
<caption align="bottom">Histrogram of the number of reviews submitted by users</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/byRatingNum.png" width="720"></td></tr>
</table>

However, we also did not want to treat heavy reviewers as same as inactive users, because these few heavy users create bigger
impacts than normal users do. Therefore, we adjusted our sampling method to sample users with single rating to account
 for approximately 25% of the data. By artificially feeding more data, we could better evaluate our model performances.
 Otherwise, all models perform poorly by having a n x n dataset with nearly n non-zero entries. As a result, we sampled 20,000 users from the population. First 5,000 users were sampled from the group of users that submitted less than 2 reviews. Next 5,000 users were sampled from the group of users that submitted between 2 and 4 reviews. Another 5,000 users were sampled from the group that submitted between 4 and 7 reviews. The last 5,000 users were the sample from the group that submitted more than 7 reviews.
 
After sampling users, we sampled items according to the distribution of the video game categories that we defined earlier. The histogram below represents the distribution of video game categories. For example, we randomly sampled 34 PC games, because the overall proportion of PC games in the population was 34%. Furthermore, we sampled according to the popularities of video games. For example, 70%, 85% and 90% of PC games received less than 3, 5 and 7 reviews, respectively. In order to overcome the sparsity issue within the items, we also adjusted the popularity measure. Finally, we sampled 9 random PC games with less than 3 reviews, 9 random PC games with between 3 and 5 reviews, 9 random games with between 5 and 7 reviews and 11 random games with more than 7 reviews. The resulting random 34 PC games consist of our PC game sample.

<table class="image">
<caption align="bottom">Histogram of the number of reviews submitted by users</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/category_distribution.png" width="720"></td></tr>
</table>

This sampling method resulted in the sample of 100 items and 2,916 users by incorporating the distribution of the level of activities of users, and the distribution of popularity and category of items.


## Objective

The objective of the first part of the project is to compare neighbor based and model based recommendation systems on the Amazon Video Game dataset. Accuracy and coverage in tandem hold the most business value for a company like Amazon. For instance, the least popular items tend to provide the most profit if bought, due to higher margins. However, they are usually not recommended over popular items, as most recommender systems still put large focus only on accuracy, targeting only low hanging fruits. We want the best mix of accuracy and converge, to not only provide the best recommendation, but from a business perspective, the most profitable recommendations

Accuracy of recommendations is of paramount importance, and we will be using Root Mean Square Error (RMSE) and Mean Absolute Error  (MAE). Moreover, the efficiency of the code was another key metric to be measured. We used multiple samples and multiple hyper-parameters for each recommendation model to compare obtain the best accuracy scores. Over and above that, we have attempted to go beyond accuracy to calculate the coverage of all of our models and compare them. The metric for coverage basically comprises of the total unique top - N recommendation given by each model over the test set, over the total number of items in the sample (100 items). 

## Algorithms

We have primarily used Neighbor based and Model Based Recommendation systems to obtain recommendations for our data. We utilized scikit-surprise 1.0.4 library in python for the purposes of creating recommendations.

### Neighbor based Systems:

For neighbor based systems, we utilized memory based collaborative filtering algorithms that are directly derived from a basic nearest neighbors approach. For each of these algorithms, the actual number of neighbors that are aggregated to compute an estimation is necessarily less than or equal to k. 

All neighbor based algorithms use a similarity measure to estimate a rating. The way they can be configured is done in a similar fashion as for baseline ratings: you just need to pass a sim_options argument at the creation of an algorithm. This argument is a dictionary with the following (all optional) keys:

*'name': The name of the similarity to use, as defined in the similarities module. Default is 'msd (mean square distance)'. The other measures include cosine similarity and Pearson coefficient.

<table class="image">
<caption align="bottom">Mean Square Distance</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/MSD.png" width="720"></td></tr>
</table>

<table class="image">
<caption align="bottom">MSD Similarity</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/MSD%20Similarity.png" width="720"></td></tr>
</table>

<table class="image">
<caption align="bottom">Cosine Similarity</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/Cosine.png" width="720"></td></tr>
</table>

<table class="image">
<caption align="bottom">Pearson Similarity</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/PearsonS.png" width="720"></td></tr>
</table>

*'user_based': Whether similarities will be computed between users or between items. This has a huge impact on the performance of a prediction algorithm. Default is True. For all the neighbor based recommendations algorithms, we used item based similarity, as it was providing with a better accuracy metrics as compared to user based metrics, as expected for a sparse data such as this one with a huge difference between number of users and items.

*'min_support': The minimum number of common items (when 'user_based' is 'True') or minimum number of common users (when *'user_based' is 'False') for the similarity not to be zero. Simply put, if |Iuv|<min_support|Iuv|<min_support then sim(u,v)=0sim(u,v)=0. The same goes for items. We have used multiple values of min_support, and displayed 1 and 5 in the code.

*'shrinkage': Shrinkage parameter to apply (only relevant for pearson_baseline similarity). We have used the Default shrinkage which is 100.

The important thing to note is that in the code is that we are using the same range of hyper parameters for the three KNN based algorithms. For all KNN Basic, KNN With Means and KNN With ZScore, we are calculating cosine, msd and pearson similarity, while ranging k from 10 to 30 and min_support from 1 to 5.

The key algorithms used are described below:

* KNN Basic - It is a a basic collaborative filtering algorithm. The prediction r̂ is set as:
<table class="image">
<caption align="bottom">KNN Basic</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/KNNBasic.png"></td></tr>
</table>

* KNN With Means - The prediction r̂ is set as:
<table class="image">
<caption align="bottom">KNN With Means</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/KNNWithMeans.png"></td></tr>
</table>

* KNN With Z-Score - The prediction r̂ is set as:
<table class="image">
<caption align="bottom">KNN With Z-Score</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/KNNWithZScore.png"></td></tr>
</table>

### Model Based: 
For Model Based implementation of the recommender system we used matrix factorization technique and primarily focused our attention on SVD, SVD++ and NMF. To implement those algorithms, we used scikit surprise python package, which already has all the predefined procedures. 

### 1.) SVD
The famous SVD algorithm, as popularized by Simon Funk during the Netflix Prize. When baselines are not used, this is equivalent to Probabilistic Matrix Factorization.
<table class="image">
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/svd_screenshot_1.png"></td></tr>
</table>

If user u is unknown, then the bias b_u and the factors p_u are assumed to be zero. The same applies for item i_i with b_i and q_i.To estimate all the unknown, we minimize the following regularized squared error:
<table class="image">
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/svd_screenshot_2.png"></td></tr>
</table>

where e_ui=r_ui−r̂_ui. These steps are performed over all the ratings of the trainset and repeated n_epochs times. Baselines are initialized to 0. User and item factors are randomly initialized according to a normal distribution, which can be tuned using the init_mean and init_std_dev parameters.

You also have control over the learning rate γ and the regularization term λ. Both can be different for each kind of parameter (see below). By default, learning rates are set to 0.005 and regularization terms are set to 0.02.
 
 
Parameters:<br />
n_factors – The number of factors. Default is 100. <br />
n_epochs – The number of iteration of the SGD procedure. Default is 20.<br />
biased (bool) – Whether to use baselines (or biases). See note above. Default is True.<br />
init_mean – The mean of the normal distribution for factor vectors initialization. Default is 0.<br />
init_std_dev – The standard deviation of the normal distribution for factor vectors initialization. Default is 0.1.<br />
lr_all – The learning rate for all parameters. Default is 0.005.<br />
reg_all – The regularization term for all parameters. Default is 0.02.<br />
lr_bu – The learning rate for bubu. Takes precedence over lr_all if set. Default is None.<br />
lr_bi – The learning rate for bibi. Takes precedence over lr_all if set. Default is None.<br />
lr_pu – The learning rate for pupu. Takes precedence over lr_all if set. Default is None.<br />
lr_qi – The learning rate for qiqi. Takes precedence over lr_all if set. Default is None.<br />
reg_bu – The regularization term for bubu. Takes precedence over reg_all if set. Default is None.<br />
reg_bi – The regularization term for bibi. Takes precedence over reg_all if set. Default is None.<br />
reg_pu – The regularization term for pupu. Takes precedence over reg_all if set. Default is None.<br />
reg_qi – The regularization term for qiqi. Takes precedence over reg_all if set. Default is None.<br />
verbose – If True, prints the current epoch. Default is False.


### 2.)SVD++
The SVD++ algorithm, an extension of SVD taking into account implicit ratings.
<table class="image">
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/svdpp_screenshot_1.png"></td></tr>
</table>

Where the y_j terms are a new set of item factors that capture implicit ratings. Here, an implicit rating describes the fact that a user uu rated an item j, regardless of the rating value.

If user uu is unknown, then the bias bubu and the factors pupu are assumed to be zero. The same applies for item i with b_i, q_i and y_i.  Just as for SVD, the parameters are learned using a SGD on the regularized squared error objective.

Baselines are initialized to 0. User and item factors are randomly initialized according to a normal distribution, which can be tuned using the init_mean and init_std_dev parameters.

You have control over the learning rate γ and the regularization term λ. Both can be different for each kind of parameter (see below). By default, learning rates are set to 0.005 and regularization terms are set to 0.02.

Parameters:<br />
n_factors – The number of factors. Default is 20.<br />
n_epochs – The number of iteration of the SGD procedure. Default is 20.<br />
init_mean – The mean of the normal distribution for factor vectors initialization. Default is 0.<br />
init_std_dev – The standard deviation of the normal distribution for factor vectors initialization. Default is 0.1.<br />
lr_all – The learning rate for all parameters. Default is 0.007.<br />
reg_all – The regularization term for all parameters. Default is 0.02.<br />
lr_bu – The learning rate for bubu. Takes precedence over lr_all if set. Default is None.<br />
lr_bi – The learning rate for bibi. Takes precedence over lr_all if set. Default is None.<br />
lr_pu – The learning rate for pupu. Takes precedence over lr_all if set. Default is None.<br />
lr_qi – The learning rate for qiqi. Takes precedence over lr_all if set. Default is None.<br />
lr_yj – The learning rate for yjyj. Takes precedence over lr_all if set. Default is None.<br />
reg_bu – The regularization term for bubu. Takes precedence over reg_all if set. Default is None.<br />
reg_bi – The regularization term for bibi. Takes precedence over reg_all if set. Default is None.<br />
reg_pu – The regularization term for pupu. Takes precedence over reg_all if set. Default is None.<br />
reg_qi – The regularization term for qiqi. Takes precedence over reg_all if set. Default is None.<br />
reg_yj – The regularization term for yjyj. Takes precedence over reg_all if set. Default is None.<br />
verbose – If True, prints the current epoch. Default is False.

### 3.)NMF

A collaborative filtering algorithm based on Non-negative Matrix Factorization.
This algorithm is very similar to SVD. The prediction r̂ ui is set as:
<table class="image">
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/NMF_screenshot_1.png"></td></tr>
</table>

where user and item factors are kept positive. 
The optimization procedure is a (regularized) stochastic gradient descent with a specific choice of step size that ensures non-negativity of factors, provided that their initial values are also positive.

<table class="image">
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/NMF_screenshot_2.png"></td></tr>
</table>
This algorithm is highly dependent on initial values. User and item factors are uniformly initialized between init_low and init_high. 

 
Parameters:<br />
n_factors – The number of factors. Default is 15.<br />
n_epochs – The number of iteration of the SGD procedure. Default is 50.<br />
biased (bool) – Whether to use baselines (or biases). Default is False.<br />
reg_pu – The regularization term for users λuλu. Default is 0.06.<br />
reg_qi – The regularization term for items λiλi. Default is 0.06.<br />
reg_bu – The regularization term for bubu. Only relevant for biased version. Default is 0.02.<br />
reg_bi – The regularization term for bibi. Only relevant for biased version. Default is 0.02.<br />
lr_bu – The learning rate for bubu. Only relevant for biased version. Default is 0.005.<br />
lr_bi – The learning rate for bibi. Only relevant for biased version. Default is 0.005.<br />
init_low – Lower bound for random initialization of factors. Must be greater than 0 to ensure non-negative factors. Default is 0.<br />
init_high – Higher bound for random initialization of factors. Default is 1.<br />
verbose – If True, prints the current epoch. Default is False.<br />

## Evaluation

### Hyper-parameters and their effect on accuracy
We varied hyper parameters for all models to check the effect on RMSE. Here is the relationship between RMSE and hyper parameters for SVDpp and KNNBasic (as they had the best scores for model based and neighbor based methods):

<table class="image">
<caption align="bottom">SVDpp: RMSE vs. Hyper parameters</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/SVDpp RMSE.png"></td></tr>
</table>

<table class="image">
<caption align="bottom">KNNBasic: RMSE vs. Hype rparametres </caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/KNNBasic RMSE.png"></td></tr>
</table>


### Accuracy
To determine accuracy, we used evaluate() and gridsearch() from the surprise package:http://surprise.readthedocs.io/en/stable/evaluate.html?highlight=evaluate

We used RMSE for comparison of accuracy of all the models. The code used is located in the file:https://github.com/taeyoung-choi/personalization-theory/blob/master/accuracy_coverage.py


Following are the figures for all the models:

<table class="image">
<caption align="bottom">RMSE values</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/model_accuracy.png"></td></tr>
</table>


### Coverage

For coverage, we used top n (where n=1,3,5) predictions to calculate the total number of unique recommendations over the test set using all algorithms. The above number was then divided by the total number of items in the sample and thus it's percentages on the vertical axises.

<table class="image">
<caption align="bottom"> Coverage: Top 1 (% vs. models) </caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/top1.png"></td></tr>
</table>

<table class="image">
<caption align="bottom"> Coverage: Top 3 (% vs. models)</caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/top3.png"></td></tr>
</table>

<table class="image">
<caption align="bottom"> Coverage: Top 5 (% vs. models) </caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/top5.png"></td></tr>
</table>

As we can observe, the coverage % does not improve much between top 3 and top 5 models.


### Run Time

We utilized time() to calculate the training running time and test running time on the code to test the efficiency of the code, as it will play an important role in part two of the project. We used the following code:https://github.com/taeyoung-choi/personalization-theory/blob/master/Runtime_Scaling.ipynb

The run times of all the algorithms are as follows:

<table class="image">
<caption align="bottom"> Run Time </caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/Runtime%20plot.png"></td></tr>
</table>

As we can observe, the test running time is comparably higher for neighbor based methods than model based methods. This is a key consideration that you have to keep in mind while adding a new user, as training of data sets can be done online, but test running times should be low for the method chosen.


### Samples Scaling

We also utilized multiple samples of the data of different sizes by changing the number of users in the sample.
The following code was utilized to perform the scaling: https://github.com/taeyoung-choi/personalization-theory/blob/master/runtime_scalling.py

This is the comparison of RMSE scores for all models, at different sample sizes:

<table class="image">
<caption align="bottom"> RMSE vs. Sample Size </caption>
<tr><td><img src="https://github.com/taeyoung-choi/personalization-theory/blob/master/plot/Scaling%20plot.png"></td></tr>
</table>

## Conclusion

Based on the evaluations above, we think that SVD++ is the best model to implement in this case, because it has the best accuracy and does better than the baseline. Even though running time for training is relatively big and the testing time is about the average across all models, we think that training could be done offline and thus overall running will time not be affected. Furthermore, the Coverage for SVD++ is close to 30%, which we find sufficient for this data. 

As we know 90% of users rated only one product and the baseline algorithm gives average of all the ratings. In this case for our data, it means that for 90% of users, Baseline gives the same result as the actual rating. Thus, it is expected RMSE of the Baseline to be so small and beating the accuracy of the Baseline. It means we are doing predictions better for more than 90% of users.

However, even though SVD++ works best for this particular sample, we feel that a from a practical and business perspective, a hybrid approach that includes content based methods combined with collaborative filtering will be a more useful approach moving forward. A lot of information about video games is lost if only the above approaches are used in isolation, without focusing on content.

## Misc
### Outline for the second part
Dataset: 
-Include all observations from the dataset used for this projects
-Add related datasets. 
     As we working with Amazon Video Game dataset, we thought that we could add Electronics dataset from the same Amazon Merchandise Dataset( http://jmcauley.ucsd.edu/data/amazon), which will allow us to reccomend certain type of graphic carts, keybaords, etc. suitable for that particular type of the game.
-Combine datasets. 
     Adding new datasets means we should find smart ways to combine them. As one is able to observe, video games dataset actually contains platforms on which they could be played and based on that we want to reccomend a user a certain type of electronics. Furthermore, we want to make use of the 'also bought' meta description of the item, which tells what other products were bought with thic particular product and identify those 'also bought' links with video games and electronics datasets. 
     
     Timeline: 
 - Nov 7
    Discuss datasets and ideas for the second part. HW: do research of models and packages that could be used till next meeting
 - Nov 19
    Discuss models that could be used and divide the work between people in the team. HW:research maths behind the models chosen, install all necessary software packages till next meeting, play with code and simpler versions, while datasets would be prepared till next meetin
 - Nov 22 
     After Datasets are prepared, discuss findings of the closer research and datapreparing. HW: Prepare all the codes and the models running till next meeting
 -Nov 25    
     Discuss results of the running of the models, try to tune them and find solutions to the problems that came up during running of the models. HW: tune models
 - Nov 29 
      Dicuss all the results that came up and any other steps that could be introduced

 
      
     
     

Model: 
We want to use parallel hybrid model, which includes SVD++, Approximate nearest neighbors, recommender for novelty or serendipity, ALS collaborative filtering using Spark and Frequent pattern mining / Association rules using Spark
