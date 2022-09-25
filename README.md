# Big Data Project - Small Files Problem on Object Storage

## Project Background and Summary

The program goes through 33 CSV files, all stored on the cloud, and each has 100K inspections of 3 fiels - first name, last name, and city. 

The program uses MapReduce to efficiently and effectively create a list that for each combination of field+value, shows the names of the files the combination appears in.

For example, if Haifa and Hamburg are cities in the database, the results would look like the following:

```
[['city_Haifa' 'myCSV15.csv, myCSV6.csv, myCSV8.csv, myCSV9.csv, myCSV11.csv, myCSV12.csv, myCSV17.csv, myCSV10.csv, myCSV5.csv, myCSV7.csv, myCSV18.csv, myCSV2.csv, myCSV4.csv']

['city_Hamburg' 'myCSV15.csv, myCSV6.csv, myCSV8.csv, myCSV9.csv, myCSV12.csv, myCSV11.csv, myCSV17.csv, myCSV19.csv, myCSV10.csv, myCSV5.csv, myCSV1.csv, myCSV13.csv, myCSV18.csv, myCSV7.csv, myCSV2.csv, myCSV0.csv, myCSV4.csv']
```

Our work comes to solve the small file problem under Object Storage, and to suggest that if we make an aggregation of small files into larger ones, processing time will be reduced. In the experiment, we will measure the change in processing time - with and without the aggregation step. 

Our results show a 60% improvement in processing time with the aggregation process prior to using MapReduce.

The notebook of the project can be found [here](https://github.com/orenavidan/orenavidan.github.io/blob/main/Big_Data_Final_Project_Small_file_problem_in_Object_Storage_v2.ipynb). 

[![GitHub Actions Build](https://github.com/apache/spark/actions/workflows/build_main.yml/badge.svg)](https://github.com/apache/spark/actions/workflows/build_main.yml)
[![AppVeyor Build](https://img.shields.io/appveyor/ci/ApacheSoftwareFoundation/spark/master.svg?style=plastic&logo=appveyor)](https://ci.appveyor.com/project/ApacheSoftwareFoundation/spark)
[![PySpark Coverage](https://codecov.io/gh/apache/spark/branch/master/graph/badge.svg)](https://codecov.io/gh/apache/spark)


## Online Documentation

You can find the latest Spark documentation, including a programming
guide, on the [project web page](https://spark.apache.org/documentation.html).
This README file only contains basic setup instructions.

## Building Spark

Spark is built using [Apache Maven](https://maven.apache.org/).
To build Spark and its example programs, run:

```bash
./build/mvn -DskipTests clean package
```

(You do not need to do this if you downloaded a pre-built package.)

More detailed documentation is available from the project site, at
["Building Spark"](https://spark.apache.org/docs/latest/building-spark.html).

For general development tips, including info on developing Spark using an IDE, see ["Useful Developer Tools"](https://spark.apache.org/developer-tools.html).

## Interactive Scala Shell

The easiest way to start using Spark is through the Scala shell:

```bash
./bin/spark-shell
```

Try the following command, which should return 1,000,000,000:

```scala
scala> spark.range(1000 * 1000 * 1000).count()
```

## Interactive Python Shell

Alternatively, if you prefer Python, you can use the Python shell:

```bash
./bin/pyspark
```

And run the following command, which should also return 1,000,000,000:

```python
>>> spark.range(1000 * 1000 * 1000).count()
```

## Example Programs

Spark also comes with several sample programs in the `examples` directory.
To run one of them, use `./bin/run-example <class> [params]`. For example:

```bash
./bin/run-example SparkPi
```

will run the Pi example locally.

You can set the MASTER environment variable when running examples to submit
examples to a cluster. This can be a mesos:// or spark:// URL,
"yarn" to run on YARN, and "local" to run
locally with one thread, or "local[N]" to run locally with N threads. You
can also use an abbreviated class name if the class is in the `examples`
package. For instance:

```bash
MASTER=spark://host:7077 ./bin/run-example SparkPi
```

Many of the example programs print usage help if no params are given.

## Running Tests

Testing first requires [building Spark](#building-spark). Once Spark is built, tests
can be run using:

```bash
./dev/run-tests
```

Please see the guidance on how to
[run tests for a module, or individual tests](https://spark.apache.org/developer-tools.html#individual-tests).

There is also a Kubernetes integration test, see resource-managers/kubernetes/integration-tests/README.md

## A Note About Hadoop Versions

Spark uses the Hadoop core library to talk to HDFS and other Hadoop-supported
storage systems. Because the protocols have changed in different versions of
Hadoop, you must build Spark against the same version that your cluster runs.

Please refer to the build documentation at
["Specifying the Hadoop Version and Enabling YARN"](https://spark.apache.org/docs/latest/building-spark.html#specifying-the-hadoop-version-and-enabling-yarn)
for detailed guidance on building for a particular distribution of Hadoop, including
building for particular Hive and Hive Thriftserver distributions.

## Configuration

Please refer to the [Configuration Guide](https://spark.apache.org/docs/latest/configuration.html)
in the online documentation for an overview on how to configure Spark.

## Contributing

Please review the [Contribution to Spark guide](https://spark.apache.org/contributing.html)
for information on how to get started contributing to the project.
