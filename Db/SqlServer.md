# SqlServer 日常维护

## 异常处理办法

### 事务日志过大

```sql
USE [master]
GO
ALTER DATABASE [数据库名称] SET RECOVERY SIMPLE WITH NO_WAIT
GO
ALTER DATABASE [数据库名称] SET RECOVERY SIMPLE
GO
USE [DbName]
GO
# 收缩数据库事务日志文件到指定大小（M）
# 例子 DBCC SHRINKFILE (N'VehicleManagerSys_log' , 11, TRUNCATEONLY) 
DBCC SHRINKFILE (N'日志文件名称' , 11, TRUNCATEONLY) 
GO
USE [master]
GO
ALTER DATABASE [数据库名称] SET RECOVERY FULL WITH NO_WAIT
GO
ALTER DATABASE [数据库名称] SET RECOVERY FULL
GO
```

## 维护指令

### 磁盘空间占用

#### 数据库空间

```sql
# 查看数据库空间使用情况
exec sp_spaceused
```

#### 表空间

```sql

--查询所有表大小，大小倒序排序
SELECT
TableName = obj.name,
TotalRows = prt.rows,
[DataSize(KB)]=SUM(alloc.data_pages)*8, --这里返回的是数据页个数，1页是8K，所以乘以8
[DataSize(MB)]=SUM(alloc.data_pages)*8/1024.0,
[DataSize(GB)]=SUM(alloc.data_pages)*8/1024.0/1024.0,
[SpaceUsed(KB)] = SUM(alloc.used_pages)*8,--这里返回的是数据页个数，1页是8K，所以乘以8
[SpaceUsed(MB)] = SUM(alloc.used_pages)*8/1024.0,
[SpaceUsed(GB)] = SUM(alloc.used_pages)*8/1024.0/1024.0
FROM sys.objects obj
JOIN sys.indexes idx ON obj.object_id = idx.object_id
JOIN sys.partitions prt ON obj.object_id = prt.object_id
JOIN sys.allocation_units alloc ON alloc.container_id = prt.partition_id
WHERE obj.type = 'U' AND idx.index_id IN (0, 1)
GROUP BY obj.name, prt.rows
ORDER BY TotalRows DESC
```

### Cpu占用

#### 阻塞中的Sql

```sql
SELECT r.session_id as [SessionId], r.command as [Command], sqltext.TEXT, db_name(r.database_id) as [Database], r.blocking_session_id as [Blocking], r.wait_type as [Wait], r.wait_resource as [Wait Resource], s.original_login_name as [LogIn], c.client_net_address as [Client], s.host_name as [Host], s.program_name as [Program], r.status as Status, r.start_time as [Start Time], r.cpu_time as [CPU Time], r.total_elapsed_time as [Elapsed time], r.reads as [Reads], r.logical_reads as [Logical Reads], r.writes as [Writes], p.query_plan as [Plan] FROM sys.dm_exec_requests r INNER JOIN sys.dm_exec_sessions s on r.session_id = s.session_id INNER JOIN sys.dm_exec_connections c on s.session_id = c.session_id CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS sqltext OUTER APPLY sys.dm_exec_query_plan(r.plan_handle) as p WHERE r.session_id <> @@SPID ORDER BY [Elapsed Time] DESC;
```

#### 运算时间过大的Sql

```
SELECT TOP 50 total_worker_time/execution_count/1000 AS [Avg CPU Time (ms)], SUBSTRING(st.text, (qs.statement_start_offset/2)+1, ((CASE qs.statement_end_offset WHEN -1 THEN DATALENGTH(st.text) ELSE qs.statement_end_offset END - qs.statement_start_offset)/2) + 1) AS Statement, qs.creation_time as CompileTime, qs.last_execution_time as LastExecution, qs.execution_count as ExecutionCount, (qs.total_physical_reads / execution_count) as [Avg Physical Read], (qs.total_logical_reads / execution_count) as [Avg Logical Read], (qs.total_elapsed_time / execution_count / 1000) as [Avg Elapsed Time], (qs.last_physical_reads / execution_count) as [Last Physical Read], (qs.last_logical_reads / execution_count) as [Last Logical Read], (qs.last_elapsed_time / execution_count / 1000) as [Last Elapsed Time], p.query_plan as [Plan] FROM sys.dm_exec_query_stats AS qs CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) AS st OUTER APPLY sys.dm_exec_query_plan(qs.plan_handle) AS p WHERE qs.last_execution_time > DATEADD(minute, -60, getdate()) ORDER BY [Avg Elapsed Time] DESC;

```

