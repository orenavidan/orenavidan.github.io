# Big Data Project - Small Files Problem on Object Storage

## Project Background and Summary

The program goes through 33 CSV files, each has 100K inspections of 3 fiels - first name, last name, and city. The program's goal is to create a list of lists, that each combination of field+value, will recive a list of the files it appeares in. 

The program uses MapReduce method, together with Lithops and IBM Cloud, to efficiently and effectively solve the problem.

For example, if Haifa and Hamburg are two cities appearing in the database, the results would look like the following:

```
[['city_Haifa' 'myCSV15.csv, myCSV6.csv, myCSV8.csv, myCSV9.csv, myCSV11.csv, myCSV12.csv, myCSV17.csv, myCSV10.csv, myCSV5.csv, myCSV7.csv, myCSV18.csv, myCSV2.csv, myCSV4.csv']

['city_Hamburg' 'myCSV15.csv, myCSV6.csv, myCSV8.csv, myCSV9.csv, myCSV12.csv, myCSV11.csv, myCSV17.csv, myCSV19.csv, myCSV10.csv, myCSV5.csv, myCSV1.csv, myCSV13.csv, myCSV18.csv, myCSV7.csv, myCSV2.csv, myCSV0.csv, myCSV4.csv']
```

Our experiment comes to suggest a solution to the small file problem under Object Storage. Our suggestions was to aggregate small files into larger ones, prior to executing the MapReduce process, and by that reduce processing time. In the experiment, we will run the program two timws - with and without the aggregation step, and measure the differences in the time it took to perform the task. 

Our results show a 60% improvement in processing time with the aggregation process prior to using MapReduce.


### Online Documentation

The notebook of the project can be found [here](https://github.com/orenavidan/orenavidan.github.io/blob/main/Big_Data_Final_Project_Small_file_problem_in_Object_Storage_v2.ipynb). This README file only contains basic explanations about the project.


## Setup and configuration
The setup includes pip installing and importing of the Lithops package, that will enable us to access files stored on IBM Cloud. Part of the configuration would be to provide lithops with the name of the user, the bucket, a public and private keys, and more. 

In addition, we will import SQLite and Pandas that will be used in the Shuffle phase and the time package for measuring the time it took to perform the two experiments. 


## Program flow and main functions
As mentioned, the program uses the MapReduce method, together with Lithops and IBM Cloud. The files are stored on the cloud and the Lithops package enables us to communicate with the cloud with simple built-in commands. 

The flow of the program is as follows:
1. **Get data from cloud** - firstly, we would want to understand how many files we have on the cloud and what their names are. The function accesses our bucket on the cloud and creates a list of all of the CSV files we have there. This will later be used (under the Map phase) to iterate over all files. 
2. **Aggregator** - the aggregation function, as its name implies, responsible for aggregating the small files together and keeping the name of the original file inside and together with the data of the file. The function iterates over all CSV files on the cloud, and looks for small files. For each small file, the function will create a new field with the name of the file and then add it to the file's data. After, it will add the updated data to the aggregated file. When reaching a size that is large enough, the funciton will create a new large file and start from the beginning. 
3. Map function - the function operates under the MapRduce method, and this it receives only one name of one CSV file we have on the cloud, and returns a list of lists of the shape (field_value, document). The document part includes only one document name at a time. For exmaple we can receieve - 

```
[['city_Haifa', 'myCSV15.csv'], ['city_Haifa', 'myCSV6.csv'], ['city_Haifa', 'myCSV8.csv'], ['city_Haifa', 'myCSV8.csv']...]

```

Note: It is important to know that sometimes we use tuples and sometimes lists, as to some limitation lithops has with working with tuples.

4. **Shuffle phase** - the shuffle phase is actually executed under the MapReduceServerlessEngine class, under the execute function. The shuffle phase includes aggregating all files names for each field+key combination, and listing them together, even with duplications. For example, after the shuffle phase, the data could look like -

```
[['city_Haifa', 'myCSV15.csv', 'myCSV6.csv'], 'myCSV8.csv', 'myCSV8.csv']...]
```

5. **Reduce phase** - the purpose of the reduce phase is to remove duplicates from the list above. The function receives a list of the shape [key+value, doc1, doc2, doc2,...] and returns [key+value, doc1, doc2,...].



## Experiment results
Our expriment shows a 60% reduction in processing time from ~11 seconds to ~4 seconds. 

