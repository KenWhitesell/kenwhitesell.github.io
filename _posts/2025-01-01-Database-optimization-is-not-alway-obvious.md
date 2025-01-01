---
layout: post
title: Database optimization isn't always obvious
tags: Django PostgreSQL
---

One of the issues of working with abstractions is that what you think may be logically true, isn't.

We use abstractions to hide underlying complexity of systems.

Why is this an issue?


I recently was reminded of this as the result of a blog post that was brought to my attention. The current (27 December 2024) issue of Django News (https://django-news.com/) highlighted an article titled “Django: avoid using .first() when retrieving a unique object” (https://b0uh.github.io/django-avoid-using-first-when-retrieving-a-unique-object.html).

Briefly, the article points out that if you use the construct “SomeModel.objects.filter(field=’value’).first()”, it adds an “order by” clause to the generated SQL that the expression “SomeModel.objects.get(field=’value’)” avoids. 

This is accurate.

However, the author then goes on to say “No more ordering, it is faster!” – and it’s the conclusion in _this_ statement that I question. Is it faster? I’m not sure.

It is easy to lose sight of the fact that SQL itself is an abstraction layer for the database.

Making judgments about the relative performance of two SQL statements is not trivial and cannot be accurately or definitively made without going beyond the SQL statement itself.

### Executing a query

When an SQL statement is issued to the DB, two things need to happen.

1. The SQL statement needs to be parsed and analyzed, and the database engine needs to create a “query plan” for that statement. (That’s the term used by PostgreSQL. MySQL uses the phrase “query execution plan”, Oracle refers to it as an “execution plan”, and DB2 calls them “access plans”. Despite the different names, the concept is the same in each case.)

The SQL language is not a procedural language like Python, C, Java, etc. It does not define a sequence of steps to be executed. Instead, it’s a declarative language to identify a set of constraints that must be satisfied when identifying the set of rows to be returned.

The creation of the plan depends upon a number of factors, including the size of the tables, what indexes may be present, and what predicates may be involved in the query.

A database engine will usually keep statistics about the tables in the database to help the query planner determine what the most effective query plan will be for a given query.

As a result of this, there is no requirement that the query plan include explicit operations for every clause in the original SQL statement. The query planner / query optimizer for the database is free to select and organize any defined set of operations necessary to satisfy the submitted statement. 

It is also possible that changes to the database may change the operations selected by the query planner for a given SQL statement. It’s quite reasonable to find that a query plan generated today is not the same query plan generated yesterday for exactly the same query.

2. The database engine needs to execute that plan to retrieve the requested data.

Whether the execution of the plan proceeds optimally depends upon whether the internal statistics were sufficiently accurate when the query plan was being generated. If the statistics are way off, the effects on the query could be significant. (However, due to how Django creates its queries, this is typically an unlikely situation.)

Why am I explaining all this?

Because identifying the performance characteristics of an SQL query requires a lot more information than just looking at the statement itself.

You cannot compare two SQL statements “A” and “B”, and say that “A” is faster than “B” without examining the query plans generated for each – and sometimes the results are surprising.

There’s also the question of whether the difference in performance is significant to the degree that it is worth changing your code or altering the types of statements you write to account for it. 

- If the difference in execution time is 10 seconds, I’ll definitely look at optimizing the query. 

- If the difference in execution time is 10 milliseconds, I’m probably not going to bother – unless it’s a _very_ frequently executed query in an automated process. In the case of a web request, it’s unlikely I would consider making a change.

### Testing:

I made a copy of some tables from an existing Django-project database to a new database. 

I generated 1000 User objects to be stored in `auth_user`.

I modified a table named `forwarded_rsu_packets` to add two CharFields with a  max_length=20. I then copied the first 1,000,000 rows of the original table, populating those new fields with data from other fields in that table. I created an index on one of those new fields (“smg”) and left the other new field (“noidx”) without an index.

That gave me 5 different scenarios to test:

  1. Small table (~ 1000 rows), querying for an indexed value (primary key)

  2. Small table, querying for a non-indexed value (a character field)

  3. Large table (~ 1,000,000 rows), querying for an indexed value (primary key)

  4. Large table, querying for a non-primary key indexed value.

  5. Large table, querying for a non-indexed value

For each scenario, I ran “explain analyze” on each of the two different versions of the query 100 times. I then printed the output for the fastest instance of each query.

Why the fastest? Because I’m looking to identify how quickly the database can return the results. The database isn’t running in a vacuum – it’s running on an operating system with hundreds of other things all going on at the same time, competing for resources. All of those other processes do affect the timings, which is valid when you’re looking to evaluate the overall throughput of a system, but is not valid when you’re trying to isolate the performance of the database itself.

#### Scenario 1: Querying the “auth_user” table by “id”.

```
explain analyze select "id" from auth_user where id=555 limit 1;

                                                             QUERY PLAN                                                              
-------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.28..8.29 rows=1 width=4) (actual time=0.036..0.037 rows=1 loops=1)
   ->  Index Only Scan using auth_user_pkey on auth_user  (cost=0.28..8.29 rows=1 width=4) (actual time=0.033..0.033 rows=1 loops=1)
         Index Cond: (id = 555)
         Heap Fetches: 1
 Planning Time: 0.182 ms
 Execution Time: 0.066 ms
(6 rows)
```

And

```
explain analyze select "id" from auth_user where id=555 order by "id" limit 1;

                                                             QUERY PLAN                                                              
-------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.28..8.29 rows=1 width=4) (actual time=0.027..0.029 rows=1 loops=1)
   ->  Index Only Scan using auth_user_pkey on auth_user  (cost=0.28..8.29 rows=1 width=4) (actual time=0.023..0.024 rows=1 loops=1)
         Index Cond: (id = 555)
         Heap Fetches: 1
 Planning Time: 0.176 ms
 Execution Time: 0.063 ms
(6 rows)
```

From my perspective, these results are effectively identical. The planning cost estimates are the same and the Execution time measurements are easily within any reasonable margin of measurement error. I see no reason here to choose one version over the other.

#### Scenario 2: Querying the “auth_user” table by “username”.

```
explain analyze select "id" from auth_user where username='MB' limit 1;

                                                                    QUERY PLAN                                                                    
--------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.28..8.29 rows=1 width=4) (actual time=0.047..0.049 rows=1 loops=1)
   ->  Index Scan using auth_user_username_6821ab7c_like on auth_user  (cost=0.28..8.29 rows=1 width=4) (actual time=0.045..0.045 rows=1 loops=1)
         Index Cond: ((username)::text = 'MB'::text)
 Planning Time: 0.173 ms
 Execution Time: 0.080 ms
(5 rows)
```

And

```
explain analyze select "id" from auth_user where username='MB' order by "id" limit 1;

                                                                       QUERY PLAN                                                                       
--------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=8.30..8.31 rows=1 width=4) (actual time=0.044..0.046 rows=1 loops=1)
   ->  Sort  (cost=8.30..8.31 rows=1 width=4) (actual time=0.043..0.044 rows=1 loops=1)
         Sort Key: id
         Sort Method: quicksort  Memory: 25kB
         ->  Index Scan using auth_user_username_6821ab7c_like on auth_user  (cost=0.28..8.29 rows=1 width=4) (actual time=0.033..0.036 rows=1 loops=1)
               Index Cond: ((username)::text = 'MB'::text)
 Planning Time: 0.153 ms
 Execution Time: 0.069 ms
(8 rows)
```

Again, the difference between the two is negligible. In theory, the version without the sort could be expected to be faster, since it shows the lower planning cost, but that’s not how it worked out in practice.

#### Scenario 3: Querying the “forwarded_rsu_packet” table by “id”:

```
explain analyze select id from forwarded_rsu_packet where "id" = 555555 limit 1;

                                                                        QUERY PLAN                                                                         
-----------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.42..8.44 rows=1 width=8) (actual time=0.048..0.049 rows=1 loops=1)
   ->  Index Only Scan using forwarded_rsu_packet_pkey on forwarded_rsu_packet  (cost=0.42..8.44 rows=1 width=8) (actual time=0.046..0.046 rows=1 loops=1)
         Index Cond: (id = 555555)
         Heap Fetches: 1
 Planning Time: 0.132 ms
 Execution Time: 0.071 ms
(6 rows)
```

And

```
explain analyze select id from forwarded_rsu_packet where "id" = 555555 order by id limit 1;

                                                                        QUERY PLAN                                                                         
-----------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.42..8.44 rows=1 width=8) (actual time=0.044..0.045 rows=1 loops=1)
   ->  Index Only Scan using forwarded_rsu_packet_pkey on forwarded_rsu_packet  (cost=0.42..8.44 rows=1 width=8) (actual time=0.042..0.042 rows=1 loops=1)
         Index Cond: (id = 555555)
         Heap Fetches: 1
 Planning Time: 0.150 ms
 Execution Time: 0.067 ms
(6 rows)
```

Again, negligible difference. What I actually noticed here was the effect of the PostgreSQL caches on the execution times. The first couple of tests of both queries were significantly slower – into the hundreds of millisecond ranges, but then dropped quickly into the 0.1 ms area.

#### Scenario 4: Querying the “forwarded_rsu_packet” table by “smg” (an indexed 20 character field).

```
explain analyze select id from forwarded_rsu_packet where smg = '4e530a8f184314b4676b' limit 1;
                                                                      QUERY PLAN                                                                       
-------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.42..8.44 rows=1 width=8) (actual time=0.051..0.052 rows=1 loops=1)
   ->  Index Scan using forwarded_r_smg_e77b86_idx on forwarded_rsu_packet  (cost=0.42..8.44 rows=1 width=8) (actual time=0.050..0.050 rows=1 loops=1)
         Index Cond: ((smg)::text = '4e530a8f184314b4676b'::text)
 Planning Time: 0.127 ms
 Execution Time: 0.074 ms
(5 rows)

```
And

```
explain analyze select id from forwarded_rsu_packet where smg = '4e530a8f184314b4676b' order by id limit 1;
                                                                         QUERY PLAN                                                                          
-------------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=8.45..8.46 rows=1 width=8) (actual time=0.060..0.061 rows=1 loops=1)
   ->  Sort  (cost=8.45..8.46 rows=1 width=8) (actual time=0.059..0.059 rows=1 loops=1)
         Sort Key: id
         Sort Method: quicksort  Memory: 25kB
         ->  Index Scan using forwarded_r_smg_e77b86_idx on forwarded_rsu_packet  (cost=0.42..8.44 rows=1 width=8) (actual time=0.049..0.051 rows=1 loops=1)
               Index Cond: ((smg)::text = '4e530a8f184314b4676b'::text)
 Planning Time: 0.192 ms
 Execution Time: 0.086 ms
(8 rows)
```

For the first time, the version without the “order by” clause is faster. What I consider the more significant difference though is not the Execution time, but the Planning time. The version with the “order by” required 50% more time, which far outweighed any difference between the Execution times.

#### Scenario 5: Querying the “forwarded_rsu_packet” table by “noidx” (an unindexed 20 character field)

```
explain analyze select id from forwarded_rsu_packet where noidx = '4e530a8f184314b4676b' limit 1;
                                                                QUERY PLAN                                                                 
-------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=1000.00..93756.11 rows=1 width=8) (actual time=178.552..191.465 rows=1 loops=1)
   ->  Gather  (cost=1000.00..93756.11 rows=1 width=8) (actual time=178.548..191.458 rows=1 loops=1)
         Workers Planned: 2
         Workers Launched: 2
         ->  Parallel Seq Scan on forwarded_rsu_packet  (cost=0.00..92756.01 rows=1 width=8) (actual time=125.632..125.633 rows=0 loops=3)
               Filter: ((noidx)::text = '4e530a8f184314b4676b'::text)
               Rows Removed by Filter: 284324
 Planning Time: 0.271 ms
 Execution Time: 191.500 ms
(9 rows)
```

And

```
explain analyze select id from forwarded_rsu_packet where noidx = '4e530a8f184314b4676b' order by id limit 1;
                                                                   QUERY PLAN                                                                    
-------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=93756.12..93756.13 rows=1 width=8) (actual time=138.248..148.649 rows=1 loops=1)
   ->  Sort  (cost=93756.12..93756.13 rows=1 width=8) (actual time=138.245..148.645 rows=1 loops=1)
         Sort Key: id
         Sort Method: quicksort  Memory: 25kB
         ->  Gather  (cost=1000.00..93756.11 rows=1 width=8) (actual time=138.100..148.633 rows=1 loops=1)
               Workers Planned: 2
               Workers Launched: 2
               ->  Parallel Seq Scan on forwarded_rsu_packet  (cost=0.00..92756.01 rows=1 width=8) (actual time=99.179..131.234 rows=0 loops=3)
                     Filter: ((noidx)::text = '4e530a8f184314b4676b'::text)
                     Rows Removed by Filter: 284504
 Planning Time: 0.269 ms
 Execution Time: 148.685 ms
(12 rows)
```

The first thing that jumps out at me is how much slower it is to search this large table without an index. The execution time for the unindexed version is about 2000 times slower. That’s the best example I can see for showing the value of ensuring the proper indexes exist on larger tables.

But overall, the version with the “order by” clause was clearly consistently faster than the version without the “order by”. Even though the planning costs come out effectively identical, the difference in Execution time was very consistent. Of the 100 tests run with “order by”, 80 of them yielded an Execution time less than 160 ms, compared to the single fastest run of the version without “order by” being more than 190 ms, with the majority of runs requiring more than 220 ms.

### Conclusion:
As with any benchmark, these specific numbers are useless outside the context of my application running on a particular server with specific hardware and configuration settings. I have no idea whether anyone else could replicate these results.

However, what I have determined to my own satisfaction is that the decision whether to use `.get()` or `.filter().first()` is not a trivial decision with a simple, direct answer. The situation is far more nuanced than that, and if you believe that fractional milliseconds are critical, then you’ll want to test your queries on your hardware with your data.

