# Employee Data Analysis – We have employee and department data and as per this we want to complete analysis. Below are the use cases for data analysis.
 
 
  * step1: Creating and Loading Data into Emp and Dept Tables in Redshift
  
  * setp2: Create the Emp and Dept tables in Redshift database.
  
  ```
  CREATE TABLE employee (
  emp_no INT,
  ename VARCHAR(100),
  job VARCHAR(100),
  mgr_id INT null,
  date_of_joining VARCHAR(100),
  salary DECIMAL,
  bonus DECIMAL null,
  dept_no DECIMAL
 );
 ```
 ```
   CREATE TABLE department (
  dept_no DECIMAL not null,
  dept_name VARCHAR(100) not null,
  loc VARCHAR(100) not null
  
);
```




Step-3: Upload both employee and department files on S3 bucket.
We need to upload the data files to S3 so that they can be accessed by Redshift


Step-4: Load employee.csv and department.csv files data to Redshift Emp and Dept table from S3 bucket.

```
COPY Emp
FROM 's3://my-bucket/employee.csv'
IAM_ROLE 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
CSV
IGNOREHEADER 1;

COPY Dept
FROM 's3://my-bucket/department.csv'
IAM_ROLE 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
CSV
IGNOREHEADER 1;

```



SQL query 1: 	Display all the columns from employee table where Job in ascending order and emp_no in descending order.

```
Select * From public.employee
order by JOB ASC, emp_no DESC;
```

SQL Query 2: Display the emp_no, ename, dept_no, dept_name, loc, salary, bonus, Total Salary (salary + bonus) of all the employees.

```
SELECT emp_no, ename, e.dept_no, dept_name, loc, salary, bonus, (salary + colease(bonus,0)) AS Total salary
FROM public.employee e
JOIN public.department d 
ON e.dept_no = d.dept_no

```

SQL Query 3: Display dept_no, number of employees of those departments where at least two employees are working.

```
SELECT dept_no, COUNT(*) as "Number of Employees"
FROM public.employee
GROUP BY dept_no
HAVING COUNT(*) >= 2;
```

SQL Query 4: Display all the columns of employee table whose enames contain the letter ‘A’.

```
SELECT *
FROM public.employee
WHERE ename LIKE '%A%';
```

SQL Query 5: Display ename, job, salary, manager name and manager's salary of those employees whose salary is higher than managers salary. 

```
SELECT e.ename, e.job, e.salary, m.ename AS "Manager Name", m.salary AS "Manager Salary"
FROM employee e
INNER JOIN employee m
ON e.mgr = m.emp_no
WHERE e.salary > m.salary;
```

## Difficulty I phase in this case study

When I loading the data from SQL copy command then I got error 1207 and 1205 then for checking main reason I dig dive into   ‘STL_LOAD_ERRORS ’ this will show the main reason.

Actually reason when DATATYPES when I created the table take limit that why it throwing error.
