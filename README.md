# Market-Analysis
Creating customer segments using unsupervised learning
The data comes from customers.csv
The visuals code from visuals.py was provided by Udacity as part of the Machine Learning Engineer Nanodegree program.


Exploring the data using data.describe:

![image](https://cloud.githubusercontent.com/assets/24555661/22263747/b8bb1592-e232-11e6-9d56-be4c9165b7ae.png)

Given the three samples I selected (samples with indices 405, 210, and 13).

![image](https://cloud.githubusercontent.com/assets/24555661/22263807/f00cd4d6-e232-11e6-94e5-67b4cd16f626.png)

Here are some possibilities of the types of establishments that these three samples might represent:

Mean: Fresh: 12k, milk: 5700, grocery: 8k, frozen: 3k, detergents_paper: 3k, delicatessen: 1500 Max: 112k, Milk: 73k, grocery: 93k, frozen: 61k, detergents_paper: 41k, delicatessen: 48k

The first establishment (index 0) seems to have relatively low costs overall. The costs in each category are much lower overall than the total mean from the sample, which leads me to believe that the establishment might include some retailer which is not as involved in food-related products. However, even though the amount is still lower than the average, the frozen category for the first establishment is still much higher than the minimum value of 25, which implies that this establishment is somehow involved in frozen foods. That means that this establishment might be some smaller fast food restaurant that deals mostly through heating and serving frozen food.

The second establihsment also has relatively low costs across the board, except for fresh and delicatessen. In both of these cases, it surpasses the mean, yet does not come close to approaching the maximum values for those categories. The data point might be representing some establishment which resembles a restaurant which primarily serves foods. The costs for milk, grocery, frozen, and detergents are below average, with fresh and delicatessen well above average.

Finally, the third data point likely represents some food store, as it has values above average across every feature. It's not a particularly large store, as all of the values are still far from the maximum values, but every feature is well-above the overall averages across the dataset which implies a general-purpose retailer, and not a restaurant/cafe.

Next, a label is removed to attempt to predict the value of that label. The label removed is "milk". 

 Interestingly, by changing the random_state parameter in train_test_split, I was able to achieve an R^2 score of 0.4. Although it's not a strong score, it implies that the model was able to somewhat fit to the data. However, by simply changing the random seed, my results were very inconsistent which generally implies that the model fails to fit the data. Using a random_state of 7, I received an R^2 score of -2.16, which implies there was no fit whatsoever. Thus, we cannot draw any conclusions about proportionality of spending patterns in customers.
 
This also shows that the milk feature is independent of the other features -- as in the milk spending does not exist as some linear combination of the other features, which implies that it should be left into our analysis of customer spending.

Below is the scatter matrix generated by Market-Analysis.py which displays the feature distributions prior to scaling:

![image](https://cloud.githubusercontent.com/assets/24555661/22263834/108c2c8e-e233-11e6-8ae3-d055b556b706.png)

It seems as though there might exist a correlation between detergents_paper and grocery. In the scatter matrix, this entry is located at (3, 5). However, by looking at the general trends in the data from the above questions (which look at the mean, max, min, and other metrics), it's clear that many of these features are scaled very differently. Most of the data is sitting near the origin of the scatter plot, which implies that it might be necessary to first scale the features before looking for correlations in the data.

The data is not normally distributed as they exist right now. Along the diagonals of the above graph matrix, it's clear that the correlation between a feature and itself appears to be distributed far skewed towards very low values.

Because  this data is likely not normally distributed, and because the mean and median vary significantly, we ought to apply nonlinear scaling to the dataset. The data is scaled using the natural logarithm. Below is the statistical information after a natural logarithm scale is applied to the data:

![image](https://cloud.githubusercontent.com/assets/24555661/22263953/78d2a93a-e233-11e6-9d43-f7bcf9bde840.png)

And here is the revised scatter matrix after the feature scaling of the dataset:

![image](https://cloud.githubusercontent.com/assets/24555661/22263969/9114b15a-e233-11e6-9ad2-d63054394e1f.png)

For a small snapshot of what the new data looks like transformed, below is what the three sample points looks like post-transformation: 

![image](https://cloud.githubusercontent.com/assets/24555661/22264096/13104200-e234-11e6-8b57-9c64bfef7f48.png)

After outliers are detected and removed, the next step is to apply Principal Component Analysis to the dataset. By setting the parameter n_components to 6, we can view the 6 new principal components:

![image](https://cloud.githubusercontent.com/assets/24555661/22264195/52e314b6-e234-11e6-8969-830217ff92a3.png)

70.68% of the variance in the data is explained by the first and second principal component. Adding in the next two principal components explains 93.11% of the total variance within the data. The first dimension vector primarily points towards detergents_paper, Milk, and Grocery with large positive weights. It also correlates with a decrease in fresh and frozen features. This might imply that fresh and frozen foods are purchased separately than more general retail objects.

The next largest principal component focuses on fresh, frozen, and delicatessen, with large positive weight values. The third most variant principal component vector focuses on frozen and delicatessen. It is also worth noting that in this third principal component, "fresh" has a very large negative weight, implying customers who buy frozen and delicatessen generally do not spend much on fresh.

Finally, the fourth dimension represent primarily frozen with a little influence towards detergents_paper. Interestingly, this fourth dimension also has a large negative weight components representing Delicatessen. Thus, customers who spend a lot on frozen generally do not spend much on delicatessen.

Because the first component describes the features that contain the highest variance, we can hypothesize that customers who who purchase high values of detergents_paper, milk, and grocery purchase a lot of these categories, whereas customers who purchase low values of detergents_paper, milk, and grocery purchase very little retail goods in general. This might imply a large separation between retail stores and restaurant-centered purchases. It seems as though customers are either buying detergents/milk/grocery or fresh/frozen/delicatessen. Even further, customers who buy a lot of frozen and delicatessen are even more separated from fresh.

Here are the three samples as expressed in the six principal components:

![image](https://cloud.githubusercontent.com/assets/24555661/22264245/80320242-e234-11e6-81e7-470ded2ce07c.png)

Because so much of the variance of the data can be seen in the first two principal components, and because two dimensions can easily be plotted, we can now visualize our data in the first two principal components. In addition, the biplot shows the projection of the original features:

![image](https://cloud.githubusercontent.com/assets/24555661/22264371/ecaeccfc-e234-11e6-94b7-3247a37de1f4.png)

Now that the data has been plotted in the first two principal components, we can now cluster the data to finally create our market segments. First, to give us some intuition of how many clusters to create, we can use a Silhouette Score, which gives us a measure of how similar a data point is to its assigned cluster. A score of 1 represents high similarity. Below is a visualization which shows the relationship between the number of clusters and the associated silhouette score. Note that in general, as the number of clusters increases, the score will also increase.

![image](https://cloud.githubusercontent.com/assets/24555661/22265054/a1e35104-e237-11e6-835e-2b8c51e96a6e.png)

Because two clusters still provides good results, we will stick to the parameter n_clusters = 2. We can now visualize all of the data points categorized by the two clusters. In addition, the three samples will be drawn as x's. 

![image](https://cloud.githubusercontent.com/assets/24555661/22265111/d169e64a-e237-11e6-84ca-7e4debc07317.png)

I split the data into two primary segments (labelled cluster 0, and cluster 1). The segments are visualized in two dimensions. The first dimension implies that the three features with the highest variance are Milk, Grocery, and Detergents_Paper. The customer segment that correspond to higher values in the first dimension is segment 0, and the customer segment that corresponds to lower values is segment 1.
So visually, it was clear that we had the following segment distributions:

Segment 0: Segment 0 has higher values in the first dimension. Recall, dimension 1 primarily represented high values of milk, grocery, and detergents_paper, with negative values of fresh and frozen. Therefore we can claim that customers in segment 0 generally have higher purchase volumes of milk, grocery, and detergents_paper and lower purchase volume with fresh and frozen.

Segment 1: Lower values in the first dimension. Thus, because segment 1 represents a smaller dimension 1, we can conclude that they generally have low values of milk, grocery, and detergents_paper spending with higher values of fresh and frozen.

Sample point 0: According to my answer above, I predicted that the first sample might represent some smaller fast food restaurant that deals mostly through heating and serving frozen food.

Based on my customer segments, they fall under segment 1, which corresponds to higher values of fresh and frozen, which supports my original prediciton that this point might represent some type of restaurant.

Sample point 1: I originally predicted that the second sample point might represent a restaurant that serves food.

According to my segment analysis, the second data point falls under segment 1, which again corresponds to higher spending in fresh and frozen, and lower spending with milk, grocery, and delicatessen. Again, this segment might represent some type of restaurant that serves food. For this data point, my prediction was spot-on correct!

Sample point 2: Finally, I originally predicted that the third sample point might represent some food store or a "general-purpose retailer".

According to my segment analysis, it falls under cluster 0 which is the segment which has higher spending in milk, grocery, and delicatessen which might align with spending habits of retail stores that sell food, rather than a restaurant that prepares and sells cooked meals. Again, my prediction was correct!

Overall, all of my predictions were correct (or regarding sample point 0, partially correct). When I originally made my predictions, I mainly compared the features to the statistical analysis (mean, max, and min), which helps explain why my predictions were somewhat accurate. However, by limiting my categories to 2 main segments, it becomes much easier and generalizable across many more data points.
