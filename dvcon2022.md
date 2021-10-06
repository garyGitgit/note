```
Find a Needle in a Haystack: A Novel Log Analysis Method with Test Clustering in Distributed Systems
Jin Choi1, Sangwoo Noh2, Hanna Jang3, Seonghee Yim4, Seonil Brian Choi5
(1jjin.choi; 2sangwoo.noh; 3hn01.jang; 4sh.yim; 5seonilb.choi@samsung.com)


Abstract- As the complexity and number of gates in SOC increase, the cost for verification also increases. In such a trend, verification engineers are struggling with boring and repetitive debugging process, such as debugging similar tests with same errors in very limited resources, i.e., time and licenses. In this paper, we propose a novel approach of test prioritization based on clustering algorithms. The proposed algorithm is based on components from terabytes of simulation logs. Furthermore, to develop and deploy machine learning models with millions of data, we propose the system architecture to deploy machine learning model as a service in design verification process.

I.   INTRODUCTION

Demands for SoC have exponentially exploded not only in the mobile phone industry but also other industries including IoT and automotive industry. With expansion, the market requires SoC designers and engineers to develop a wider spectrum of chips with more complex design. Moreover, the period of the development is getting shorter with bug-free design. In such trend, the conventional verification method has its limitation to less sustainable and less scalable methodologies, and important missions are given to SoC designers and engineers to innovate the development process by integrating machine learning and big data into hardware industry. 

One of the important missions is to reduce debugging process in design verification. The process of design verification accounts for 60~70% of the entire SoC development process. There have been many efforts to reduce cost for design verification, however, verification engineers are still struggling with boring and repetitive debugging process, such as debugging common errors on different tests, or finding important errors behind common or less important errors. 

In this paper, we propose a novel approach of testcase prioritization to reduce time and cost for design verification. The goal of this paper is to reduce bug localization time by reordering testcases. The proposed algorithm prioritizes testcases based on features extracted from terabytes of simulation logs. This paper contributes to following categories:
-	Testcase clustering algorithm based on similarity of components in simulation logs
-	Prioritization algorithm for sampling representative testcases
-	System architecture for application of machine learning models in design verification industry

First, we propose a component-based clustering algorithm. Components are elements extracted from simulation logs and indicate potential features that may affect function of SoC. They include Task, Class and Sequence from System Verilog and error messages such as tool errors or user defined errors. The clustering algorithm considers these components as features of clusters, and the distance of  two points represent how common components are shared between testcases. The proposed algorithm compares various clustering algorithms to find the optimal algorithm based on components from simulation logs, and selects one for the next prioritization algorithm. 

Second, we propose the testcase prioritization algorithm. After clustering, this algorithm samples testcases which represents each cluster, and these selected testcases run with top priority. The algorithm reduces time and cost by reordering testcases to find distinct errors as soon as possible with least resources. This paper compares various algorithms to sample representative testcases from each cluster and selects one with the highest value and best performances. 

Finally, we propose the system architecture for application in the industry. The system architecture shows that the proposed algorithm is developed for not only research but also application in the industry. It enables the machine learning model to be updated and deployed in a stable manner and in a timely manner. This paper introduces how we designed automated pipelines for machine learning models and improved the system by using software engineering techniques such as in-memory models, design patterns distributed systems. 

Backgrounds introduce basic concepts of clustering algorithms, prioritization algorithms and software engineering. Algorithms describe the comparison of candidate algorithms and the proposed algorithm for testcase clustering and prioritization. Architecture explains the proposed automated workflow of machine learning development and system architecture. Result shows the result of the proposed algorithm and system architecture in a real design verification process. Two projects, a small one and a big one, are targeted to validate the proposed methods in the industry.

II.   BACKGROUNDS
Clustering Algorithms 
K-means clustering is one of the most widely used clustering algorithms in machine learning. This algorithm first picks centroids of clusters randomly and divides data into given K clusters. Next, another centroid is picked in each cluster, and this process is repeated until there is no change in clusters. DBSCAN algorithm is introduced to find appropriate clusters in data with high density. DBSCAN algorithm considers the density by using given distance, epsilon € and minimum number of points (m). OPTICS algorithm is used without epsilon € and appropriate for data with diverse density..

Weighted K-means clustering algorithm improves simple K-means clustering algorithm. In the original K-means algorithm, each point is contributed equally, and this sometimes results in low density in a cluster. Even if a specific point should be included into a cluster, it can be included into another cluster when new data is given. Here, weighted K-means clustering algorithm prevents this problem. When a specific feature has larger impact on clustering, weights can be given to the feature. This algorithm is useful when distances between points in the cluster should be closer in its cluster and farther to other clusters

Regardless of whether weights are given or not, clustering algorithms can be evaluated with evaluation algorithm such as Silhoutte and Elbow method. The result of Silhouette indicates how clusters are well performed with the given clustering algoriht, and the result of Elbow method shows the optimal K value in K-means clustering algorithm. In this paper, we first compare 3 clustering algorithms without weights and 3 clustering algorithms with weights, and select one algorithm which maximizes the result of Silhoutte method, and the optimal K-value is chosen by Elbow method.

Testcase Prioritization
There have been many researches related to testcase prioritization. 
[References] …

In this paper, we propose testcase prioritization algorithms based on clustering algorithms. After clustering, testcases are prioritized and representative testcases with higher priority are sampled in each cluster. We compare 4 algorithms which gives higher priority on testcases with: closest to the centroid, farthest to the centroid, random and highest score. The score is calculated considering run time, number of appearance, etc. The details about the score are described in Algorithms.

Software System Architecture
Software system architecture is of large importance in a successful development of machine learning. Even if a good machine model is developed, it is of no use when the model remains in local environment. Moreover, even if the development process is not automated, the machine learning model is hard to be updated with continuous data stream. In this paper, we use software techniques such as in-memory model, distributed systems and automated machine learning model development pipelines. These are basic software concepts to design system architecture. 

First, the concept of in-memory is a method to load models in memory. As memory access is much faster than disk access, this method is widely used to improve speed in the system. For instance, Spark, an open source for distributed systems, improved data access speed compared to Hadoop, widely well known distributed file system. 

Second, the distributed systems also improve data analyzing and processing speed. As clustering algorithms, such as K-means clustering, are appropriate target for parallel processing distributed systems. By using both multithreading and distributed systems, the system can maximize its performance to process large dataset such simulation logs in design verification.

Finally, automated machine learning development pipeline improves system. As it is a boring task to find out optimal hyper parameter, for example, the number of cluster K, in case of K-means clustering, this can be improved by automated workflow. Airflow, an open source to automate workflow, is one of good tools to build such systems. In this paper, we introduce how we design automated pipelines for machine learning model development with Airflow. 

```
