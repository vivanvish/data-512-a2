## Bias in Data
### Project Goal
The main goal of the project is to provide a well documented, fully reproducible process that construct, analyze the Wikipedia articles dataset to explore the concept of **bias in data**. 
[Link to the problem statement](https://wiki.communitydata.cc/Human_Centered_Data_Science_(Fall_2018)/Assignments#A2:_Bias_in_data) 

### Data Source

Data from the following sources were used for this analysis:
- **2018 World Population Datasheet by Population Research Bureau.**
This datset contains provides the 2018 population estimates for countries across the world (as of mid 2018). The data was prepared by the Population Research Bureau. To ensure reproducibility and convenience, the data has been saved to dropbox ([link](https://www.dropbox.com/s/5u7sy1xt7g0oi2c/WPDS_2018_data.csv?dl=0)). The data is also available in this repo as a csv file: `data/WPDS_2018_data.csv`.  
For more details about the data and estimation methods please visit the [PRB website](https://www.prb.org/2018-world-population-data-sheet-with-focus-on-changing-age-structures/). 


- **Politicians by Country from the English-language Wikipedia**
This data was downloaded from Figshare ([link](https://figshare.com/articles/Untitled_Item/5513449)). It contains a subset of metadata info of articles on politicians published in the English version of Wikipedia. The data was retrieved using Wikimedia API and stored on Figshare. It is also made available here as `data/WPDS_2018_data.csv`.

- **Article Quality estimation using ORES**
A measure of quality of each of the articles is obtained using the **ORES** ("Objective Revision Evaluation Service") machine learning service. Given an article revision id, the service will return its estimated quality as one of the following classes:
  - FA - Featured article
  - GA - Good article
  - B - B-class article
  - C - C-class article
  - Start - Start-class article
  - Stub - Stub-class article  
Here FA and GA are considered to measure of high quality. For more information about the scores, visit this [link](https://en.wikipedia.org/wiki/Wikipedia:WikiProject_assessment#Grades).  
For this particular project we are using the `wp10` machine learning model to predict the article quality.  
[ORES Documentation](https://www.mediawiki.org/wiki/ORES)  
[ORES Endpoint](https://ores.wikimedia.org/v3/)  
The estimated article results are stored locally as `data/page_data_with_scores.csv`.

[Figshare Terms and Conditions](https://figshare.com/terms)

[Wikimedia Terms and Conditions](https://wikimediafoundation.org/wiki/Terms_of_Use/en)

The Wikipedia data is licensed under [MIT](https://opensource.org/licenses/MIT)

### Data
All data files that are used for the analysis are stored in the `data`. Some of them are the raw data that were downloaded from the source, whereas others are intermediate files saved during processing to for convenience and to ensure reproducibility.  
**WPDS_2018_data.csv (Population raw data)** : Population data downloaded from dropbox server (link provided above). There are around 207 locations in the dataset (Countries, Continents, Independent regions, etc). It has the following structure:

Column | Value | Description |
| ------------- |:-------------:| -----|
Geography  | string | Name of the location|
Population mid-2018 (millions) | numeric | Population of the location in millions, as of mid 2018|

**page_data.csv (Article raw data)** : Data of articles about politicians in Wikipedia English. The data was collected using the Wikimedia APIs and stored on Figshare (link given above). The file has the following columns

Column | Value | Description |
| ------------- |:-------------:| -----|
country| string|The country name|
page|string |The Wikipedia article title|
rev_id| numeric |The revision_id of the article's last edit|

**page_data_v1.csv (Processed article data)** : The raw article data has some issues with the country names not matching the ones in the population data. Hence I tried to manually fix some of those inconsistencies and stored the resulting data as `page_data_v1.csv`. It has the same structure as the raw data.

**page_data_with_scores.csv (Article data with scores)** : The artcile quality predictions provided by the ORES service was merged with the processed article data (`page_data_v1.csv`) on the revision ids and then stored for convenience. The service did not provide any scores for about 105 revision ids. Hence those articles are dropped while It has the following structure:

Column | Value | Description |
| ------------- |:-------------:| -----|
country| string|The country name|
page|string |The Wikipedia article title|
rev_id| numeric |The revision_id of the article's last edit|
score|string| The quality class returned by the ORES service|

**article_quality_with_population.csv (Final analytical dataset)** : This is the final dataset that was used for the analysis. It was obtained by merging the population dataset with the article dataset (`page_data_with_scores.csv`). Since several locations in the article dataset are not present in the population dataset, we lost about approx. 500 articles while merging. The final dataset has the following structure:


Column | Value | Description |
| ------------- |:-------------:| -----|
country| string|The country name|
article_name|string |The Wikipedia article title|
revision_id| numeric |The revision_id of the article's last edit|
article_quality|string| The quality class returned by the ORES service|
population|numeric| The population of the country|

### Code
All the code involved in this project are saved as a `Jupyter notebook` : `hcds-a2-bias.ipynb`.

The notebook was divided into 3 sections:
- Getting Article and Population data.
- Getting the article scores using ORES.
- Analysis of articles.
This purpose of this step was to explore the article to check for possible biases in data and also to answer the following questions:
  - 10 highest-ranked countries in terms of number of politician articles as a proportion of country population.
  - 10 lowest-ranked countries in terms of number of politician articles as a proportion of country population.
  - 10 highest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country.
  - 10 lowest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country.

The notebook runs on Python 3.6 and the following packages are needed to run:
- json
- matplotlib
- numpy
- pandas
- requests

The final result is saved as a .png file which also has been published here as : `wiki_pageviews_2008_2018.png`

### Results
**1. 10 highest-ranked countries in terms of number of politician articles as a proportion of country population**
![alt text](https://github.com/vivanvish/data-512-a2/blob/master/imgs/q1.png)

**2. 10 lowest-ranked countries in terms of number of politician articles as a proportion of country population**
![alt text](https://github.com/vivanvish/data-512-a2/blob/master/imgs/q2.png)

**3. 10 highest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country**
![alt text](https://github.com/vivanvish/data-512-a2/blob/master/imgs/q3.png)

**4. 10 lowest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country**
![alt text](https://github.com/vivanvish/data-512-a2/blob/master/imgs/q4.png)

### Reflection
The exercise was a good experience that reiterated the complexities involved in dealing with data from multiple sources. For instance, several countries present in the article data were not present in the population data. There were also several confusing aspects such as oveseas territories and conflicted regions. It was a difficult task to try and resolve the issue. I could have chosen to do some cursory analysis of all the locations under focus and then based on that match them with the countries in the population data. But that would mean that I would be introducing my bias in the data, This is especially critical here because even the presence of a single article can change the results we observe. So I opted to go for the safer approach and resolved the inconsistencies that were straightforward. But this may not always be the case.

Over the course of the analysis, I started realizing several potential sources of bias. The first one was related to the ORES service. After a bit of digging around, I learned that the article quality measure is actually based on the structure of the article and not on its content, which was a bit misleading to say the least. This could mean several things. Unless the editors of the articles are seasoned experts, I doubt whether everyone who had added these articles would have taken care to fulfill all the criteria to qualify as a high quality article. This could bias the data towards countries with editors who are more familiar with the scoring criteria.

The second potential source of bias has more to do with the topic itself: **Politicians**. Even though each country has several hundred politicians on average, not all of them would necessarily be added on Wikipedia. Its more likely for a Politician to be added on Wikipedia if that person was really popular in that country and internationally or was part of something controversial. This could skew that data towards countries that may have a controversial history, such as North Korea. I could imagine that articles on controversial personalities would be subjected to several reviews before being published. As we can see, North Korea is on the top of the list of countries with the highest proportion of high quality articles. It would be good to conduct a subsequent study to verify the validity of my assumption. 

The last point I would like to add here is that the results are heavily influenced by the population. The population data is more accurate that the article dataset. What I meant by that is that the population data is created with the aim to match the ground truth as much as possible. But the article dataset is not. We cannot say confidently that the article dataset contains all the politicians from all the countries in the world. Its much more flaky and is solely determined by the collection strategy used. Thus there is a lot of unknowns governing the content of the article dataset which makes me wonder how different the results seen here are different from the actual truth.
## Known Issues
- Several locations in article dataset does not match with the locations in the population dataset. So several articles were dropped as a result. 
- The ORES service did not return data for 105 articles. This could be dependent on the request rate and hence can change during future runs. So if you are interested in reproducing this result exactly, I would recommend using the file `page_data_v1.csv`.
## License
The code is licensed under [MIT](LICENSE).

## Contact
In case of any queries, please contact me at vishnn@uw.edu
