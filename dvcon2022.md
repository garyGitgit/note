Find a needle in a haystack: log analysis with test clustering in distributed system

Jin Choi, Sangwoo Noh, Hanna Jang, Seonghee Yim, Seonil Brian Choi
{jjin.choi, sangwoo.noh, hn01.jang, sh.yim, seonilb.choi}@samsung.com

Abstract-As the complexity and number of gates in System on Chip (SoC) increase, the cost for verification also increases. In such a trend, verification engineers are struggling with boring and repetitive debugging processes, such as debugging similar tests with same errors, in very limited resources, i.e., time and licenses. In this paper, we propose a novel approach of log analysis with test clustering in distributed system to reduce repetitive debugging and use limited resources more efficiently. This paper focuses on simulation log files, which may help in future debugging, because they contain useful information, such as components, run time, environments and included files. We make a contribution to give new insight on debugging by finding a needle in a haystack of simulation log files. The expected result is to reduce debugging time by predicting error prone tests in the earlier stage and to use limited resources more efficiently by test case prioritization. 

I.   INTRODUCTION
As the design of SoC becomes more complex, the cost for verification accounts for 70% in the overall SoC development process. [1][2][3] This leads for verification engineers to do boring and repetitive debugging of millions of tests, paralleled with multiple projects, which can be failed with similar errors. To reduce such efforts, similarity-based studies were conducted in both the software testing industry [4][5][6][7] and design verification industry. [8]
In this paper, we propose a novel approach of test case clustering by clustering test cases with their components from simulation log files. The proposed methods are based on automated regression system suggested in the earlier paper, [9] and the system can run more efficiently in two ways: sampling representative tests from each cluster and finding related tests with the same errors. With sampling representative tests, tests can be prioritized and run 30~40% of all tests first, resulting in efficient use of resources. With finding related tests, verification engineers can debug once without repetitive work on similar tests by running related tests automatically. The contribution in this paper is as follows:
●	to focus on test clustering with component analysis from simulation log files
●	to propose a clustering algorithm with weighted K-means clustering
●	to build automated machine learning model pipelines with distributed system

II.   PROPOSED METHODS
Test clustering with component analysis
In this paper, we propose a novel approach of test clustering with component analysis based on simulation logs. The approach consists of two phases. In the first phase, tests are clustered with components from simulation log files. The components, including Task, Class, Function and Sequence, are collected from log files and used as a feature for clustering. To ensure that components are valid enough to be used as features, an experiment comparing between a naive method and the proposed component based method was conducted: the naive method is to extract features from test name, which is the most noticeable feature for identifying tests. Figure 1 shows clustering results of both methods, less sparse on the right figure compared to the left figure. The silhouette score and elbow method was used for clustering evaluation score, with 0.67 and 0.81, respectively. Next, each test is vectorized with term-frequency, the number of occurrences in all logs, of components, and tests are clustered with K-means clustering algorithm. Choosing a proper K value is achieved by comparing silhouette scores and elbow method automatically.
In the second phase, both components and errors are considered to increase accuracy in clustering. Only with the first phase, it is possible that some of the tests can be excluded from its group of related tests with the same errors. As the purpose of the clustering is to find out all related tests with a specific error, the second phase makes the related tests closer inside and outside of their clusters. This clustering is named as weighted K-means clustering. The components in specific errors are given weights, and clusters in the first phase are reorganized based on weights. 



   

Figure 1. t-SNE with K-means labels by test name (left) and t-SNE with K-means labels by components (right)

Machine learning model pipelines with distributed systems
The architecture of data pipelining consists of four stages: Data collection, Data processing, Clustering & Validation and Deployment. This architecture is robust in that the clustering model is able to be deployed with a continuous data integration and continuous model development flow in a loop of regression automation with response time of less than a second. Data collection, the first stage of the architecture, is a process of collecting simulation log files and storing preprocessed data into distributed databases. In 17745 sample log files, sizes of files range from megabytes to hundreds gigabytes, meaning distributed databases, such as MongoDB or Hadoop, can be more efficient than a standalone database. Data processing is a process of preprocessing data and extracting components from simulation log files stored in distributed databases. In this stage, processing time can be remarkably decreased by maximizing computing power by using both multithreading and distributed processing throughout multiple servers. Clustering & Validation is a process of classifying tests with the proposed clustering algorithm, and validating its performance and selecting the best hyper parameters. As tuning a proper hyper parameter is one of the boring jobs in data analysis, this stage automatically finds the optimal hyper parameter: the number of clusters is a main hyper parameter in the clustering algorithm. Finally, in the Deployment stage, the final clustering model is loaded in the memory of the deployment server and be serviced through REST API with response time, less than a second. To reduce memory usage in multiple worker threads, a thread-safe Singleton design pattern is used. This stage is important in that our model is not only for research but also used practically in pipelines of regression automation with building models, prioritizing tests, running tests automatically.

  

Figure 2. Architecture of distributed machine learning pipeline (left) and its performance evaluation compared to a standalone system (right)

III. RESULTS
To validate the proposed weighted K-means clustering algorithm, 100 of 17754 tests were sampled randomly. Of 100 tests, 16 targeted tests contained a specific error in a component. The goal of this experiment was to see those 16 tests are clustered successfully considering both components and errors. Figure 3 shows the result of clustering between normal K-means clustering and weighted K means clustering. The former one shows a cluster of targeted tests on the left figure, but test A is out of the actual cluster because of its similarity in components with another cluster. The right figure shows clustering results of weighted K-means clustering, which makes the interior tests in the actual cluster closer and includes the missing test A on the left figure. This indicates given weights in the error component increase clustering accuracy compared to no weight. Figure 4 also shows improvement on a machine learning modeling process with the proposed distributed pipelines. 

  
Figure 3. Cluster tests without weight (left) and cluster tests with weight (right)

IV. CONCLUSIONS
The increase in complexity and diversity of design is a big challenge in the design verification industry because of limited resources. This paper focuses on methods of test clustering with component analysis based on simulation logs to find representative tests in similar groups and run them first to find distinct errors in the earlier stage of the verification process. The result showed that similarity from component analysis can be measured more accurately than similarity from test names. Moreover, the proposed weighted K-means clustering shows higher accuracy by considering both components and errors. In future works, more features can be considered to improve clustering results for two reasons: loss of logs and changes in names of components. As logs may not contain full records because some tools may not generate full logs or verification engineers may not print all logs for all components. To overcome this problem, a System Verilog parser is required to fill missing components in simulation log files. Further analysis for components is also considered with a machine learning inference model trained with diverse combination of components.





REFERENCES
[1]    Chen, Wen & Ray, Sandip & Abadir, Magdy & Bhadra, Jayanta & Wang, Li-C. (2017). Challenges and Trends in Modern SoC Design Verification. IEEE Design & Test. PP. 1-1. 10.1109/MDAT.2017.2735383.
[2]    Harry D. Foster. 2015. Trends in functional verification: a 2014 industry study. In Proceedings of the 52nd Annual Design Automation Conference (DAC '15). Association for Computing Machinery, New York, NY, USA, Article 48, 1–6. DOI:https://doi.org/10.1145/2744769.2744921
[3]    mackinsey: productivity and verfication
[4]    Fang, C., Chen, Z., Wu, K. et al. Similarity-based test case prioritization using ordered sequences of program entities. Software Qual J 22, 335–361 (2014). https://doi.org/10.1007/s11219-013-9224-0
[5]    B. Miranda, E. Cruciani, R. Verdecchia and A. Bertolino, "FAST Approaches to Scalable Similarity-Based Test Case Prioritization," 2018 IEEE/ACM 40th International Conference on Software Engineering (ICSE), 2018, pp. 222-232, doi: 10.1145/3180155.3180210..
[6]    T. B. Noor and H. Hemmati, "A similarity-based approach for test case prioritization using historical failure data," 2015 IEEE 26th International Symposium on Software Reliability Engineering (ISSRE), 2015, pp. 58-68, doi: 10.1109/ISSRE.2015.7381799.
[7]    Flemström, D., Potena, P., Sundmark, D. et al. Similarity-based prioritization of test case automation. Software Qual J 26, 1421–1449 (2018). https://doi.org/10.1007/s11219-017-9401-7
[8]    A. Wahba, J. Hohnerlein and F. Rahman, "Expediting Design Bug Discovery in Regressions of x86 Processors Using Machine Learning," 2019 20th International Workshop on Microprocessor/SoC Test, Security and Verification (MTV), 2019, pp. 1-6, doi: 10.1109/MTV48867.2019.00009.
[9]    DVA paper
