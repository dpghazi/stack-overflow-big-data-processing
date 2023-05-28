# Stack Overflow Big Data Processing
![Screen Shot 2022-08-17 at 11 48 07 AM](https://user-images.githubusercontent.com/94224903/185272147-248e5729-8a1d-4a11-b4e3-f415f87200a0.jpg)

## **Project Description**
- Spun an Elastic MapReduce (EMR) cluster based on Spark and created a Spark application written in Python.
- Implemented Python API for Apache Spark (PySpark) and performed spark-submit to process data from the [Stack Overflow Annual Developer Survey 2020](https://insights.stackoverflow.com/survey).
- Created an S3 bucket to upload "survey_results_public.csv" file so EMR can access it for data processing.
- Locally issued Linux commands (Amazon Linux 2) to the EMR cluster's master node by connecting to an Elastic Compute Cloud (EC2) instance using Secure Shell (SSH) connection.

### **Overview**

- Created an EMR cluster with cluster launch mode and an initial S3 bucket was created automatically to store logs.
    - Software Configuration
        - emr-5.36.0
        - Spark: Spark 2.4.8 on Hadoop 2.10.1 YARN and Zeppelin 0.10.0
    - Hardware Configuration
        - m5.xlarge
        - Number of instances: 3
    - Security and access
        - EC2 key pair (used Amazon EC2 to create a RSA key pair)
![Kapture 2022-08-17 at 16 07 08](https://user-images.githubusercontent.com/94224903/185259581-10e86439-fb22-48de-8593-f423cd1e2079.gif)
- Set-up a new S3 bucket to upload the file "survey_results_public.csv" so EMR can access it for data processing.
![Kapture 2022-08-17 at 17 02 56](https://user-images.githubusercontent.com/94224903/185264338-9171114a-d83c-4018-bd85-8e164c3964d8.gif)
- Inserted a new folder within the same S3 bucket called "data-source" that contains the CSV file.
![Kapture 2022-08-17 at 17 12 50](https://user-images.githubusercontent.com/94224903/185265193-2391efaa-5f12-48a6-89c2-05a8fd609765.gif)
- Created a Spark application in a Python file called "main.py" for Spark storage job to process data.
```Python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col

S3_DATA_SOURCE_PATH = 's3://stackoverflow-123456/data-source/survey_results_public.csv'
S3_DATA_OUTPUT_PATH = 's3://stackoverflow-123456/data-output'

def main ():
    spark = SparkSession.builder.appName('StackoverflowApp').getOrCreate()
    all_data = spark.read.csv(S3_DATA_SOURCE_PATH, header=True)
    print('Total number of records in the source data: %s' % all_data.count())
    selected_data = all_data.where((col('Country') == 'United States') & (col('WorkWeekHrs') > 45))
    print('The number of engineers who work more than 45 hours a week in the US is: %s' % selected_data.count())
    selected_data.write.mode('overwrite').parquet(S3_DATA_OUTPUT_PATH)
    print('Selected data was was successfully saved to S3: %s' % S3_DATA_OUTPUT_PATH)

if __name__ == '__main__':
    main()
```
- Opened port 22 to SSH into the EMR cluster using IP address and ran spark-submit command for "main.py" data processing.
![Kapture 2022-08-17 at 17 35 25](https://user-images.githubusercontent.com/94224903/185267170-3fd6b9ac-9578-4b03-abe6-f4039d36a675.gif)
![Kapture 2022-08-17 at 18 49 00](https://user-images.githubusercontent.com/94224903/185274467-d4207aae-f728-4f5a-9999-62b1e027681c.gif)
## Result
![Kapture 2022-08-17 at 18 53 44](https://user-images.githubusercontent.com/94224903/185275094-b45bc7bb-84d1-4f17-87c4-5b3da50f8acb.gif)
![Screen Shot 2022-08-17 at 6 56 05 PM](https://user-images.githubusercontent.com/94224903/185275382-d63b5857-8789-4c58-beea-626a728f3644.jpg)
![Screen Shot 2022-08-17 at 6 56 28 PM](https://user-images.githubusercontent.com/94224903/185275385-e4e65f29-454f-4191-b5ec-cbccd10e0c02.jpg)
![Screen Shot 2022-08-17 at 6 56 49 PM](https://user-images.githubusercontent.com/94224903/185275394-a7085e0e-59b0-4044-8e12-5795fa823954.jpg)


- A new folder called "data-output" with parquet files was created in the same S3 bucket, after executing commands written in "main.py".
![Kapture 2022-08-17 at 18 59 50](https://user-images.githubusercontent.com/94224903/185276121-4c7831df-89a7-4637-9ece-58d4e2393a80.gif)


### Language **& Tools**

- Python
- SQL
- Spark (PySpark)
- AWS (EMR, EC2, S3)
- Bash (Amazon Linux 2)
