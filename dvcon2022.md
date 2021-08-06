```
Title
xxxx
{X, X, X}@samsung.com


Abstract-As the complexity and number of gates in System on Chip (SoC) increase, the cost for verification also increases, which accounts for half of development time in overall process. Such trend is accelerated by increasing number of additional blocks, such as NPU, TPU, GPU, for specific purposes. In this paper, a novel approach of classifying tests based on component analysis with simulation log files is presented to decrease debugging time in verification. This paper focuses on simulation log files, as a number of log files are interpreted by each engineer manually for debugging and discarded without investigation about improvement of further debugging, even if they contains much more information than engineers expect. This paper contributes to a new insight on simulation log files in verification by decomposing logs into components and classifying them for efficient debugging process.

I.   INTRODUCTION
It is known that the cost of design verification increases as the size of design increases, accounting for 70% of the entire SoC development process. [1][2][3] This trend is accelerated by increasing number of blocks with specific purposes, such as NPUs, TPUs and GPUs, etc. It leads verification engineers who handle multiple projects simultaneously to do boring and repetitive debugging process, which takes most of their work time. [1] The robust and bugless architecture must be kept with meeting time-to-market in the industry. However, as simulation time ranges from a few hours to a few days, it is challenging for verification engineers to detect bugs from ten thousands of tests. In this paper, we propose a novel approach to reduce such challenging debugging process by grouping tests by their similarity with component analysis from simulation log files. Researches related to test case similarity were conducted in both software testing industry and design verification industry. In software industry, B. Miranda et al,  suggests that Farthest-first ordered sequence (FOS) algorithm and Greed-aided-clustering ordered sequence (GOS) algorithm based on similarity of ordered sequences with their execution frequency. [6] X2 proposes FAST (FAST) algorithm, minhashing and locality-sensitive hashing algorithm for quickly finding diverse test cases within a big dataset. [7] … [8] In the hardware industry, X4 proposes classification of RTL and verification errors based on signatures, a difference between failures. The contribution of this paper is to focus on components from log files, to classify test cases based on the components and to build data pipeline in distributed systems for practical use of classification models. 

II.   PROPOSED METHODS
In this paper, we propose a novel approach of test case classification with component analysis based on simulations logs. The approach consists of two phases. In the first phase, it classifies tests with components from simulation log files. The components include Task, Class, Function and Sequence, and they are recorded in log files. To ensure that components are good enough to be used as features for classification, an experiment comparing two methods was gone through: the first method is a naïve method to extract features from names of tests. As names of tests can be the most distinct clue for identifying tests, we pick it as a comparable method; the second method is the proposed method, component based analysis method which is proposed in this paper. Each test is vectorized with the number of components and classified with K-means clustering. 
In the second phase, errors are considered for classification, and clusters are reorganized by this new feature. As the final goal is to find out the most similar tests related to a specific error, this process is used for improving accuracy of clustering considering not only their components but also errors. As errors contain components in its line, components could be extracted. With this component, weights were given to the component, making the distances between tests sharing the same component based on the result from the first phase. The result is shown in Figure.3 comparing the result from phase 1. Considering errors, tests with the same components gathers closer in a cluster and some of tests in other clusters could be in the clusters which contain a specific component and an error. This result indicates that tests can be classified more accurately, considering both their components and errors. This proposed method can be used for recommendation for which test should be run to reach debugging pass rate in shorter time, resolving different issues first with a representative test from each cluster and running other tests in cluster when resource and cost are available. 


[Figure 1.]













III.   ARCHITECTURE
Before, The architecture of data pipelining consists of four stages: data collection, data processing, classification & validation and deployment. This architecture is robust in that the developed model is able to be deployed with a continuous data integration and continuous model development flow in a loop of regression automation with faster response time, which is less than a minute. Data collection, the first stage of the architecture, is a process of collecting simulation log files and storing preprocessed data into distributed databases. In 17745 sample log files, sizes of files range from 1MB to hundreds of GB, meaning distributed databases, such as MongoDB or Hadoop, can be more efficient than a standalone database. Data processing is a process of preprocessing data and extracting components from simulation log files stored in distributed databases. In this stage, processing time can be exponentially decreased by maximizing computing power by using multithreading and distributed processing throughout multiple machines. This reduces processing time from Y1 hours to Y2 minutes in this stage. Classification & Validation is a process of classifying tests with their components extracted from the earlier stage, and validating its performance and selecting the best hyper parameters. One of the most boring jobs in data analysis is finding the optimal hyper parameter. In our architecture, this boring task can be removed by changing multiple values automatically and pick the most reliable value to maximize the degree of clustering. Finally, in the Deployment stage, the final classification model is loaded in the memory of the deployment server and be serviced through REST API with response time, less than a minute. This stage is important in that our model is not only for research but also used practically in the industry in the loop of regression automation. 

  
Figure 2. Architecture of distributed machine learning pipeline (left) 
and its performance evaluation compared to a standalone system (right)

IV.   RESULTS
Figure.2 shows that the result of clustering between two methods. The clusters are less sparse on the right figure compared to the left figure. The evaluation how clustering was well-performed was also measure by Dunn index, one of clustering evaluation methods. Dunn index for the right figure shows 0.67, and the index for the left figure shows 0.97. This comparison shows that classification with component based analysis performs better than the naïve approach.


[Figure 3.]












V.   CONCLUSION
Log files are large and it contains a number of information from simulation. It also has actual output of simulation, which means log has actual actions for simulation. In this paper, we focus on the analysis of log files and proposed a novel approach of test classification with component analysis based on simulation logs. The result shows that similarity from component analysis can be measured more accurately than similarity from test names. Moreover, the proposed weighted-component K-means clustering shows higher accuracy by considering both components and errors. In the future works, more features should be considered to improve classification result for two reasons: loss of logs and changes in names of components. As logs may not contain full records because some tools may not generate full logs or verification engineers may not print all logs for all components. To overcome this problem, a System Verilog parser is required to fill missing components in simulation log files. For the expected problem in changes in names of components, we can consider further analysis of components in the context of not only their names but also actual log outputs recorded in log files. 

DESIGN COMPLEXITY REFERENCES
[1]	Harry D. Foster. 2015. Trends in functional verification: a 2014 industry study. In Proceedings of the 52nd Annual Design Automation Conference (DAC '15). Association for Computing Machinery, New York, NY, USA, Article 48, 1–6. DOI:https://doi.org/10.1145/2744769.2744921
[2]	
[3]	
[4]	

METHOD REFERENCES
[1]	A. Wahba, J. Hohnerlein and F. Rahman, "Expediting Design Bug Discovery in Regressions of x86 Processors Using Machine Learning," 2019 20th International Workshop on Microprocessor/SoC Test, Security and Verification (MTV), 2019, pp. 1-6, doi: 10.1109/MTV48867.2019.00009.
[2]	Fang, C., Chen, Z., Wu, K. et al. Similarity-based test case prioritization using ordered sequences of program entities. Software Qual J 22, 335–361 (2014). https://doi.org/10.1007/s11219-013-9224-0
[3]	B. Miranda, E. Cruciani, R. Verdecchia and A. Bertolino, "FAST Approaches to Scalable Similarity-Based Test Case Prioritization," 2018 IEEE/ACM 40th International Conference on Software Engineering (ICSE), 2018, pp. 222-232, doi: 10.1145/3180155.3180210..
[4]	T. B. Noor and H. Hemmati, "A similarity-based approach for test case prioritization using historical failure data," 2015 IEEE 26th International Symposium on Software Reliability Engineering (ISSRE), 2015, pp. 58-68, doi: 10.1109/ISSRE.2015.7381799.
[5]	Flemström, D., Potena, P., Sundmark, D. et al. Similarity-based prioritization of test case automation. Software Qual J 26, 1421–1449 (2018). https://doi.org/10.1007/s11219-017-9401-7


```
