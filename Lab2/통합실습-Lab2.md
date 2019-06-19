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

## Test 2
```
-- 데이터베이스 생성
create database if not exists problem2;

-- 데이터 베이스 변경
use problem2;

-- 외부 테이블 생성
create external table if not exists solution (
id int,
fname string,
lname string,
address string,
city string,
state string,
zip string,
birthday string,
hireday string
)
comment "employee"
row format delimited fields terminated by "\t"
stored as parquet location "/user/hive/warehouse/employee"

-- hdfs 경로 상의 데이터 복사
hive> dfs -cp /user/training/problem2/data/employee/*.parquet /user/hive/warehouse/employee/

-- 결과
hive> select * from solution;
OK
10000	Rigel	Shaw	Ap #124-4664 Vulputate, Rd.	Cannole	OH	83380	07/12/80	05/30/18
10001	Chancellor	Bond	Ap #702-9298 Pretium Street	Piana degli Albanesi	AZ	38128	03/02/95	04/12/18
10002	Aurora	Franco	5717 Mattis. Street	Casalvieri	CA	12296	01/29/77	07/19/18
10003	Armando	Aguilar	Ap #189-3372 Nam St.	Durgapur	TX	50289	05/10/50	04/14/18
10004	Neve	Sharp	2748 Risus. Rd.	Poole	NV	23113	07/07/76	06/04/17
10005	Kenyon	Whitfield	Ap #863-352 Blandit St.	Ayr	CA	62678	12/23/66	04/30/18
10006	Germane	Pruitt	P.O. Box 266, 1496 Mus. St.	Opole	DE	21311	02/20/63	02/14/18
10007	Alyssa	Lindsey	989-4040 Habitant Av.	Lexington	TX	62013	09/03/95	05/17/17
10008	Driscoll	Bishop	P.O. Box 428, 449 Cum Ave	La Reina	NV	61197	05/18/84	12/20/16
10009	Tanisha	Rosales	5388 Vitae Street	Caramanico Terme	TX	14932	07/03/64	09/01/16
```
