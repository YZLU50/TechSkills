# SQL Practice

Tables used in the problems below <br>

# Problems

1) Query students with higher scores in course 002 than in course 001, return student ID

``` stylus
select
  s1.StudentNo
from
  score s1,
  score s2
where
  s1.CourseNo = 001
  and
  s2.CourseNo = 002
  and
  s1.StudentNo = s2.StudentNo
  and
  s1.score > s2.score
```
<br>

2) Query students with average scores > 60, return student ID and average scores
``` stylus
select
  s1.StudentNo,
  AVG(s1.score)
from
  score s1
  group by s1.StudentNo
  having AVG(s1.score) > 60
```
<br>

3) Return student ID, names, total number of courses, total scores

``` stylus
select
  stu1.studentNo,
  stu1.name,
  count(s1.CourseNo),
  sum(s1.score)
from
  score s1,
  student stu1
where
  s1.StudentNo = stu1.studentNo
  group by s1.StudentNo
```
<br>

4) Return number of teachers with last name as "Smith"

``` stylus
select
  count(*)
from
  teacher t1
where
  t1.name like '%Smith'
```
<br>

5) Return ID, names of students that are not in Casey Johnson's course(s)

``` stylus
select
  stu1.studentNo,
  stu1.name
from
  student stu1
where
  stu1.studentNo not in
  (
  select distinct
    s1.StudentNo
  from
    score s1,
    course c1,
    teacher t1
  where
    s1.CourseNo = c1.courseNo
    and
    c1.teacherNo = t1.teacherNo
    and
    t1.name = 'Casey Johnson'
  )
```
<br>

6) Return ID, names of students that took both course 001 and 002

``` stylus
select
  stu1.studentNo,
  stu1.name
from
  score s1,
  student stu1
where
  s1.StudentNo = stu1.studentNo
  and
  s1.CourseNo = 001
  and
  s1.StudentNo in
  (
  select distinct
    s2.StudentNo
  from
    score s2
  where
    s2.CourseNo = 002
  )
```
<br>

7) Return ID, names of students that took Casey Johnson's course(s)

``` stylus
select
  s1.StudentNo,
  stu1.name
from
  score s1,
  student stu1,
  course c1,
  teacher t1
where
  s1.StudentNo = stu1.studentNo
  and
  s1.CourseNo = c1.courseNo
  and
  c1.teacherNo = t1.teacherNo
  and
  t1.name = 'Casey Johnson'
  group by s1.StudentNo
  having count(c1.courseNo)=
  (
    select
      count(c2.courseNo)
    from
      course c2,
      teacher t2
    where
      c2.teacherNo = t2.teacherNo
      and
      t2.name = 'Casey Johnson'
   )
```
<br>


8）Reutrn ID, names of students with at least one score < 60

``` stylus
select distinct
  s1.StudentNo,
  stu1.name
from
  score s1,
  student stu1
where
  s1.StudentNo = stu1.studentNo
  and
  s1.score < 60
```
<br>

9) Return ID, names of students that didn't take every course

``` stylus
select
  s1.StudentNo,
  stu1.name
from
  score s1,
  student stu1
where
  s1.StudentNo = stu1.studentNo
  group by s1.StudentNo
  having count(s1.CourseNo) <
  (
  select
    count(*)
  from
    course c1
  )
```
<br>

10) Return ID, names of students that have exact same courses as student 0001

``` stylus
select distinct
  s1.StudentNo,
  stu1.name
from
  score s1,
  student stu1
where
  s1.StudentNo = stu1.studentNo
  and
  s1.CourseNo in
  (
  select
    s2.CourseNo
  from
    score s2
  where
    s2.StudentNo = 0001
  )
```
<br>

11）Update the scores to average scores for all Casey Johnson's courses

``` stylus
UPDATE
  score s,
  (
  SELECT
    s1.CourseNo as courseNo,
    AVG(s1.score) as avgScore
  FROM
    score s1,
    course c1,
    teacher t1
  WHERE
    s1.CourseNo = c1.CourseNo
    AND
    c1.teacherNo = t1.teacherNo
    AND
    t1.name = 'Casey Johnson'
  GROUP BY s1.CourseNo
  ) as t
SET
  s.score = t.avgScore
WHERE
  s.CourseNo = t.courseNo
```
<br>


12）Delete score records for Casey Johnson's courses

``` stylus
delete from
  score s1
where
  s1.CourseNo in
  (
  select
    c1.courseNo
  from
    course c1,
    teacher t1
  where
    c1.teacherNo = t1.teacherNo
    and
    t1.name = 'Casey Johnson'
  )

```
<br>

13) Insert into score table for students that didn't take course 002. Set score as course average

``` stylus
insert into score(StudentNo, courseNo, score)
(
select
  stu1.StudentNo,
  "002",
  (
  select
    avg(s1.score)
  from
    score s1
  where
    s1.CourseNo = 2
  )
from
  student stu1
where
  stu1.studentNo not in
  (
  select
    s2.StudentNo
  from
    score s2
  where
    s2.CourseNo = 2
  )
)
```
<br>

14) Return ID, scores for Literature, Math, English, number of courses, average scores. Order by average scores

``` stylus
select
  s.StudentNo,
  (select s1.score from score s1 where s1.CourseNo=1 and s1.StudentNo = s.StudentNo) as "Literature",
  (select s2.score from score s2 where s2.CourseNo=2 and s2.StudentNo = s.StudentNo) as "Math",
  (select s3.score from score s3 where s3.CourseNo=3 and s3.StudentNo = s.StudentNo) as "English",
  COUNT(s.CourseNo) as "number of courses",
  AVG(s.score) as "Avg_Score"
from
  score s
  group by s.StudentNo
  order by AVG(s.score)
```

<br>
<br>

15) Return Course ID, highest score, lowest score for all courses

``` stylus
select
  s1.CourseNo as 'CourseID',
  max(s1.score) as 'TopScore',
  min(s1.score) as 'LowestScore'
from
  score s1
  group by s1.CourseNo
```
<br>

16) Return Course ID, average score and pass rate. Order by average score and pass rate DESC

``` stylus
select
  t0.CourseNo,
  t0.avgScore,
  ifnull(t1.passNum, 0),
  t0.amount,
  ifnull(t1.passNum, 0) / t0.amount * 100 as 'passRate'
from
  (
  select
    s1.CourseNo,
    avg(s1.score) as 'avgScore',
    count(s1.score) as 'amount'
  from
    score s1
    group by s1.CourseNo
  )t0
  natural left outer join
  (
  select
    s2.CourseNo,
    count(s2.StudentNo) as 'passNum'
  from
    score s2
  where
    s2.score > 60
    group by s2.CourseNo
  )t1
  order by t0.avgScore, passRate desc
```
<br>
<br>


17) Return Course ID, Course Names and scores by the following brackets: (100-85),(85-70),(70-60),(Less than 60)

``` stylus
select
  s1.CourseNo,
  c1.name,
  sum(case when s1.score between 85 and 100 then 1 else 0 end) as "85-100",
  sum(case when s1.score between 70 and 85 then 1 else 0 end) as "70-85",
  sum(case when s1.score between 60 and 70 then 1 else 0 end) as "60-70",
  sum(case when s1.score < 60 then 1 else 0 end) as "<60"
from
  score s1,
  course c1
where
  s1.CourseNo = c1.courseNo
  group by s1.CourseNo
```
<br>

18) Return top three students in each course

``` stylus
select
  *
from
  score s
where
  (
  select
    count(*)
  from
    score s1
  where
    s1.CourseNo = s.CourseNo
    and
    s1.score > s.score
  ) < 3
order by s.CourseNo
```
<br>
<br>

19) Return students born in 2001

``` stylus
select
  stu1.name,
  stu1.age
from
  student stu1
where
  YEAR(CURRENT_DATE())-stu1.age = 1991
```
<br>

20) Return students with average scores over 85

``` stylus
select
  stu1.studentNo,
  stu1.name,
  avg(s1.score)
from
  score s1,
  student stu1
where
  s1.StudentNo = stu1.studentNo
  group by s1.StudentNo
  having avg(s1.score)>85
```
<br>
