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

## Test 3
```
-- 데이터베이스 변경
use problem3;

-- 테이블 생성
create external table if not exists solution (
id int,
fname string,
lname string,
hphone string
)
comment "problem3"
row format delimited fields terminated by "\t"
stored as orc location "/user/training/problem3/solution"
;

-- 데이터적재
insert into problem3.solution
select a.id
, a.fname
, a.lname
, a.hphone
from problem3.customer a
     inner join
     problem3.account b
     on a.id = b.custid
where 1=1
  and b.amount < 0
;

-- 결과
hive> select * from solution ;
OK
10001	Sybil	Wiley	(504) 780-0366
10010	Brittany	Martinez	(341) 462-0222
10011	Merritt	Roth	(341) 344-3753
10015	Amaya	Weeks	(979) 852-8703
10023	Jermaine	Barnes	(245) 674-2743
10026	Lacy	Rios	(227) 499-8358
10036	Laith	Kirk	(312) 466-1766
10045	Chancellor	Hart	(913) 147-2294
10046	Olga	Hall	(615) 645-2920
10053	Jonah	Alvarado	(578) 584-0882
10054	Brady	Cunningham	(631) 358-1802
10056	Yoshio	Benton	(875) 483-7221
10068	Maggy	Whitney	(886) 286-7557
10073	Ariel	Jordan	(422) 191-3529
10080	Margaret	Mercer	(695) 261-7712
10092	Christian	Baker	(659) 732-3384
10093	Brandon	Rhodes	(344) 635-8512
10094	Orli	Nolan	(459) 228-5694
10096	Blaze	Gill	(834) 777-5765
```

## Test 4
```
-- 데이터베이스 생성
create database if not exists problem4;

-- 데이터베이스 변경
use problem4;

-- employee1 테이블 생성
create external table if not exists employee1 (
cust_id int,
fname string,
lname string,
adress string,
city string,
state string,
zip string
)
COMMENT "employee1"
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\t"
STORED AS TEXTFILE LOCATION "/user/training/problem4/data/employee1/"
;

-- employee2 테이블 생성
create external table if not exists employee2 (
cust_id int,
digit string,
fname string,
lname string,
adress string,
city string,
state string,
zip string
)
COMMENT "employee2"
ROW FORMAT DELIMITED FIELDS TERMINATED BY ","
STORED AS TEXTFILE LOCATION "/user/training/problem4/data/employee2/"
;

-- solution 테이블 생성
create external table if not exists solution (
cust_id int,
fname string,
lname string,
adress string,
city string,
state string,
zip string
)
COMMENT "problem4"
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\t"
STORED AS TEXTFILE LOCATION "/user/training/problem4/solution/"
;

-- solution 테이블에 데이터 적재
insert into solution
select *
from employee1
where 1=1
and state = 'CA'
union all
select cust_id
, fname
, lname
, adress
, city
, state
, zip
from employee2
where 1=1
and state = 'CA'
;

-- 결과
hive> select * from solution ;
OK
10000063	Burton	Hayes	Ap #720-4012 Vivamus Avenue	San Diego	CA	96066-0000
10000068	Ria	Herman	2974 Cras St.	San Francisco	CA	95310-0000
10000073	Daquan	Roy	7636 Et Rd.	Los Angeles	CA	96606-0000
10010024	WOOD	PRESTON	2782 Amet Street	San Francisco	CA	92124
10010032	ROJAS	BO	Ap #445-6043 Massa Av.	San Diego	CA	92363
10010037	BANKS	ZANE	4778 Interdum. St.	San Diego	CA	92120
10010079	JUSTICE	URSA	3321 Risus. Ave	San Jose	CA	96850
10010088	BOOTH	DECLAN	4048 Nunc Rd.	San Francisco	CA	96324
```

## Test 5
```
-- solution.sql (경로 : /home/training/problem5)

use problem5;

select concat_ws('\t',fname , lname, city, state)
from customer c
where c.city = 'Palo Alto'
and   c.state = 'CA'
union all
select concat_ws('\t',fname , lname, city, state)
from employee e
where e.city = 'Palo Alto'
and   e.state = 'CA'
;

-- 실행 cmd
$ hive -f ./solution.sql

-- 결과
$ hive -f ./solution.sql

Farrah	Preston	Palo Alto	CA
Brielle	Hudson	Palo Alto	CA
Nelle	Kim	Palo Alto	CA
Tatum	Jacobs	Palo Alto	CA
Ivan	Gentry	Palo Alto	CA
Naida	Tran	Palo Alto	CA
Alden	Daniels	Palo Alto	CA
Maggy	Mcdaniel	Palo Alto	CA
Griffin	Pate	Palo Alto	CA
Burton	Hayes	Palo Alto	CA
Ali	Barker	Palo Alto	CA
Jasper	Lara	Palo Alto	CA
```

## Test 6
```
-- 데이터베이스 변경
use problem6 ;

-- solution 테이블 생성
create external table solution
(
  id int , 
  fname string,
  lname string, 
  address string,
  city string,
  state string,
  zip string,
  birthday string
 )
;

-- solution 테이블에 데이터 적재
insert into table solution 
select id,
       fname,
       lname,
       address,
       city,
       state,
       zip,
       substr(birthday,1,5)
from employee;

-- 결과
hive> select * from solution limit 10;
OK
10000000	Deanna	Lane	900-1514 Vitae, Rd.	Lafayette	LA	97827	08/31
10000001	Hall	Garrett	9656 Urna Avenue	Tucson	AZ	86511	08/24
10000002	Lucian	Dotson	P.O. Box 277, 4808 Fusce St.	Kearney	NE	57731	08/12
10000003	Yuri	Sherman	Ap #399-8275 Molestie Road	Kapolei	HI	16943	08/26
10000004	Jaime	Griffin	Ap #647-2123 Quis Rd.	Madison	WI	51394	08/13
10000005	Zorita	Weber	747-9424 Orci, Av.	Hattiesburg	MS	90262	08/09
10000006	Mara	Meadows	517-4594 Ac, Rd.	Huntsville	AL	35374	08/11
10000007	Evan	Richard	P.O. Box 223, 8182 Non, Av.	College	AK	99682	08/25
10000008	Briar	Anderson	Ap #548-6452 Nunc Road	Cleveland	OH	90704	08/18
10000009	Cole	Odom	P.O. Box 962, 2496 Sodales St.	Boston	MA	27282	08/21
```

## Test 7
```
-- solution.sql (경로 : /home/training/problem7)

select concat(a.fname,' ' , a.lname)
from (
select e.*
from employee e
where e.city = 'Seattle'
order by fname,lname
) a
;

-- 수행 command
hive -f ./solution.sql
```

## Test 8
```
-- 데이터베이스 생성
create database if not exists problem8;

-- 데이터베이스 변경
use problem8;

-- sqoop export 명령어
$ sqoop export \
> --table solution \
> --connect "jdbc:mysql://localhost/problem8" \
> --username cloudera \
> --password cloudera \
> --export-dir "/user/training/problem8/data/customer/" \
> --fields-terminated-by "\t" \
> --columns "id, fname , lname , address , city, state , zip , birthday" ;

-- mysql 접속 및 데이터 확인
$ mysql -u cloudera -p
Enter password: cloudera

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| dualcore           |
| hue                |
| metastore          |
| mysql              |
| problem8           |
| test               |
+--------------------+
7 rows in set (0.01 sec)

mysql> use problem8;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select * from solution limit 10;
+----------+--------+----------+--------------------------------+-------------+-------+-------+------------+
| id       | fname  | lname    | address                        | city        | state | zip   | birthday   |
+----------+--------+----------+--------------------------------+-------------+-------+-------+------------+
| 10000000 | Deanna | Lane     | 900-1514 Vitae, Rd.            | Lafayette   | LA    | 97827 | 08/31/2016 |
| 10000001 | Hall   | Garrett  | 9656 Urna Avenue               | Tucson      | AZ    | 86511 | 08/24/2016 |
| 10000002 | Lucian | Dotson   | P.O. Box 277, 4808 Fusce St.   | Seattle     | WA    | 57731 | 08/12/2016 |
| 10000003 | Yuri   | Sherman  | Ap #399-8275 Molestie Road     | Kapolei     | HI    | 16943 | 08/26/2016 |
| 10000004 | Jaime  | Griffin  | Ap #647-2123 Quis Rd.          | Madison     | WI    | 51394 | 08/13/2016 |
| 10000005 | Zorita | Weber    | 747-9424 Orci, Av.             | Hattiesburg | MS    | 90262 | 08/09/2016 |
| 10000006 | Mara   | Meadows  | 517-4594 Ac, Rd.               | Huntsville  | AL    | 35374 | 08/11/2016 |
| 10000007 | Evan   | Richard  | P.O. Box 223, 8182 Non, Av.    | College     | AK    | 99682 | 08/25/2016 |
| 10000008 | Briar  | Anderson | Ap #548-6452 Nunc Road         | Cleveland   | OH    | 90704 | 08/18/2016 |
| 10000009 | Cole   | Odom     | P.O. Box 962, 2496 Sodales St. | Boston      | MA    | 27282 | 08/21/2016 |
+----------+--------+----------+--------------------------------+-------------+-------+-------+------------+
10 rows in set (0.00 sec)

```

## Test 9
```
-- 데이터베이스 변경
use problem9;

-- 테이블 생성
create external table solution 
( 
 id string,
 fname string,
 lname string,
 address string,
 city string,
 state string,
 zip string,
 birthday string
);

-- 데이터 적재
insert into solution
select distinct concat('A',id) as id,
       fname,
       lname,
       address,
       city,
       state,
       zip,
       birthday
from customer;

-- 결과
select* from solution limit 10;

hive> select * from solution limit 10;
OK
A1000000	Medge	Roach	P.O. Box 799, 6865 Nec Rd.	Racine	WI	56336	08/10/2016
A1000001	Nasim	Stone	P.O. Box 975, 759 Scelerisque Street	Tuscaloosa	AL	36696	08/16/2016
A1000002	Jolie	Schneider	P.O. Box 829, 9011 Vulputate St.	Kapolei	HI	59913	08/22/2016
A1000003	Lacota	Molina	Ap #831-2124 Pharetra. Avenue	Philadelphia	PA	84716	08/24/2016
A1000004	Blaine	Sweet	P.O. Box 151, 7847 Pede. St.	Topeka	KS	66452	08/21/2016
A1000005	Lesley	Bird	P.O. Box 569, 6635 Maecenas St.	Dallas	TX	87918	08/26/2016
A1000006	Sydnee	Howell	P.O. Box 147, 3714 Dignissim Street	Dallas	TX	94072	08/11/2016
A1000007	Jermaine	Griffin	Ap #859-2722 Donec Rd.	Baltimore	MD	13392	08/10/2016
A1000008	Brynn	Pennington	Ap #968-9936 Eleifend Avenue	Lincoln	NE	89536	08/23/2016
A1000009	Ava	Noble	P.O. Box 955, 1459 Urna St.	Baton Rouge	LA	34822	08/08/2016
Time taken: 0.082 seconds, Fetched: 10 row(s)
```
