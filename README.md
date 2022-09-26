# Big Data Project - Small Files Problem on Object Storage

## Project Background and Summary

The program goes through 33 CSV files, each with 100K inspections with 3 fiels - first name, last name, and a city. The program's goal is to create a list of lists, that for each combination of field+value, will show a list of the files it appeares in. 

For example, if Haifa and Hamburg are two cities appearing in the database, the results would look like the following:

```
[['city_Haifa' 'myCSV15.csv, myCSV6.csv, myCSV8.csv, myCSV9.csv, myCSV11.csv, myCSV12.csv, myCSV17.csv, myCSV10.csv, myCSV5.csv, myCSV7.csv, myCSV18.csv, myCSV2.csv, myCSV4.csv']

['city_Hamburg' 'myCSV15.csv, myCSV6.csv, myCSV8.csv, myCSV9.csv, myCSV12.csv, myCSV11.csv, myCSV17.csv, myCSV19.csv, myCSV10.csv, myCSV5.csv, myCSV1.csv, myCSV13.csv, myCSV18.csv, myCSV7.csv, myCSV2.csv, myCSV0.csv, myCSV4.csv']
```

The program uses [MapReduce method](https://en.wikipedia.org/wiki/MapReduce), together with [Lithops](https://lithops-cloud.github.io/) and [IBM Cloud](https://www.ibm.com/cloud), to efficiently and effectively solve the problem.

Our experiment comes to suggest a solution to the small file problem under Object Storage. Our suggestion was to aggregate small files into larger ones, prior to the execution of the MapReduce process, and by that reduce the total processing time. In the experiment, we exeuted the program twice - one time was with the aggregation and one time was without. We measured the differences in the time it took to perform the task. 

Our results show a 60% improvement in processing time with the aggregation process prior to using MapReduce.


### Online Documentation

The notebook of the project can be found [here](https://github.com/orenavidan/orenavidan.github.io/blob/main/Big_Data_Final_Project_Small_file_problem_in_Object_Storage_v2.ipynb). 


## Setup and configuration
The setup includes pip installing and importing of the Lithops package, that will enable us to access files stored on IBM Cloud. Part of the configuration would be to provide lithops with the name of the user, the bucket, a public and private keys, and more. 

In addition, we will import SQLite and Pandas that will be used in the Shuffle phase and the time package for measuring the time it took to perform the two experiments. 


## Program flow and main functions
As mentioned, the program uses the MapReduce method, together with Lithops and IBM Cloud. The files are stored on the cloud and the Lithops package enables us to communicate with the cloud with simple built-in python commands. 

The flow of the program is as follows:

### **Get data from cloud** 
Firstly, we would want to understand how many files we have on the cloud and what their names are. This function accesses our bucket on the cloud and creates a list of all of the CSV files we have there. This will later be used, under the Map phase, to iterate over all CSV files. 

### **Aggregator**
The aggregation function, as its name implies, responsible for aggregating the small files into larger ones. Due to the importance of the names of the files, the function also keeps the name of the original file inside and together with the original data of the file. The function iterates over all CSV files on the cloud, and looks for small files. For each small file, the function will create a new field inside and will add the name of the file to the file's data. After, it will add all of the updated data to the aggregated file. When the aggregated file reaches a size that is large enough  (as we define in the program), the funciton will create a new large file and start from the beginning.

### **Map function**
The Map function operates under the MapRduce method, and thus it receives as input only one name of a CSV file at a time. The function returns a list of lists of the shape (field_value, document). The document part includes only one document name at a time. For exmaple we can receieve - 

```
[['city_Haifa', 'myCSV15.csv'], ['city_Haifa', 'myCSV6.csv'], ['city_Haifa', 'myCSV8.csv'], ['city_Haifa', 'myCSV8.csv']...]

```

Note: It is important to know that sometimes we use tuples and sometimes lists, as to some limitation lithops has with working with tuples.

4. **Shuffle phase** - the shuffle phase is actually executed under the MapReduceServerlessEngine class, under the execute function. The shuffle phase includes aggregating all files names for each field+key combination, and listing them together, even with duplications. For example, after the shuffle phase, the data could look like -

```
[['city_Haifa', 'myCSV15.csv', 'myCSV6.csv', 'myCSV8.csv', 'myCSV8.csv'], ...]
```

5. **Reduce function** - the purpose of the reduce phase is to remove duplicates from the list above. The function receives a list of the shape
``` [key+value, doc1, doc2, doc2,...]```
and returns 
```[key+value, doc1, doc2,...]```

So for example we will get - 
```
[['city_Haifa', 'myCSV15.csv', 'myCSV6.csv', 'myCSV8.csv'], ...]
```
and this will be our final result as shown above. 


## Experiment desription and results
Our expriment included measuring the time it took the program to process the data, with and without aggregating it first. The program uses `aggregator switch` that is turned on when we detect small files, and changes the way the map function works. 
The reuslts show a 60% reduction in processing time from ~11 seconds to ~4 seconds. 

