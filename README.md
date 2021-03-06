# Reddit-Data-Tools

Note: this project is in no way an official or endorsed Reddit tool.

Reddit user Stuck_In_The_Matrix has created a very large archive of public Reddit comments
 and put them up for downloading, see: [Thread on Reddit](https://www.reddit.com/r/datasets/comments/3bxlg7/i_have_every_publicly_available_reddit_comment/)
  
This repository contains some tools to handle the over 900 GByte of JSON data.

Future plans are to create a simple web interface for complex queries 
(for example: >2000 upvotes, must not be in /r/funny or /r/pics, must contain all of (op, surely, deliver), 
must not contain more than one mention of (safe, picture, money) and can be from 2012 or 2013). 
Currently you will have to write such queries in Java (see: Search class to get an idea of how to start).

## What's inside

### Java
 
Java classes to 
    
* read/convert the original bz2-compressed JSON data
* create a Lucene index (skipping deleted comments, needs 24 hours single-threaded)
* add the comments to a Postgresql database (useful if you want to play with a month's worth of data)
* convert the JSON data into CSV (cuts required size in half and is easier to digest for many programs, 
  but may cause problems with line breaks in comments)

### Scala
  
Classes using Apache Spark to:
  
* combine sentiment CSV files and bz2-compressed JSON data to parquet format for better Spark/Hadoop querying
* count upvotes in JSON files
* find the most positive users
     
### Python

Python classes for (simple) sentiment analysis which can

* add sentiment data to comments existing in a PostgreSQL db (a database filled by the Java code)
* read original bz2 compressed JSON, and output sentiment data to CSV files        

## Changes

* 2016-10-22: FindMostPositiveUsers.scala: find the 100 most positive users using the sentiment files.
* 2016-09-25: Add bash script to loop over all comment archives.
* 2016-09-22: scoreCommentsJson.py: read comments in JSON format from bz2 files with Python.
* 2016-09-22: Added sentiment analysis with Python and NLTK. For example results, see [code.dewarim.com](http://code.dewarim.com)
* 2016-09-19: Upgrade to Spark 2.0

## Field order of CSV:

    @JsonPropertyOrder(value = {"author", "name", "body", "author_flair_text", "gilded", "score_hidden", "score", "link_id",
            "retrieved_on", "author_flair_css_class", "subreddit", "edited", "ups", "downs", "controversiality",
            "created_utc", "parent_id", "archived", "subreddit_id", "id", "distinguished"})
    
## Fields indexed with Lucene
    
            doc.add(new StringField("author", comment.author, Field.Store.YES));
            doc.add(new StringField("name", comment.name, Field.Store.YES));
            doc.add(new TextField("body", comment.body, Field.Store.YES));
            doc.add(new IntField("gilded", comment.gilded, Field.Store.YES));
            doc.add(new IntField("score", comment.score, Field.Store.YES));
            doc.add(new IntField("ups", comment.ups, Field.Store.YES));
            doc.add(new IntField("downs", comment.downs, Field.Store.YES));
            doc.add(new LongField("created_utc", comment.created_utc, Field.Store.YES));
            doc.add(new StringField("parent_id", comment.parent_id, Field.Store.YES));
            doc.add(new StringField("subreddit", comment.subreddit, Field.Store.YES));
            doc.add(new StringField("id", comment.id, Field.Store.YES));
            doc.add(new StringField("url", Comment.createLink(comment.subreddit, comment.link_id, comment.id), Field.Store.YES));
   
   STORE.YES means the field is contained in the index and can be shown on a search result page.       

## Input/Output Format

CsvConverter should be called with two parameters, input directory and output directory.
It reads .bz2 and emits .csv.gz files at the moment.
    
    mvn clean package
    # copy target/reddit-1.0-SNAPSHOT-distribution.zip to target machine
    # unzip  unzip reddit-1.0-SNAPSHOT-distribution.zip 
    # cd  reddit-1.0-SNAPSHOT-distribution 
    java -cp reddit-1.0-SNAPSHOT.jar:lib/. com.dewarim.reddit.csv.CsvConverter /home/ingo/reddit_data /home/ingo/reddit_csv    

## License

My code is free to use under the [Apache License](http://www.apache.org/licenses/LICENSE-2.0), version 2.
Contributions will be accepted under the same terms and are welcome.

## Code style

The simplest thing that will work.
 
## Author
 
Ingo Wiarda / ingo_wiarda@dewarim.de /u/Dewarim
