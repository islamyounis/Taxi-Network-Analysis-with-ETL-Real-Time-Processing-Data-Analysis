# Taxi-Network-Analysis-with-ETL-Real-Time-Processing-Data-Analysis

## Project Description and Architecture Overview
we developed a cutting-edge data pipeline capable of gathering real-time data from streaming sources and feeds into two dynamic dashboards: a real-time dashboard on a NoSQL Database and an aggregated dashboard showcasing our business's transformative changes over time.

![1231“](https://github.com/islamyounis/Taxi-Network-Analysis-with-ETL-Real-Time-Processing-Data-Analysis/assets/83661639/e769e904-f6fe-48b2-95b1-43d11663e5e2)


Our data originates from the "Taxi Network in New York City." We simulate it as a streaming data source using Python. Next, we utilize Kafka as a Message Queueing Service to receive data from multiple sources and distribute it to multiple consumers. The data is then processed in Spark and loaded into HDFS. With the help of Pyspark, we perform ETL operations and store the results in a modeled DWH in Postgres. For visualizing aggregated data, we employ PowerBI.

Additionally, we use Spark to consume data from Kafka and store it in a NoSQL database, Cassandra. PowerBI is also used to create a real-time dashboard for this data.


## Project Workflow
**1- Data Source System:**

The Data Source System is the initial stage of the project pipeline. The system collects taxi transactions, trip information, and location data from [TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) as parquet files.

This process collects taxi transactions from Parquet files and sends them to Kafka in the form of "serialized JSON." It simulates a source to Kafka by sending 1 to 1000 records every 3 seconds.
we have developed four separate scripts, each simulating a streaming source for its respective taxi type and send it to spacific topic in kafka. This approach ensures that the data sent to Kafka accurately represents the distinct characteristics of each taxi category.



**2- Data Collection:**

We have employed a pub/sub model to efficiently gather data from different sources simultaneously. The collected data is stored and made available for consumption by various consumers in real-time. To enable this architecture, we leverage Kafka as it offers scalability, fault tolerance, and other essential features. 

We have designed four distinct topics in kafka server, each catering to a specific type of taxi: "Yellow_taxi," "Green_Taxi," "Fhv_Trip," and "Fhvhv_Trip." These topics serve as dedicated storage for data relevant to their respective taxi categories. By organizing the data in this manner, we ensure data consistency and maintain the integrity of information for each taxi type.

Each of the four topics, namely "Yellow_taxi," "Green_Taxi," "Fhv_Trip," and "Fhvhv_Trip," is configured with ***2 replicas and 2 partitons***



**3- Landing Data in a staging area:**

In our Spark streaming process, we utilize four separate Spark jobs, each functioning as a consumer, to retrieve data from Kafka. Each consumer is responsible for processing the data from its corresponding topic. The processed data is then loaded into HDFS, where we have created four distinct directories, one for each topic, to store the data in an organized manner.

The directory structure in HDFS is as follows:

1. `/Yellow_taxi/raw_data`: This directory stores the data retrieved from the "Yellow_taxi" topic.
2. `/Green_Taxi/raw_data`: This directory stores the data retrieved from the "Green_Taxi" topic.
3. `/Fhv_Trip/raw_data`: This directory stores the data retrieved from the "Fhv_Trip" topic.
4. `/Fhvhv_Trip/raw_data`: This directory stores the data retrieved from the "Fhvhv_Trip" topic.

This setup ensures that the data from each topic is segregated and organized, making it easier to manage and analyze the information independently for each taxi type.



**4- Enhancing Data storage In HDFS:**
![1236](https://github.com/islamyounis/Taxi-Network-Analysis-with-ETL-Real-Time-Processing-Data-Analysis/assets/83661639/fecc8ccf-9e0d-4783-ab3d-72a899ec1ff8)



While implementing the streaming data retrieval from Kafka and loading it into HDFS, we encountered two challenges:

  ***4.1- Small File Problem :*** 
   small files have negatively impacting HDFS performance and name node memory storage, so we applied the "coalesce" function. By using "coalesce," we combined multiple small files into a single file, reducing the number of files in HDFS and optimizing the usage of storage space.

  ***4.2- Avoiding ETL Redundancy :***
   we have revented redundant ETL processing on the same data by creating a separate directory acting as a listener. This listener monitors files that have already undergone the ETL process. After processing, the data is moved from the "Raw_Data" directory to the "Proc_Data" directory, where it is partitioned by year, month, and day. This approach effectively creates a cold data archive for future processing needs, such as for use in machine learning or other analytical tasks.



**5- Data Warehouse Modeling**

![1238](https://github.com/islamyounis/Taxi-Network-Analysis-with-ETL-Real-Time-Processing-Data-Analysis/assets/83661639/70fd47b7-03a7-4826-a002-aef9d42fb09a)



**6- ETL process:**

We have meticulously designed an ETL job for each type of data to execute as a batch processing operation, typically scheduled at the end of each day using Cron tab. These ETL jobs undertake essential tasks such as data processing, transformation, and cleanup. We handle NULLs, 0 values, and unwanted columns, ensuring data integrity and consistency. Additionally, we extract day, month, and year information, group and aggregate transactions by day, and create a DateID column for efficient date-based querying.


Once the data is processed and transformed, we store the result in the "Proc_data Dir" and proceed to write it to Postgres. The data is then seamlessly integrated into a transactional fact table in the Postgres database, providing a reliable foundation for further analysis and reporting.



**7- Data Analytics & Visualization:**

By automating the ETL process through scheduled batch jobs and leveraging the power of Postgres, we streamline data management, enhance data quality, and enable faster and more accurate decision-making based on the derived insights.

we harnessed the power of Power BI to gain valuable insights and effectively visualize our data

![1235](https://github.com/islamyounis/Taxi-Network-Analysis-with-ETL-Real-Time-Processing-Data-Analysis/assets/83661639/5b154e12-642c-4849-8265-20a0bfe949ce)




.
.
.

**8- Realtime Dashboard**
We have strategically employed Cassandra as our data storage solution to facilitate real-time dashboard support. Cassandra's ability to provide efficient random access on the data allows for rapid updates and seamless real-time updates in our dashboards. This ensures that the dashboard reflects the most up-to-date information for users.

Below is a screenshot depicting how our data is organized and stored in Cassandra, highlighting its distributed nature and partitioning scheme:
![1237](https://github.com/islamyounis/Taxi-Network-Analysis-with-ETL-Real-Time-Processing-Data-Analysis/assets/83661639/a7a2d64e-c344-4803-a50c-66448369ac5b)


With Cassandra's distributed architecture and robust data replication, we can achieve high availability, fault tolerance, and scalability, making it an ideal choice for handling the demanding requirements of our real-time dashboard. Its optimized read and write operations enable us to deliver a responsive and dynamic dashboard experience to our users, ensuring they have access to the latest insights and analytics.
