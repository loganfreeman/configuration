word count
---
```py
datafile = spark.textFile("hdfs://...")
datafile.flatMap(lambda line: line.split())
        .map(lambda word: (word, 1))
        .reduceByKey(lambda x, y: x+y)
```
