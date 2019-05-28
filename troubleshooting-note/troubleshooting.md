# Troubleshooting

## Java

**Problem 1**

Intellij IDEA prompts "Try-with-resources are not supported at language level '5'".

Solution: Add the following in pom.xml. (https://blog.csdn.net/qq_37502106/article/details/86771122)

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

---

## Spark

**Problem 1**

When running "hello world" example once after setting up the project, got such an error.

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 10582
...
```

Solution: Add such a dependency in pom.xml.

```
<dependency>
	<groupId>com.thoughtworks.paranamer</groupId>
	<artifactId>paranamer</artifactId>
	<version>2.8</version>
</dependency>
```

**Problem 2**

When running the below code:

```scala
val dfWithEventTimeQuery3 = dfWithEventTime.groupBy(window(col("Event_Time"), "10 minutes", "10 seconds"))
      .count()
      .writeStream
      .queryName("events_per_window_sliding")
      .format("memory")
      .outputMode("complete")
      .start()
```

got error: 

```
ERROR CodeGenerator: failed to compile: org.codehaus.janino.InternalCompilerException: Compiling "GeneratedClass": Code of method "expand_doConsume$(Lorg/apache/spark/sql/catalyst/expressions/GeneratedClass$GeneratedIteratorForCodegenStage1;JZ)V" of class "org.apache.spark.sql.catalyst.expressions.GeneratedClass$GeneratedIteratorForCodegenStage1" grows beyond 64 KB
```

Reason: The problem is that when Java programs generated using Catalyst from programs using DataFrame and Dataset are compiled into Java bytecode, the size of byte code of one method must not be 64 KB or more, This conflicts with the limitation of the Java class file, which is an exception that occurs.

