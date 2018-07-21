# ETL Note 

## ETL Processing

### ETL Overview 

![etl-processing-overview.png](img/etl-processing-overview.png)

![etl-1.png](img/etl-1.png)

![etl-2.png](img/etl-2.png)

![etl-workflow.png](img/etl-workflow.png)


**ETL Challenges** 

- incremental load
- data duplication 
- takes hours for load to complete 

**Data Extraction** 

- ETL jobs are scheduled during off peak hours. 
- ETL process can be made file dependent or time dependent. 
- Two ways of reading data from source systems: 
    - Read everything for every run.
    - Read only the incremental changes. (more efficient)

*Create a separate table where we store the status of all the run in order to avoid data loss due to run failure.* 

![batch-run-status-table.png](img/batch-run-status-table.png)

---

**Transforming data from tables**

![example-table.png](img/example-table.png)

```sql
-- Converting Data Using Cast
SELECT 
  [TitleId] = [title_id] 
, [TitleName] = CAST([title] as nVarchar(100)) 
, [TitleType] = CASE CAST([type] as nVarchar(100)) 
   When 'business' Then 'Business'
   When 'mod_cook' Then 'Modern Cooking'                             
   When 'popular_comp' Then 'Popular Computing'                     
   When 'psychology' Then 'Psychology'                         
   When 'trad_cook' Then 'Traditional Cooking'    
   When 'UNDECIDED' Then 'Undecided'                                 
  End
FROM [pubs].[dbo].[titles];
```

---

**Loading transformed data into a new data structure**

```sql
Use TempDB;
Go
-- Create a reporting table 
CREATE TABLE [dbo].[DimTitles]
( [TitleId] [char](6) NOT NULL
, [TitleName] [nVarchar](100) NOT NULL
, [TitleType] [nVarchar](100) NOT NULL
);
Go
-- Add transformed data
INSERT INTO [TempDB].[dbo].[DimTitles]
 SELECT 
   [TitleId] = [title_id] 
 , [TitleName] = CAST([title] as nVarchar(100)) 
 , [TitleType] = CASE CAST([type] as nVarchar(100)) 
    When 'business' Then 'Business'
    When 'mod_cook' Then 'Modern Cooking'                             
    When 'popular_comp' Then 'Popular Computing'                     
    When 'psychology' Then 'Psychology'                         
    When 'trad_cook' Then 'Traditional Cooking'    
    When 'UNDECIDED' Then 'Undecided'                                 
   End
 FROM [pubs].[dbo].[titles];
Go
```

---

To complete the ETL process you create and test ETL queries for each reporting table. These reporting tables are then collected into reporting databases called data warehouses or data marts.

---

Case Study 

Tutorial: [Microsoft: DAT217xImplementing ETL with SQL Server Integration Services](https://courses.edx.org/courses/course-v1:Microsoft+DAT217x+2T2018/course/)

[Code](https://github.com/MicrosoftLearning/Implementing-ETL)

original source database -> reporting database 

AdventureWorksLT2012 -> DWAdventureWorksLT2012 

Use Flush and Fill technique for the first version of the ETL solution. Later on, use incremental loading process. 



 ## SQL Knowledge Review 

Newer databases use nVarchar instead of varchar.

Making data types and character lengths more consistent can assist other developers using this code.

------

### Views

SQL Views are essentially a named select statement stored in a database. 

```sql
-- Listing 1-6. Creating an ETL View
Use TempDB;
go
CREATE VIEW vETLSelectSourceDataForDimTitles
AS
 SELECT 
   [TitleId] = [title_id] 
 , [TitleName] = CAST([title] as nVarchar(100)) 
 , [TitleType] = CASE CAST([type] as nVarchar(100)) 
    When 'business' Then 'Business'
    When 'mod_cook' Then 'Modern Cooking'						     
    When 'popular_comp' Then 'Popular Computing'					 
    When 'psychology' Then 'Psychology'						 
    When 'trad_cook' Then 'Traditional Cooking'	
    When 'UNDECIDED' Then 'Undecided'							     
   End
FROM [pubs].[dbo].[titles];
go

-- Listing 1-7. Using the View
SELECT 
  [TitleId]
, [TitleName]
, [TitleType]
FROM vETLSelectSourceDataForDimTitles;
go
```

---

### Stored Procedures

```sql
-- Listing 1-10. Creating an ETL Procedure
CREATE PROCEDURE pETLInsDataToDimTitles
AS
 DELETE FROM [TempDB].[dbo].[DimTitles];
 INSERT INTO [TempDB].[dbo].[DimTitles]
 SELECT 
   [TitleId]
 , [TitleName]
 , [TitleType]
 FROM vETLSelectSourceDataForDimTitles;
go
EXECUTE pETLInsDataToDimTitles;
go
SELECT * FROM [TempDB].[dbo].[DimTitles]
go
```

---

It is a **best practice** for professionals **use both views** and **stored procedure** as abstraction layer in their ETL solutions. 

Why you should use views and stored procedures? 

![reason-to-use-view-or-stored-procedure.png](img/reason-to-use-view-or-stored-procedure.png)

---

### Stored Procedures Coding Steps

1. Create procedure. Begin.
2. Declare variables, including return code, event name, event status, and event error info. 
3. Try-catch block. 
   1. Begin transaction. 
   2. ETL code.
   3. Commit transaction.
   4. Set event status, event error info, and return code.
   5. In Catch, roll back transaction, set event status, event error info, and return code. 
4. Log, including event name, event status, and event error info. 
5. Return return code.
6. End procedure. 

