# Data Modeling with Apache Cassandra

A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analysis team is particularly interested in understanding what songs users are listening to. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.

In this project, I as Data Engineer will apply what I've learned about data modeling with Apache Cassandra and complete an ETL pipeline using Python. I will model the data by creating tables in Apache Cassandra to run queries and help the analysis team to answer business questions. We’ll also be able to test the database by running queries given by the analytics team at Sparkify.


## But why to use NoSQL?

There are some drawbacks of the relational database management systems when it comes to big data because you need:

1. high availability
2. horizontal scaling
3. fast throughputs: reads and writes
4. flexibility

So if we're dealling with a big data in Sparkify, we need to use NoSQL like Apache Cassandra as its built for high availability, scalability, and fault-tolerant systems.

## Our Data
We'll be working on one dataset: event_data. The directory of CSV files partitioned by date. Here are examples of filepaths to two files in the dataset:

```event_data/2018-11-08-events.csv
event_data/2018-11-09-events.csv
```
## Data Modeling
 
### ETL pipeline for pre-processing the data
Data currently resides in a directory of JSON files. We need to create a streamlined CSV file from all these. The final file will be used to extract and insert data into Apache Cassandra tables.

The code to pre-process the CSV files was provided already. So no need to go in-depth for it.

This is what the file event_datafile_new.csv looks like. It has 6821 rows and contains the following columns.

- artist
- firstName of user
- gender of user
- item number in session
- last name of user
- length of the song
- level (paid or free song)
- location of the user
- sessionId
- song title
- userId


![csv_data](images/image_event_datafile_new.jpg)

### Coding

In Apache Cassandra, we model our data based on the ***queries we will perform***. Aggregation like **GROUP BY**, **JOIN** are highly discouraged in Cassandra. This is because we shouldn’t scan the entire data because it is distributed on multiple nodes. It will slow down our system because sending all of that data from multiple nodes to a single machine will crash it.

##### Connecting

We create a Cluster and connect to our local host using this block of code:

    from cassandra.cluster import Cluster
    cluster = Cluster(['127.0.0.1'])

    # To establish connection and begin executing queries, need a session
    session = cluster.connect()

##### Creating a keyspace

Then we create a keyspace, which is similar to creating a database in Postgres where we specify the host and user privileges.

    try:
        session.execute("""CREATE KEYSPACE IF NOT EXISTS udacity 
        WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1}""")
    except Exception as e:
        print(e)

Then we set the keyspace

    session.set_keyspace("udacity")
    
##### Modeling

Now we will create the tables to run the following queries.

1. Give me the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4

2. Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182

3. Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'

You can view the complete process in ```Project_1B_ Project_Template.ipynb```.

## Files

- ```Project_1B_ Project_Template.ipynb``` Perform ELT
- ```event_data``` CSV files to be pre-processed
- ```evet_datafile_new.csv``` Streamlined file to create database