[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

# Data Science, Data Engineering, and AWS Solutions Architecture Projects

Projects, tools, notebooks and AWS cloud architectures I have built.

## Table of Contents
- [Data Science, Data Engineering, and AWS Solutions Architecture Projects](#data-science-data-engineering-and-aws-solutions-architecture-projects)
  - [Table of Contents](#table-of-contents)
  - [YouTube Transcript Topic Modeling Using BERTopic](#youtube-transcript-topic-modeling-using-bertopic)
  - [serverless-streaming-reddit-pipeline](#serverless-streaming-reddit-pipeline)
  - [aws-permits-pipeline](#aws-permits-pipeline)
  - [bioNX - *Under Development*](#bionx---under-development)
  - [dict-smasher](#dict-smasher)
  - [csv2ddb](#csv2ddb)
  - [neo4j-titanic](#neo4j-titanic)
  - [permits-data](#permits-data)
  - [brazil-fuel-price-analysis](#brazil-fuel-price-analysis)
  - [Contact](#contact)
  - [Contributing](#contributing)
  - [License](#license)

## [YouTube Transcript Topic Modeling Using BERTopic](https://gist.github.com/abk7777/34faf3cb6b5d6721fadc21ca69b4b138)
Perform topic modeling on a YouTube video transcript using BERTopic. Uses a video from the Lex Fridman podcast for the example. 

## [serverless-streaming-reddit-pipeline](https://github.com/abk7777/serverless-streaming-reddit-pipeline)

Serverless big data streaming application using a fan-out architecture with Lambda and SQS to fetch data from Reddit forums, stream to Kinesis Firehose and store in an S3 data lake. An AWS Glue workflow crawls the data and converts it to parquet which can then be queried in Athena or visualized in Quicksight. The pipeline is serverless and infinitely scalable, which means it could be used to monitor any number of subreddits, or even all of Reddit if desired.

Example Athena queries:
```
-- Most popular posts by number of comments and upvote ratio
SELECT MAX(upvote_ratio) AS upvote_ratio, MAX(num_comments) AS num_comments, title
FROM prod_ssrp_1_raw_reddit_posts_parquet
GROUP BY title
ORDER BY num_comments DESC, upvote_ratio DESC
LIMIT 100;
```
```
-- Most popular authors
SELECT DISTINCT author AS author, COUNT(DISTINCT title) as posts_count
FROM prod_ssrp_1_raw_reddit_posts_parquet
GROUP BY author
ORDER BY posts_count DESC
LIMIT 100;
```

## [aws-permits-pipeline](https://github.com/abk7777/aws-permits-pipeline)

An ETL pipeline for construction permits data from the [Los Angeles Open Data Portal](https://data.lacity.org/) hosted on AWS using *S3*, *Lambda*, and *RDS PostgreSQL*. Once deployed the pipeline fetches fresh data once a day and loads or upserts it into an RDS instance running PostgreSQL with PostGIS for geospatial analysis. The pipeline can be deployed with or without VPC and read replicas. Built using Python, CloudFormation and Serverless Framework:

```
# Deploy AWS RDS
aws --region us-east-1 cloudformation deploy \
--template-file cfn/rds-vpc.yml \
--capabilities CAPABILITY_NAMED_IAM \
--stack-name aws-permits-pipeline-vpc

# Initialize PostgreSQL database and install PostGIS
serverless invoke --function initDatabase \
--stage dev \
--region us-east-1 \
--log

# Invoke Lambdas for ETL
serverless invoke --function fetchData \
--stage dev \
--region us-east-1 \
--log
```

## [bioNX](https://github.com/abk7777/bioNX) - *Under Development*

Automated Knowledge Graph construction of protein-protein interaction networks using Python and Neo4j. A bioNX Knowledge Graph allows the linking of biological data across disparate sources including as databases, APIs, literature and websites. It can provide insight to the relationships between nodes, which can be anything from academic literature, samples, or experiments, to subjects of inquiry such as gene products, PPIs, small molecules and disease conditions.

Example Knowledge Graph of protein interactions mentioned in a particular [PubMed article](https://pubmed.ncbi.nlm.nih.gov/28514442/):
![bioNX Screenshot](./img/bionx-screenshot.png)

## [dict-smasher](https://github.com/abk7777/dict-smasher)

Python library using recursive functions to flatten nested dictionaries and lists of dictionaries. Built using Python standard library.

```python
from dict_smasher import dict_smasher, dict_write, select_keys

dict_smasher(nested_dict) # flattens dictionary
select_keys(nested_dict, keys) # select specific keys
dict_write(nested_dict, header, path) # output csv file
```

## [csv2ddb](https://github.com/abk7777/csv2ddb)

Command line tool for easy loading of CSV files directly into AWS DynamoDB. Solves the problem of how to quickly get your CSV data into a DynamoDB table. Built using boto3 and click.

```bash
csv2ddb create --table-name my-cool-table --partition-key userId --partition-key-type N
csv2ddb load --table-name my-cool-table ./data/profile.csv
```

## [neo4j-titanic](https://github.com/abk7777/neo4j-titanic)

Simple Python data pipeline that loads the *RMS Titanic* dataset into a Neo4j Docker instance. Get it up and running using `make graph`. Built using Conda, Python, Shell, Make, Neo4j and Docker.

This Cypher query reveals how family members were spread out across different lifeboats:
```sh
MATCH (p1:Passenger)-[:BOARDED]-(l1:Lifeboat),
(p2:Passenger)-[:BOARDED]-(l2:Lifeboat),
(p1)-[:RELATED_TO]-(p2)
RETURN p1, p2, l1, l2 LIMIT 25;
```
![neo4j-titanic-screencap.png](./img/neo4j-titanic-screencap.png)

## [permits-data](https://github.com/abk7777/permits-data)

Python/Docker ETL pipeline to load construction permits data from the [Los Angeles Open Data Portal](https://data.lacity.org/) into a PostgreSQL Docker instance. Run `make data` to download data, load it into a Docker PostgreSQL database, transform columns and geocode missing addresses. Includes a basic Object-Relational Mapper (ORM) library for PostgreSQL using psycopg2. Built on Conda, Python, Shell, Make, PostgreSQL, Docker and psycopg2. For an updated version running on AWS please see my `aws-permits-pipeline` project.

## [brazil-fuel-price-analysis](https://github.com/abk7777/brazil-fuel-price-analysis)

Data science notebooks containing EDA and geospatial analyses in the form of choropleth maps that visualize fuel price data from the Agência Nacional do Petróleo, Gás Natural e Biocombustíveis in Brazil. Uses pandas, geopandas, matplotlib, seaborn, and shapely.

![Brazil Fuel Analysis Screenshot](./img/choropleth_mean_fuel_price_state.png)

<!-- CONTACT -->
## Contact

Gregory Lindsey - [@abk7x4](https://twitter.com/abk7x4) - gclindsey@gmail.com

Project Link: [https://github.com/abk7777/project-portfolio](https://github.com/abk7777/project-portfolio)

<!-- CONTRIBUTING -->
## Contributing

Contributions to these projects are always welcome. Here are the basic steps:

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<!-- LICENSE -->
## License

Most of these projects are distributed under the MIT License. See the `LICENSE` file in each repo for more information.

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->

[stars-shield]: https://img.shields.io/github/stars/abk7777/project-portfolio.svg?style=flat-square
[stars-url]: https://github.com/abk7777/project-portfolio/stargazers
[issues-shield]: https://img.shields.io/github/issues/abk7777/project-portfolio.svg?style=flat-square
[issues-url]: https://github.com/abk7777/project-portfolio/issues
[license-shield]: https://img.shields.io/github/license/abk7777/project-portfolio.svg?style=flat-square
[license-url]: https://github.com/abk7777/project-portfolio/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=flat-square&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/gregory-lindsey/
