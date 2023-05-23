# USAH1BAnalysis

# Implementing OLTP, OLAP systems on United States H-1B visa data for operational usage and analytics

# Introduction
Online Transactional Processing (OLTP) systems which are operational data stores (ODS), will help in processing the data in real time. Online Analytical Processing (OLAP) systems are informational and used for analysis. Implementing these systems on H-1B visa data will help in accessing data from ODS by the users and performing analysis on DWH. H-1B visa is a nonimmigrant work visa that allows U.S. employers to hire foreign workers for specialty jobs that require a bachelor's degree or equivalent. Highly educated foreign professionals apply for USA H-1B visas. With the data, we perform analysis and create dashboards that will help us understand various educational and geographical factors of an applicant. An Entity Relationship (ER) model is created by collecting the required data points from U.S. Citizenship and Immigration Services (USCIS). We have used the ER diagram to develop the OLTP database in the cloud MySQL instance. Data in MySQL is extracted and transformed using python and is loaded to a data warehouse. Analytics is performed on top of the data warehouse with data represented using a dashboard.

# Goals and Motivations
The goal and motivation are to present the analysis for the following
Top 10 applicant countries.
Popular education background from all the applicants.
Major employers with the highest applications and decisions.
State-wise distributions of application for the United States.
The average wage for the applicants across job titles.
Distribution of foreign workers across the economic sector.
Distribution of applications across prevailing wage.
Total applications for the Fiscal year.
Distribution of wages across the educational qualifications.
The average wage of the state across roles.

# Overview and Architecture
The goal is to implement OLTP and OLAP systems on the cloud, with the entire process automated in a single data pipeline/ workflow. After exploring services offered by various cloud service providers, we have decided to develop the project on Amazon Web Services (AWS), a top CSP famous for its pricing methodology and various flexible services. We have extracted the data from USCIS in CSV format. Data available from USCIS is raw data that has 154 columns and is unnormalized.
Post normalization of the data, CSV's will be migrated to the Amazon Simple Storage Service (S3) (located - US East (N. Virginia) us-east-1), which is scalable infrastructure storage and provides object storage services through a web service interface. Then we have our cloud SQL MySQL instance - OLTP (located - us-east-1b), Identity and Access Management (IAM) roles created along with the users and groups to access the Relational Database service. The instance is configured with VPC that will monitor and filter an instance's incoming and outgoing traffic with only authorized users accessing it. The backup policy of MySQL instance will help us in unexpected circumstances. ETL scripts are developed in python and orchestrated using AWS glue job, a serverless data integration service built on Apache Spark Structured Streaming engine. Using the ETL process, data is extracted from the MySQL instance and is transformed, and loaded into the data warehouse (DWH) entire ETL setup is performed using python. With the data in the warehouse, analysis is performed and views are created for the data reporting using SQL. Views are used to report the analysis in the form of visuals.

# Project Flow

An on-demand AWS glue workflow is designed for OLTP and OLAP systems creation and maintenance. The workflow starts with jobs that will create the tables in the MySQL database, post the data available in the CSV files in S3 are loaded into the tables. Once all the tables are created by following all the constraints, the next job runs which consists of the ETL process, data is extracted from the newly populated tables that are in the MySQL instance and data is loaded into the panda's data frame for data preprocessing, and data is ingested into snowflake using dependency libraries. Post ETL tableau data source is refreshed showcasing the updated data on dashboards.

# Database Implementation
Data is collected from various sources such as Kaggle, the US Department of Labor website, and other sources which contain a total of 64 columns. Multiple data cleaning procedures are performed on the data, including elimination of special characters, transformation of incomplete data into meaningful values, transformation of incorrectly formatted data, and formatting the inconsistent data to make data more meaningful. After the data cleaning process, data is normalized into 8 tables and data is modeled into a relational schema. Following is the entity relationship diagram for the relational schema:

# About the Data
The US H1-B Visa data is collected from various sources to make the data more meaningful and comprehensive. It is open-source data from the US Department of Labor that maintains the work-related Visa. It provides information on applications they receive every year for an H1-B visa and related information about employers and applicants. Postal code data has been taken from the USPS official website to populate the postal code, city, and state data of the applicant, employer, and job-related information in the above H1-B visa application data. The above data provides information on applications that are approved, denied, or withdrawn for H1-B visas for the year 2022. This data includes the information of the applicant, employer, job of the applicant, prevailing wage, and status of the application. This dataset contains more than 70,000 applications and their related information. Some of the important attributes in the data set are:

Application case Number
Case Received date
Case Status
Case Decision Date
Applicant Citizenship
Employer Name
Applicant Education
Job information
Job work place
Job Title
Prevailing wage
Prevailing wage job title

Some of the important relationships between various attributes are as follows:
Applicant details are provided in the application.
Employers that are filing on behalf of the applicant.
Applications filed by the third party agents for the applicants.
Nationalities of the applicants.
Job roles of the applicants that are applying.

The data set is normalized to satisfy 1NF, 2NF, and 3NF by splitting the data into 8 tables by following all the required constraints. The Application table contains the majority of the information about the H1-B visa data and the information of Agent, Employer, Applicant, Job Information, and Prevailing Wage information is separated and referenced to the Application table. A geography table has been created to match the correct postal codes with the data which is referenced to Employer, Applicant, and Job information tables. Education information contains the data related to the education of the applicant and the Education qualification required for the job.
# ETL Process
Extract, Transform, and Load is implemented using python and orchestrated using AWS Glue job.
# Extraction
Data is extracted from Cloud MySQL instance using access keys, access keys are not exposed publicly or exposed in code as they could breakout secrets and give a chance of stealing keys. The secrets/ access keys are stored in a .py file and the file is uploaded to the S3 bucket. The data is read from the file in the S3 bucket and the connection to the MySQL instance is made. Post connection the data is extracted and saved into a data frame using the read_sql function and the connection is closed immediately once the data is read.
# Transform
Once the data is loaded from the cloud MySQL instance to the data frame data transformations such as renaming columns, handling nulls, removing unwanted columns, and removing, the revenue field values are carried out. Handling string formatting for the str-type columns has been handled. Added Refreshed Date column on every table which consists of the uploading timestamp.
# Load
Data is loaded from the data frame to the snowflake database using DB connectivity through python. Access credentials to snowflake are also not exposed similar to the MySQL instance. Access details are stored in a .py file and data is read from the file and a connection is made once the connection is successfully created the data frame post-data pre-processing is dumped into tables.
# Logging and Exception Handling
Python script that was developed uses modularization. In the development of python code, each step of connection to S3, MySQL, and Snowflake is implemented with exception Handling, so that the code doesn't break, and the errors will be showcased.
# Data warehouse Implementation
Following the successful completion of the ETL process, the data is loaded into the Snowflake cloud data platform, which will serve as the data warehouse for the H1-B visa data. Snowflake cloud service is one of the most powerful data warehousing systems for huge-scale data. It supports cross-cloud deployment capabilities, is highly scalable, and secure, and has many more properties. The data will be ingested in compliance with the defined data model. We built a data warehouse for this project using the STAR schema model in which the application is the fact table and geography, prevailing wage, education info, and agent are dimension tables

# Conclusions
From the above H1-B data analysis, applicants from India are the majority of immigrants that have applied and got approved for the H1-B visa.
Applicants that are having their majors in Engineering such as "Computer Science", "Computer Engineering" and "Business Administration" have the most applications.
Top IT giants like Cognizant, Google, Microsoft, and Apple are top companies that file for H1-B visas for their employees from various countries with a combined percentage of more than 30%.
The majority of the applicants are from California state and are working in the role of "Software Engineer" with a total number of count as 2,006.
The average salary for the applicants with approved visa status is $112,350 and the highest average salary is for the applicants from California state.
Out of all the applications, 95.01% of applicants got an H1-B visa, 1.45% of applicants were denied the visa and 3.54% of applicants had withdrawn their applications.

# Lessons Learned
Understood how AWS glue and workflow work.
Worked with a snowflake which is a very popular data ware house and can also be integrated with most of the CSPs.
Performed normalization which is a good hands-on experience.
Learned about data modeling and star schema creation.
Implemented ETL using python.
All the technologies and tools used in the project are new to the group. Making sure to choose the correct architecture for the project helped us with a lot of research.
Learned how we can create visualizations with tableau.

# Technical Difficulty
The data we collected had a lot of missing and inconsistent values which had to be transformed into clean data.
We wanted to go with AWS which was new to the whole group so setting up architecture and understand it took a lot of effort.
There are many null values in columns that we have considered.
We did not want to share access keys in the code so worked on a way how we can find a way to provide access through files.
Inserting the data into databases and data warehouses took effort as the data has some issues while developing.

# Future Work
The entire set is automated and the on-demand pipeline will just perform the entire process no matter even if data for future fiscal years flow in. We can adjust the parameter in the warehouse and data for the future fiscal quarter will get appended to the data warehouse tables. Currently, the analysis is only one type of visa but we can include many types and perform analysis.
# Limitations:
The H1-B visa applications can be filled out by individuals and agents if the application is filled out by individuals the agent details will be null. Most of the fields consist of Null data, even though we handle the null it doesn't give us the true story. Considering the real-life scenario of the H1-B process, the approval percentage is much lesser than what was analyzed above as the data set has most approvals than rejections or withdrawals. The data collected from sources has a very less number of denials which is different from real-world scenarios.


