# sql-challenge
Sql Analysis

Data Modeling:
I begin with designing and defining the tables that will hold our data.
titles
-
title_id VARCHAR PK
title VARCHAR 


employees
-
emp_no INT PK
emp_title_id VARCHAR FK - titles.title_id
birth_date DATE 
first_name VARCHAR
last_name VARCHAR
sex VARCHAR
hire_date DATE

departments
-
dept_no VARCHAR PK 
dept_name VARCHAR 



dept_manager
-
emp_no INT PK FK - employees.emp_no
dept_no VARCHAR PK FK - departments.dept_no


dept_emp
-
emp_no INT PK FK - employees.emp_no
dept_no VARCHAR PK FK - departments.dept_no


salaries
-
emp_no INT PK FK - employees.emp_no
salary INT

Next, I created an Entity Relationship Diagram (ERD) to visualize how these tables are related to each other.

<img width="904" alt="Screenshot 2023-10-04 at 12 45 59 PM" src="https://github.com/aamanhassan/sql-challenge/assets/139508376/9514990a-8717-42c2-b3d7-8b10fa454621">

Data Engineering:
After that for Data Engineering,I created tables in PostgreSQL, I organized them into schemas. Then impoted csv files making sure to follow a correct order. 

CREATE TABLE titles(
	title_id VARCHAR PRIMARY KEY,
	title VARCHAR NOT NULL
);

SELECT * FROM titles;

CREATE TABLE employees(
	emp_no INT NOT NULL,
	emp_title_id VARCHAR NOT NULL,
	birth_date DATE NOT NULL, 
	first_name VARCHAR NOT NULL,
	last_name VARCHAR NOT NULL, 
	sex VARCHAR NOT NULL,
	hire_date DATE NOT NULL,
	PRIMARY KEY(emp_no),
	FOREIGN KEY(emp_title_id) REFERENCES titles(title_id)
);

SELECT * FROM employees;

CREATE TABLE departments(
	dept_no VARCHAR NOT NULL,
	dept_name VARCHAR NOT NULL,
	PRIMARY KEY(dept_no)	
);

SELECT * FROM departments;


CREATE TABLE dept_manager(
	dept_no VARCHAR NOT NULL,
	emp_no INT NOT NULL,
	PRIMARY KEY(dept_no,emp_no),
	FOREIGN KEY(emp_no) REFERENCES employees(emp_no),
	FOREIGN KEY(dept_no) REFERENCES departments(dept_no)
);

SELECT * FROM dept_manager;


CREATE TABLE dept_emp(
	emp_no INT NOT NULL,
	dept_no VARCHAR NOT NULL,
	PRIMARY KEY(emp_no,dept_no),
	FOREIGN KEY(dept_no) REFERENCES departments(dept_no),
	FOREIGN KEY(emp_no) REFERENCES employees(emp_no)
);
SELECT * FROM dept_emp;

CREATE TABLE salaries(
	emp_no INT NOT NULL,
	salary INT NOT NULL,
	PRIMARY KEY(emp_no),
	FOREIGN KEY(emp_no) REFERENCES employees(emp_no)
	
);
SELECT * FROM salaries;


Data Analysis:
Following that, I executed queries in PostgreSQL and conducted data analysis.

-- List the employee number, last name, first name, sex, and salary of each employee.
SELECT emp.emp_no,
	emp.last_name,
	emp.first_name,
	emp.sex,
	sal.salary
FROM employees as emp
	LEFT JOIN salaries as sal
	ON emp.emp_no = sal.emp_no
	ORDER BY emp.emp_no;

-- List the first name, last name, and hire date for the employees who were hired in 1986.
SELECT first_name,
	last_name,
	hire_date
FROM employees 
WHERE hire_date BETWEEN '1996-01-01' AND '1996-12-31'
ORDER BY hire_date;


-- List the manager of each department along with their department number, department name, employee number, last name, and first name.

SELECT d.dept_no,
	d.dept_name,
	dm.emp_no,
	emp.last_name,
	emp.first_name
FROM dept_manager as dm
INNER JOIN departments as d
ON dm.dept_no = d.dept_no
INNER JOIN employees as emp
ON dm.emp_no = emp.emp_no;


-- List the department number for each employee along with that employee’s employee number, last name, first name, and department name.
SELECT emp.emp_no,
	emp.last_name,
	emp.first_name,
	d.dept_no,
	d.dept_name,
FROM employees as emp
INNER JOIN dept_emp as de
ON de.emp_no = emp.emp_no
INNER JOIN departments as d
ON d.dept_no = de.dept_no
ORDER BY emp.emp_no;

-- List first name, last name, and sex of each employee whose first name is Hercules and whose last name begins with the letter B.
SELECT first_name,
	last_name,
	sex
FROM employees
WHERE first_name = 'Hercules' AND last_name LIKE 'B%';



-- List each employee in the Sales department, including their employee number, last name, and first name.
SELECT emp.emp_no,
	emp.last_name,
	emp.first_name,
	d.dept_name
FROM employees as emp
INNER JOIN dept_emp as de
ON de.emp_no = emp.emp_no
INNER JOIN departments as d 
ON de.dept_no = d.dept_no
WHERE d.dept_name = 'Sales'
ORDER BY emp.emp_no;


-- List each employee in the Sales and Development departments, including their employee number, last name, first name, and department name.
SELECT emp.emp_no,
	emp.last_name,
	emp.first_name,
	d.dept_name
FROM employees as emp
INNER JOIN dept_emp as de
ON de.emp_no = emp.emp_no
INNER JOIN departments as d 
ON de.dept_no = d.dept_no
WHERE d.dept_name IN ('Sales','Development')
ORDER BY emp.emp_no;

-- List the frequency counts, in descending order, of all the employee last names (that is, how many employees share each last name).
SELECT last_name, COUNT(last_name)
FROM employees
GROUP BY last_name
ORDER by COUNT(last_name)DESC;





