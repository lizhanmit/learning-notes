# ETL Note 

Tutorial: [Microsoft: DAT217xImplementing ETL with SQL Server Integration Services](https://courses.edx.org/courses/course-v1:Microsoft+DAT217x+2T2018/course/)

Download [code](https://github.com/MicrosoftLearning/Implementing-ETL).

original source database (AdventureWorksLT2012) -> reporting database (DWAdventureWorksLT2012 )

---

## ETL Processing

### ETL Overview 

![etl-1.png](img/etl-1.png)

![etl-2.png](img/etl-2.png)

![etl-processing-overview.png](img/etl-processing-overview.png)

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

It is a **best practice** for professionals to **use views**, **stored procedures**, and **functions** in their ETL solutions. 

---

### Steps of Creating ETL Processing Using SSMS

1. Create a view from the table. 
2. Create procedure from the view. Begin.
3. Declare variables, including return code, event name, event status, and event error info. 
4. Try-catch block. 
   1. Begin transaction. 
   2. ETL code.
   3. Commit transaction.
   4. Set event status, event error info, and return code.
   5. In Catch, roll back transaction, set event status, event error info, and return code. 
5. Log, including event name, event status, and event error info. 
6. Return return code.
7. End procedure. 

```sql
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

## ETL With SQL Programming 

### Two Ways to Perform ETL Process

- Flush and Fill
- Incremental loading process (using views, stored procedures, and UDFs/user defined functions)
  - SQL Merge - most efficient method 

```sql
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

### Tracking Changes over Time

- Slow Changing Dimension (SCD) Type 1: "No One Really Cares." Just overwrite the existing data and forget it.
- SCD Type 3: "What was it last time?" Tracks the previous value using separate columns.
- SCD Type 2: "I want them all!" Tracks an infinite number of versions by just adding a Version column to the table and forcing people to do only inserts instead of updates or deletes. For example, add ChangeStartDate, ChangeEndDate, and IsCurrent columns in the target table.
  - When inserting the data, set ChangeStartDate as GetDate(), IsCurrent as 'y'. 
  - When deleting the data, set ChangeEndDate as GetDate(), IsCurrent as 'n'. 
  - When updating the data, set IsCurrent as 'n'. Then insert the new data separately, and set ChangeStartDate as GetDate(), IsCurrent as 'y'. 

### Delete vs. Truncate (faster) 

- Delete: When re-fill up the table, the key will not restart from one. It just keeps going. 
- Truncate: The key will be reset. **Note:** you cannot truncate a table with foreign key constraint. You need to drop the constraint first. 

---





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

Why you should use views and stored procedures? 

![reason-to-use-view-or-stored-procedure.png](img/reason-to-use-view-or-stored-procedure.png)

---

### MS SQL Server Build-in Functions

<https://www.w3schools.com/sql/sql_ref_sqlserver.asp>

---

### Data Warehouse 

It is a **best practice** to create new **artificial surrogate key** values in the data warehouse tables, in addition to the original ID columns used to connect tables in the source database. 

![a-typical-set-of-fact-and-dimension-tables.png](img/a-typical-set-of-fact-and-dimension-tables.png)