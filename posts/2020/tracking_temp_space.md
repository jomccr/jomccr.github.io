## Summary

We had an issue at work where every so often a job would get stuck on a long-running query. It was
pulling a day's worth of adds/changes/deletes for a few different tables, combining them all into a 
file and shipping it out to a third party. 

We noticed that whenever it was stuck, it would chew through all of the temp space available for 
the few other production processes running at the time and then eventually it would fail trying to 
use more than we had allocated in the temp tablespace. 

I thought it could potentially be useful to track the amount of temp space used by each query over
time. We could use this to investigate whether or not there were any queries finishing around the 
same time this was starting, potentially using the same underlying objects, etc. **we did not come 
to a conclusion... the point of this post is to detail the monitoring process I ended up with.**

## Design

The main idea I had was to write a stored procedure that I could run whenever I wanted to capture a
"snapshot" of the tempspace usage. Most of our queries are scheduled out of a custom scheduling 
software that was written in-house long ago (think something like apache airflow). It seemed like 
overkill for such a small task and the overhead of tracking the "flow" through to the end, handling 
issues, etc just seemed like more trouble than it was worth. Because of this, I stuck with the age
old classic of running a script out of my crontab. 

So I wrote two scripts - one shell and one SQL. 

## Details

This code isn't complex and is merely a monitoring tool that I mostly use nowadays, but it 
could be useful in learning how monitoring works as a student. 

```sql
select v.tablespace, v.username, v.sql_id_tempseg, 
       sysdate as log_dt,
       sum(v.blocks) as blocks,
       (sum(v.blocks) * 32768) / 1024 / 1024 / 1024 as gb_temp_used
from v$tempseg_usage v
group by v.tablespace, v.username, v.sql_id_tempseg;
```

This is the query I started out with with an example row below: 

| TABLESPACE | USERNAME | SQL_ID_TEMPSEG | LOG_DT         | BLOCKS | GB_TEMP_USED |
|------------|----------|----------------|----------------|--------|--------------|
| TEMP_C     | USER1    | datuu8ahwrx5z  | 6/23/2020 9:06 | 32     | 0.000976563  |

We can see here that `USER1` is running a query that is using 32 blocks of `TEMP_C`. We need to 
wrap the SQL in a procedure. 

The procedure: 

```sql
create or replace procedure LOG_TEMPSPACE_USAGE as
BEGIN
    insert into jomccr.tempspace_tb (tablespace, username, sql_id_tempseg, log_dt, blocks, gb_temp_used) 
    select v.tablespace, v.username, v.sql_id_tempseg, 
           sysdate as log_dt,
           sum(v.blocks) as blocks,
           (sum(v.blocks) * 32768) / 1024 / 1024 / 1024 as gb_temp_used
    from v$tempseg_usage v
    group by v.tablespace, v.username, v.sql_id_tempseg;

    commit;
END;
```

































