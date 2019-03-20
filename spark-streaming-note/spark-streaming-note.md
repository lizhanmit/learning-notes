# Spark Streaming Note

Spark streaming is not real stream computing. It is second level.

If you want millisecond level, use stream computing framework, e.g. Storm.  

![spark-streaming-input-output.png](img/spark-streaming-input-output.png)

![spark-streaming.png](img/spark-streaming.png)

## Coding Steps

![spark-streaming-coding-steps.png](img/spark-streaming-coding-steps.png)

---

## Spark Streaming Working Principle 

![spark-streaming-working-principle](img/spark-streaming-working-principle.png)

![spark-streaming-working-principle-2](img/spark-streaming-working-principle-2.png)

---

## How to use spark-submit to run spark application script （for real projects）

Take processing socket text as an example.

Steps: 

1. In terminal A, run a Netcat server `nc -lk 9999`.
2. In terminal B, run spark script.

```
spark-submit --master local[2] \
--class org.apache.spark.examples.streaming.NetworkWordCount\
--name NetworkWordCount \
/usr/local/spark/examples/jars/spark-examples_2.11-2.3.0.jar localhost 9999
```

3. In terminal A, type `a a a b b c`.
4. In terminal B, you will see 

```
(a,3)
(b,2)
(c,1)
``` 

---

## How to use spark-shell to run spark application script （for testing）

Take processing socket text as an example. 

Steps: 

1. In terminal A, run a Netcat server `nc -lk 9999`.
2. In terminal B, `spark-shell --master local[2]`.
3. In terminal B, run spark script.

```scala
import org.apache.spark.streaming._
val ssc = new StreamingContext(sc, Seconds(1)) 
val lines = ssc.socketTextStream("localhost", 9999)
val words = lines.flatMap(_.split(" "))
val wordCounts = words.map(x => (x, 1)).reduceByKey(_ + _)
wordCounts.print()
ssc.start()
ssc.awaitTermination()
```

4. In terminal A, type `a a a b b c`.
5. In terminal B, you will see 

```
(a,3)
(b,2)
(c,1)
``` 