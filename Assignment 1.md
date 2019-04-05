# Assignment: Decision Making
___
### Task:    

● Clients complain that the searches for a destinations sometimes fail. Head of product decided to address
the issue, and ask development team to work on fix.

● The team committed to work out the solution during August. It was agreed that the team’s bonus payout
would depend on effectiveness of the solution.

● The Head of Product ask you to analyze the data and help him to decide whether the team deserve
the bonus?

● Your answer should be documented and submitted to the GitHub so that any changes can be easily
tracked.

### Useful context:

● Data for Barcelona destination (18452212) is representative so that any conclusions can be extrapolated
for other destinations.

___

## AWS Configuration
````
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]:  <Enter_Your_Password>
Default region name [None]: eu-west-1
Default output format [None]: ENTER
````
___

## Collect Data from Bdata S3 Buckets
```
MBP-de-Nicolas:~ nicolasdalessandro$ aws s3 cp s3://data.public.bdatainstitute.com/dam/query_logs/ awsfiles.zip --recursive
download: s3://data.public.bdatainstitute.com/dam/query_logs/2018/08/2018_08_18452212_summary.json to awsfiles.zip/2018/08/2018_08_18452212_summary.json
download: s3://data.public.bdatainstitute.com/dam/query_logs/2018/09/2018_09_18452212_summary.json to awsfiles.zip/2018/09/2018_09_18452212_summary.json
download: s3://data.public.bdatainstitute.com/dam/query_logs/2018/08/2018_08_18452212_log.json to awsfiles.zip/2018/08/2018_08_18452212_log.json
download: s3://data.public.bdatainstitute.com/dam/query_logs/2018/09/2018_09_18452212_log.json to awsfiles.zip/2018/09/2018_09_18452212_log.json
```
___

## Data Analysis

1. We note that some of the last files in the August database belong to September and we decided to place this files in the corresponding database.

2. We observed a large number of query/errors during August 18th which could mean that there is an error in the database. However, for our current analysis, we assume that the data is correct.		

https://github.com/nicodalessandro11/DAM19/blob/master/Data1.xlsx

## Data Visualization

1. We print a Boxplot of the queries_error in our database for a visual Analysis.

````
> library(readxl)
> Data1 <- read_excel("~/Desktop/Data1.xlsx")
> View(Data1)
> boxplot(error~month, data = Data1, at = c(1, 2), names = c("August", "September"), horizontal = TRUE, notch = TRUE)
````
![boxplot](https://github.com/nicodalessandro11/DAM19/blob/master/Rplot.png)

Also a Bargraph comparison shows a clear improvement between August and September.



## Statistical Analysis

1. Given that Boxplots displays a slight difference between August and September, it is proposed to perform a confirmatory data analisis trough a statistical hypothesis testing.

For this case we define: 

	Null hypothesis H0 = queries_error August
	Alternative hypothesis H1 = queries_error September as an improvement

2. Regarding the assupmtion that the observations of both groups are statistically independent of each other, data are paired and come from the same population, we opt to run a non parametric test, in order to decide whether to pay the bonus or not. For this case, the Mann-Withney U test (Wilcoxon rank sum test):

````
> wilcox.test(error~month, mu=0,alt="two.sided", correct=TRUE, paired=FALSE, conf.int=TRUE, data = Data1)

	Wilcoxon rank sum test with continuity correction

data:  error by month
W = 3619200, p-value = 7.695e-07
alternative hypothesis: true location shift is not equal to 0
95 percent confidence interval:
 2.599664e-06 9.999511e-01
````

*We obtained a P-Value out of the confidence interval*

___

## Potential Revenue Gain
As per the two graph comparisons and statdistacal analyst we can see the engineers made progress between Agust and September.  

Howerver, is this progress good enought to pay the bonus?  

We woudl like to calculate if the improvement generate enoguh revenue to pay out the bonus or if they have to work another month.

In order to decide if we pay the bonus an estimate of potential additional revenue is required.  

### Assumptions
* Eccommerce conversion rate average 1.84% [source](https://www.wordstream.com/blog/ws/2014/03/17/what-is-a-good-conversion-rate)
* Assuming for every reservation the company makes €1.00. I made up this value, in the real world we will have an average reservation value per destination.

|     Description         | Value     |
|:----------------------: |:---------:|
| Reduction Errors:    |  -0.70%   |
| % Correct Queries       |   99.43%  | 
|  Correct Queries        | 852,401.02 |
| +Correct Queries        | 4,888 |

Ca

|     Description         |   Value.   |  Revenue
|:----------------------: |:----------:|:----------:|
| Revenue x destination | €1.00 |
| Average Conversion Rate | 1.84% |
| +Reservations/month x destination| 76 | €75.72
| Number of Destination | 5000 | €449,694
| 1 year                |  12 months| **€4,542,946**




## Conclusion

With the information provided after running the Mann-Withney/Wilcoxon test, we can confirm that the distribution of both samples is not the same. In September, there is statistically a significant decrease in the number of errors compared to August. 

We decided to reject the nulle hypothesis in favor of the alternative hypothesis, so we consider the bonus payment should be made.

On top of that, the potential revenue gain shows an additional revenue of €378,579 per month if we continue having a 0.48% error rate.
						



