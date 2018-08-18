---
layout: post
title: "Performance of SQL Prepared Statements"
date: 2018-08-18 12:37:15 +1000
categories: sql
---

A while ago, I got into a discussion about the performance of prepared statements compared to straight-up queries in SQL databases. My friend, based on his experience with Oracle Database, opined that prepared statements were much faster compared to straight queries because of query plan caching with prepared statements. I offered my counter that straight queries should be faster as it requires less network requests to the database.

The discussion went on for some time, but we both realised that this is a rather complex situation to reason about. Different databases handle statements, prepared or otherwise, differently. Some databases cache query plans beyond the closing of the statement that generated the plan, others do not. The way an application makes database requests also heavily factors into any discussion about performance.

For example, an application that issues the same query many times with different parameters would, in theory, be more performant if it would prepare 1 statement and run the query multiple times before closing the statement. Accordingly, if an application would open a prepared statement, run 1 query, close the statement, and repeat this for all other parameter variations, this application would be less performant.

As our discussion was at a stalemate without any data to proof one way or another, I set out to [test the performance of prepared statements][sql-test].

# Methodology
I choose to write the test in Go as it was really easy to write benchmark tests and would also help me learn more about the language. I have also decided to run my databases in Docker containers, and it is the path of least resistance and will minimise network latency.

I needed to measure the performance of the prepared statements as cleanly as possible, instead of measuring disk access times. As such, I decided to initiate the databases with a very small schema, comprising of a single table with a single integer column and a single row of data. This should mean that the data will be cached by the database, and the query needed to return that data is as simple as it gets.

# Caveats
The tests are best run on Linux instead of MacOS. I have come to learn that Docker for Mac has serious performance issues and will not provide consistent results.

The databases are pulled directly from Docker Hub without further customisations, and may not be configured for top performance. However, the simplicity of our database schema should negate any performance issue stemming from misconfiguration.

The tests involve MariaDB, PostgreSQL, and Microsoft SQL Server. Oracle Database is omitted from this test as there is no free version available on Docker Hub. Microsoft SQL Server, being a newly ported version from Windows to Linux, may offer different performance characteristics to its native Windows counterpart.

# Results

```
go test -bench .
goos: linux
goarch: amd64
pkg: github.com/kennethc/experiment/sqlquery
BenchmarkMysqlQuery-4                   	   10000	    106904 ns/op
BenchmarkMysqlPrepStmtInnerLoop-4       	   20000	     84820 ns/op
BenchmarkMysqlPrepStmtOuterLoop-4       	    5000	    271879 ns/op
BenchmarkPostgresQuery-4                	   10000	    166464 ns/op
BenchmarkPostgresPrepStmtInnerLoop-4    	   10000	    171808 ns/op
BenchmarkPostgresPrepStmtOuterLoop-4    	    3000	    483013 ns/op
BenchmarkSqlserverQuery-4               	    5000	    268654 ns/op
BenchmarkSqlserverPrepStmtInnerLoop-4   	    5000	    287138 ns/op
BenchmarkSqlserverPrepStmtOuterLoop-4   	    5000	    307703 ns/op
PASS
ok  	github.com/kennethc/experiment/sqlquery	14.622s
```

The same tests were run multiple times, but I have chosen to offer the above indicative results as they were very consistent.

`*Query*` - These tests run a straight-up SQL query.
`*PrepStmtInnerLoop*` - These tests open a prepared statement, run multiple queries using the same statement, then close the statement at the end.
`*PrepStmtOuterLoop*` - These tests open a prepared statement, run 1 query, close the statement, and then repeat.

# Observations
The MariaDB/MySQL results do not surprise me. MariaDB will cache query plans for prepared statements while the statements are open. So it stands to reason that Inner Loop tests are the fastest (~20% faster than straight queries), while Outer Loop tests are the slowest (over 1.5x slower than straight queries) due to all the extra overhead of opening and closing prepared statements.

The surprise came from both PostgreSQL and MS SQL Server showing some decrease in Inner Loop performance compared to straight queries (~3% for PostgreSQL and ~7% for MS SQL Server). I cannot offer any guesses as to why this is the case.

# Conclusion
While the performance difference for MariaDB is quite pronounced, we are only still talking about a difference in the microsecond range over tens of thousands of iterations, which is quite negligible in the majority of cases.

The design of the database schema, such as normalisation and indexing, and the complexity of the query will almost always be the deciding factor in any performance measure.

Prepared statements also offer protection from SQL injection attacks, which in my book is worth any potential decrease in performance!

[sql-test]: https://github.com/kennethc/experiment/sqlquery
