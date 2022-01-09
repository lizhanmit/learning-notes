# ETL Note 

- [ETL Note](#etl-note)
  - [ETL Processing](#etl-processing)
    - [ETL Overview](#etl-overview)
    - [Steps of Creating ETL Processing Using SSMS (code level)](#steps-of-creating-etl-processing-using-ssms-code-level)
    - [SQL/Python Code vs. SSIS](#sqlpython-code-vs-ssis)
  - [ETL With SQL Programming](#etl-with-sql-programming)
    - [Two Ways to Perform ETL Process](#two-ways-to-perform-etl-process)
    - [Slow Changing Dimension (SCD)](#slow-changing-dimension-scd)
    - [Delete vs. Truncate (faster)](#delete-vs-truncate-faster)
    - [Transformations](#transformations)
      - [Handling Nulls](#handling-nulls)
      - [ETL Views](#etl-views)
      - [ETL Stored Procedures](#etl-stored-procedures)
      - [ETL Automation](#etl-automation)
  - [ETL Processing with SSIS](#etl-processing-with-ssis)
    - [SSIS Connection](#ssis-connection)
    - [Control Flows](#control-flows)
    - [Execute SQL Tasks](#execute-sql-tasks)
      - [Reset the Database to a Preload State](#reset-the-database-to-a-preload-state)
    - [Data Flows](#data-flows)
      - [Data Sources](#data-sources)
      - [Data Flow Paths](#data-flow-paths)
      - [Data Destinations](#data-destinations)
      - [Error Output Page](#error-output-page)
      - [Data Flow Transformations](#data-flow-transformations)
    - [Performance](#performance)
      - [Increase Performance](#increase-performance)
      - [Performance Comparison](#performance-comparison)
      - [Measure Performance](#measure-performance)
    - [Performing an ETL Process Using Staging Tables](#performing-an-etl-process-using-staging-tables)
  - [ETL vs. ELT](#etl-vs-elt)

---

Tutorial: [Microsoft: DAT217xImplementing ETL with SQL Server Integration Services](https://courses.edx.org/courses/course-v1:Microsoft+DAT217x+2T2018/course/)

Download [code](https://github.com/MicrosoftLearning/Implementing-ETL).

original source database (AdventureWorksLT2012) -> reporting database (DWAdventureWorksLT2012 )

---

## ETL Processing

### ETL Overview 

ETL: Extract, Transform and Load. 

ETL is process to extract data, mostly from different types of systems, transform it into a structure that is more appropriate for reporting and analysis and finally loading it into the database. 

Three tiers / layers in ETL:  

- source layer 

- integration layer where data is stored after transformation 

- dimension layer where actual presentation layer stands 

![etl-1.png](img/etl-1.png)

![etl-2.png](img/etl-2.png)

![etl-processing-overview.png](img/etl-processing-overview.png)

![etl-workflow.png](img/etl-workflow.png)

**Fact Tables vs. Dimension Tables**  

- Facts correspond to events. For example, a retail organization might have fact tables related to customer purchases, customer service telephone calls, and product returns. 
- Dimensions correspond to objects such as people, items, or other. 

**ETL Challenges** 

- Data warehouse loading should take place at night.  
- Proper time slot decision because multiple applications on the server.
- incremental load
- data duplication 
- takes hours for load to complete 

**Data Extraction** 

- In production environment, data is extracted within the mainframe system and sent as files. These files are then cleansed and loaded.  
- Jobs are scheduled to deliver the files to a particular directory in a particular server (on which the ETL tool is installed) .  
- ETL jobs are scheduled during off peak hours. 
- ETL process can be made file dependent or time dependent, i.e. the ETL process can kick off as soon as the  complete set of files arrives in the directory or they can kick off at a pre-determined time.
- Two ways of reading data from source systems: 
    - Read everything for every run.
    - Read only the incremental changes. (more efficient)

**Transformation**

- Data type conversion.  

- Columns joining or splitting.  

- Calculations or aggregations.  

- Apply business rules.  

- Decoding.  

**Loading**

*Create a separate table where we store the status of all the run in order to avoid data loss due to run failure.* 

![batch-run-status-table.png](img/batch-run-status-table.png)

Data formats in ETL system:  

- Flat files  
- XML datasets  
- Independent DBMS tables  
- Normalized ER schemas  
- Dimensional data tables  

Popular ETL tools:  

- Oracle Warehouse Builder (OWB) 
- SAP Data Services
- IBM Infosphere Information Server. (IBM Infosphere DataStage) 
- SAS Data Management
- PowerCenter Informatica 
- SQL Server Integration Services (SSIS) 
- Talend  

**Transforming data from tables**

![example-table.png](img/example-table.png)

```mssql
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

**Loading transformed data into a new data structure**

```mssql
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

To complete the ETL process you create and test ETL queries for each reporting table. These reporting tables are then collected into reporting databases called data warehouses or data marts.

It is a **best practice** for professionals to **use views**, **stored procedures**, and **functions** in their ETL solutions. 

---

### Steps of Creating ETL Processing Using SSMS (code level)

1. Create views from the source tables. 
2. Create the procedure from views. Begin.
3. Declare variables, including return code, event name, event status, and event error info. 
4. Try-catch block. 
   1. Begin transaction. 
   2. ETL code.
   3. Commit transaction.
   4. Set event status, event error info, and return code.
   5. In Catch, roll back transaction, set event status, event error info, and return code. 
5. Log, including event name, event status, and event error info. 
6. Return return code.
7. End the procedure. 

```mssql
CREATE -- Fill Dim Customers Procedure
PROCEDURE pETLFillDimCustomers
AS
	/**************************************************************
	Desc: Fills the DimCustomers dimension table
	ChangeLog: When, How, What
	20160101,RRoot,Created Procedure  
	**************************************************************/
Begin -- Procedure Code
	Declare 
	  @RC int = 0
	, @EventName nvarchar(200) = 'Exec pETLFillDimCustomers'
	, @EventStatus  nvarchar(50) = ''
	, @EventErrorInfo  nvarchar(1000) = ''
	;
 Begin Try 
  Begin Transaction; 
  -- ETL Code  -------------------------------------------------------------------

	INSERT INTO [DWAdventureWorksLT2012].[dbo].[DimCustomers]
	SELECT 
	  [CustomerID]
	, [CompanyName]
	, [ContactFullName]
	FROM [DWAdventureWorksLT2012].[dbo].[vETLDimCustomersData];
	  
  -- ETL Code  -------------------------------------------------------------------
  Commit Transaction;
  Select @EventStatus = 'Success', @EventErrorInfo = 'NA';
  Set @RC = 100; -- Success
 End Try
 Begin Catch
  Rollback Tran;
  Select @EventStatus = 'Failure';
  -- select and format information about any error (JSON)-- 
  Execute pSelErrorInfo @ErrorInfo = @EventErrorInfo output;
  ---------------------------------------------------------------------------------------------
  Set @RC = -100; -- Failure
 End Catch
   -- Logging Code  -------------------------------------------------------------------
	  Execute pInsETLEvents
			@ETLEventName =  @EventName
		  , @ETLEventStatus = @EventStatus
		  , @ETLEventErrorInfo = @EventErrorInfo
		  ;  
  -- Logging Code  -------------------------------------------------------------------
  -- Now insert it into the ETLEvents table  
 Return @RC;
End -- Procedure Code
;
go
```
---
### SQL/Python Code vs. SSIS

|      | *Pros*                         | *Cons*                                    |
| ---- | ------------------------------ | ----------------------------------------- |
| Code | more efficient                 | losing the visual workflow of ETL process |
| SSIS | visual workflow of ETL process | cost in performance                       |

---

## ETL With SQL Programming 

### Two Ways to Perform ETL Process

- Flush and Fill
- Incremental loading process (using views, stored procedures, and UDFs/user defined functions)
  - SQL Merge - most efficient method 

```mssql
Merge Into DimCustomers as TargetTable
        Using Customers as SourceTable
	      On TargetTable.CustomerID = SourceTable.CustomerID
			When Not Matched 
				Then -- The ID in the Source is not found the Target
					INSERT 
					Values ( SourceTable.CustomerID
					            , SourceTable.CustomerName
					            , SourceTable.CustomerEmail ) 
			When Matched -- When the IDs of the row currently being looked match 
			          -- but the Name does not match...
			         AND ( SourceTable.CustomerName <> TargetTable.CustomerName
			         -- or the Email does not match...
			        OR SourceTable.CustomerEmail <> TargetTable.CustomerEmail ) 
			Then 
					UPDATE -- change the data in the Target table (DimCustomers)
					SET TargetTable.CustomerName = SourceTable.CustomerName
						 , TargetTable.CustomerEmail = SourceTable.CustomerEmail
			When Not Matched By Source 
				Then -- The CustomerID is in the Target table, but not the source table
					DELETE
	; -- The merge statement demands a semicolon at the end!
```

---

### Slow Changing Dimension (SCD) 

Tracking changes over time.

- SCD Type 1: "No One Really Cares." Just overwrite the existing data and forget it.
- SCD Type 3: "What was it last time?" Tracks the previous value using separate columns.
- SCD Type 2: "I want them all!" Tracks an infinite number of versions by just adding a Version column to the table and forcing people to do only inserts instead of updates or deletes. For example, add ChangeStartDate, ChangeEndDate, and IsCurrent columns in the target table.
  - When inserting the data, set ChangeStartDate as GetDate(), IsCurrent as 'y'. 
  - When deleting the data, set ChangeEndDate as GetDate(), IsCurrent as 'n'. 
  - When updating the data, set IsCurrent as 'n'. Then insert the new data separately, and set ChangeStartDate as GetDate(), IsCurrent as 'y'. 

---

### Delete vs. Truncate (faster) 

- Delete: When re-fill up the table, the key will not restart from one. It just keeps going. Can rollback. 
- Truncate: The key will be reset. **Note:** you cannot truncate a table with foreign key constraint. You need to drop the constraint first. Cannot rollback. 

---

### Transformations

- Renaming columns for legibility.
- Splitting and merging columns.
- Converting data types for consistency.
- Reformatting data to make it more readable.
- Combining data from multiple tables.
- Redefining nullable values for enhanced reporting.
- Creating date lookup tables.
- Connecting foreign key to surrogate key.

#### Handling Nulls

It is **best practice** to interpret null values as something meaningful.

- In fact tables
  - Measured values are null: It is often best to keep the null values. If you substitute values such as a zero for null, the calculations may become incorrect.
  - Dimensional keys are null: It is best to create an artificial key value that can further describe the meaning of the null. This value can be added to the dimension table itself or stored in a dedicated lookup table, as shown here: 

![null-lookup-table.jpg](img/null-lookup-table.jpg)

- In dimension tables
  - Not recommending to leave null values.

#### ETL Views 

Views are useful for many ETL processing tasks such as the following:

- Transforming column names.
- Using column aliases.
- Combining data from multiple tables.
- Processing nulls.
- Performing data conversions.

ETL steps (with views):

1. Create dimension tables. 
2. Create views from source tables. 
3. Insert values from views to dimension tables using stored procedures.

#### ETL Stored Procedures 

ETL Procedure Template

```mssql
USE TempDB;
go

--********************************************************************--
-- Create an ETL Procedure Template
--********************************************************************--
CREATE -- Procedure Template
PROCEDURE pETLProcedureTemplate
AS
	/**************************************************************
	Desc: <Desc Goes Here>
	ChangeLog: When, Who, What
	20160101,RRoot,Created Procedure  
	**************************************************************/
Begin -- Procedure Code
 Declare 
   @RC int = 0;
 Begin Try 
  Begin Transaction; 
  -- ETL Code  -------------------------------------------------------------------

   Select 3/1 -- Test;
  
  -- ETL Code  -------------------------------------------------------------------
  Commit Transaction;
  Set @RC = 100; -- Success
 End Try
 Begin Catch
  Rollback Tran;
  Set @RC = -100; -- Failure
 End Catch
 Return @RC;
End -- Procedure Code
;
go
```

#### ETL Automation 

Set up automation steps: 

1. In SQL Server Management Studio, create ETL scripts. 
2. Create a SQL Server Agent job.
   1. SQL Server Agent -> Jobs -> New Job.
   2. Job that was created -> Start Job at Step...

---

##  ETL Processing with SSIS

### SSIS Connection

Connection types:

- File connection manager
- OLE DB connection manager 
  - more generic
  - most versatile and easy to use
  - but not as fast as an ADO.NET connection manager in some cases
- ADO.NET  connection manager
  - good for connecting Microsoft Technology like SQL Server 

---

### Control Flows 

![ssis-control-flow-sample.png](img/ssis-control-flow-sample.png)

Steps of creating a control flow:

1. Visually design process. 
   1. Add sequence containers to the control flow surface.
   2. Connect sequence containers.
   3. Add SSIS tasks in each sequence container.
   4. Connect the precedence constraint arrows. 
2. Configure the process. 

---

### Execute SQL Tasks

The Execute SQL Tasks can be used for the following:

- Drop foreign key constraints.
- Re-create fact and dimension tables.
- Modify database tables and views by creating, altering, or dropping them.
- Truncate a table's data.
- Run stored procedures.
- Save returned rowset objects into a variable.

**Note:** If you use stored procedures for Execute SQL Tasks, when configuring it, make sure set "SQL Statement" as the name of the stored procedure only without ";" or spaces, and set "IsQueryStoredProcedure" as "True". Otherwise, there will be errors. 

#### Reset the Database to a Preload State  

Two methods:

- Run a SQL script that drops and re-creates the database. 
- Restore a SQL backup of the database. 

---

### Data Flows

#### Data Sources

Connection Manager Page -> Data access modes:

- Table or view  
- Table name or view name variable 
- SQL command (recommend)
- SQL command from a variable (recommend)

**Note:** We recommend clicking the **Columns page** to be sure the internal XML code has been registered. This may be necessary, even if you do not need to make additional configurations. 

#### Data Flow Paths

**Note:** Be sure to connect the data flow path before editing the destination. 

#### Data Destinations

- OLE DB destination: most commonly used. 
- ADO.NET destination: better performance than OLE DB destinations. 
- SQL Server destination: best performance for Microsoft SQL Server database. When using this option, be sure to transform all of the data types appropriately, to avoid incurring data type conversion errors. 

Connection Manager Page -> Data access modes:

- Table or view
- Table or view – fast load (used most often, easy to use)
- Table name or view name variable
- Table name or view name variable fast load
- SQL command

**Note:** We recommend clicking the **Mappings page** to enforce the XML code in the package to be properly written. Do this even when no additional configurations are necessary. 

#### Error Output Page

**Note:** We recommend using this only as a backup, by handling errors before this state of the data flow where possible.

#### Data Flow Transformations

Data flow transformations types: 

- Sort transformation
- Data Conversion transformation
- Aggregate transformation
- Derived Column transformation
- Lookup transformation
- Union All transformation and Merge transformation
- Merge Join transformation

**Note:** When possible, we recommend performing these transformations in the Data Flow’s data sources .

---

### Performance

#### Increase Performance 

- Avoid pulling all the data from the source if you only need a part of it.
- Use Sequence containers to process data in parallel.
- Avoid transforming large amounts of data directly from a file. (**Often it is faster to import data into a temporary (staging) table and then use SQL transformation code to complete your ETL process.**)
- Avoid using SSIS Events to track progress.
- Consider removing indexes on the destination tables before loading it, and re-create the indexes after loading completes. 
- Avoid implicit conversion. Instead, convert data outside of SSIS’s own expression language runtime environment.
- Use the SQL Server Destination to improve ETL performance.

#### Performance Comparison 

- Pure SQL Execute SQL Task - best
- Hybrid SQL Data Flow Task - better
- Pure SSIS Data Flow Task - good

#### Measure Performance 

SQL Server Management Studio -> Tools -> SQL Server Profiler (monitors traffic between a client application and the database engine) -> 

- General page -> select "Use the template". 
- Event Selection page -> select events you would like to trace. 

---

### Performing an ETL Process Using Staging Tables

Steps:

1. Import external data directly into the staging tables without any transformations.
2. Create a select statement that performs the transformations, encapsulate that code into a SQL View. 
3. Create an insert stored procedure to perform the actual ETL process.  

---

## ETL vs. ELT

[ETL VS ELT](https://www.snowflake.com/guides/etl-vs-elt)

[ETL vs ELT: 5 Critical Differences](https://www.integrate.io/blog/etl-vs-elt/)