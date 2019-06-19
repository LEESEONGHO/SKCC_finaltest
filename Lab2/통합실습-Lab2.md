# Part 2

## Test 1
```
-- solution.sql (경로 : /home/training/problem1/solution.sql)
select a.id as id, 
       a.type as type,
       a.status as status,
       a.amount as amount,
       round(a.amount - w.average,2) as different
from problem1.account a,
     (select type, avg(amount) as average from problem1.account group by type) w 
where a.status='Active'
and a.type = w.type
;

-- 실행 command
hive -f solution.sql

-- 결과
A100000	Basic Checking	Active	44539	-7402.48
A100002	Basic Checking	Active	11483	-40458.48
A100013	Basic Checking	Active	45654	-6287.48
A100016	Basic Checking	Active	639	-51302.48
A100018	Basic Checking	Active	63563	11621.52
A100019	Basic Checking	Active	79771	27829.52
A100020	Basic Checking	Active	34328	-17613.48
A100023	Basic Checking	Active	85337	33395.52
A100032	Basic Checking	Active	51703	-238.48
A100037	Basic Checking	Active	98233	46291.52
A100044	Basic Checking	Active	56530	4588.52
A100057	Basic Checking	Active	31550	-20391.48
A100061	Basic Checking	Active	87322	35380.52
A100069	Basic Checking	Active	44071	-7870.48
A100075	Basic Checking	Active	19327	-32614.48
A100078	Basic Checking	Active	78031	26089.52
(중략)
```
