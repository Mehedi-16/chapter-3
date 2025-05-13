# chapter-3

<img width="673" alt="Screenshot 2025-05-14 at 12 25 32 AM" src="https://github.com/user-attachments/assets/633015fd-79ff-413d-8b1e-946e9af9a695" />


Instructor Queries
🔸 Basic Instructor Info
1.	Find out the ID and salary of the instructors.
2.	Find out the ID and salary of the instructor who gets more than $85,000.
3.	List out the names of the instructors from Computer Science who have more than $70,000.
4.	Find the average salary of all instructors.
5.	Find the names of instructors with salary amounts between $90,000 and $100,000.
6.	Find the highest salary of any instructor.
7.	Find all instructors earning the highest salary (there may be more than one with the same salary).


### ✅ 1. Find out the ID and salary of the instructors.

```sql
SELECT ID, salary
FROM instructor;
```

---

### ✅ 2. Find out the ID and salary of the instructor who gets more than \$85,000.

```sql
SELECT ID, salary
FROM instructor
WHERE salary > 85000;
```

---

### ✅ 3. List out the names of the instructors from Computer Science who have more than \$70,000.

```sql
SELECT name
FROM instructor
WHERE dept_name = 'Comp. Sci.' AND salary > 70000;
```

---

### ✅ 4. Find the average salary of all instructors.

```sql
SELECT AVG(salary) AS average_salary
FROM instructor;
```

---

### ✅ 5. Find the names of instructors with salary amounts between \$90,000 and \$100,000.

```sql
SELECT name
FROM instructor
WHERE salary BETWEEN 90000 AND 100000;
```

---

### ✅ 6. Find the highest salary of any instructor.

```sql
SELECT MAX(salary) AS highest_salary
FROM instructor;
```

---

### ✅ 7. Find all instructors earning the highest salary (there may be more than one with the same salary).

```sql
SELECT name, salary
FROM instructor
WHERE salary = (SELECT MAX(salary) FROM instructor);
```

🔸 Instructor and Course Relations

4. For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.
5. Find the instructor names and the courses they taught for all instructors in the Biology department who have taught some course.
6. Display a list of all instructors, showing each instructor's ID and the number of sections taught. Make sure to show the number of sections as 0 for instructors who have not taught any section.
7. Output instructor names and buildings for each building an instructor has taught in. Include instructor names who have not taught any classes (the building name should be NULL in this case).


### 4. **For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.**

```sql
SELECT instructor.name, course.course_id
FROM instructor, course
WHERE instructor.ID = course.instructor_ID;
```

---

### 5. **Find the instructor names and the courses they taught for all instructors in the Biology department who have taught some course.**

```sql
SELECT instructor.name, course.course_id
FROM instructor, course
WHERE instructor.ID = course.instructor_ID
AND instructor.dept_name = 'Biology';
```

---

### 6. **Display a list of all instructors, showing each instructor's ID and the number of sections taught. Make sure to show the number of sections as 0 for instructors who have not taught any section.**

```sql
SELECT instructor.ID, 
       (SELECT COUNT(*)
        FROM section
        WHERE section.instructor_ID = instructor.ID) AS num_sections
FROM instructor;
```

---

### 7. **Output instructor names and buildings for each building an instructor has taught in. Include instructor names who have not taught any classes (the building name should be NULL in this case).**

```sql
SELECT instructor.name, 
       (SELECT building
        FROM section
        WHERE section.instructor_ID = instructor.ID) AS building
FROM instructor;
```

---

---

### 5. **Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.**

```sql
SELECT name
FROM instructor
WHERE salary > (SELECT MIN(salary) FROM instructor WHERE dept_name = 'Biology');
```

এখানে `MIN(salary)` ব্যবহার করে, আমরা Biology ডিপার্টমেন্টের সর্বনিম্ন বেতন খুঁজে বের করেছি, তারপর অন্যান্য ইনস্ট্রাক্টরদের সাথে তুলনা করেছি।

---

### 6. **Find the ID and names of all instructors whose salary is greater than at least one instructor in the History department.**

```sql
SELECT ID, name
FROM instructor
WHERE salary > (SELECT MIN(salary) FROM instructor WHERE dept_name = 'History');
```

Salary Comparisons
5. Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.
6. Find the ID and names of all instructors whose salary is greater than at least one instructor in the History department.
7. Find the names of all instructors that have a salary value greater than that of each instructor in the Biology department.
8. Find the departments that have the highest average salary. *
9. Find the average instructors' salaries of those departments where the average salary is greater than $42,000.
10. Find the maximum across all departments of the total salary at each department.

---

### 7. **Find the names of all instructors that have a salary value greater than that of each instructor in the Biology department.**

```sql
SELECT name
FROM instructor
WHERE salary > ANY (SELECT salary FROM instructor WHERE dept_name = 'Biology');
```

এখানে `ALL/ANY` ব্যবহার করে আমরা নিশ্চিত করছি যে, যে ইনস্ট্রাক্টরের বেতন **Biology** ডিপার্টমেন্টের সকল ইনস্ট্রাক্টরের বেতনের তুলনায় বেশি।

---

### 8. **Find the departments that have the highest average salary.**

```sql
SELECT dept_name,AVG(salary)
FROM instructor
GROUP BY dept_name
ORDER BY AVG(salary) DESC
LIMIT 1;
```
এখানে:<br>
ORDER BY avg_salary DESC দিয়ে ডিপার্টমেন্টগুলিকে বড় থেকে ছোট গড় বেতনের ক্রমে সাজানো হয়েছে।<br>
LIMIT 1 দিয়ে বলা হচ্ছে: শুধু সবচেয়ে উপরের ১টি রেকর্ড দেখাও, অর্থাৎ সবচেয়ে বেশি গড় বেতন যার, শুধু সেই ডিপার্টমেন্ট।

---

### 9. **Find the average instructors' salaries of those departments where the average salary is greater than \$42,000.**

```sql
SELECT dept_name, AVG(salary)
FROM instructor
GROUP BY dept_name
HAVING AVG(salary) > 42000;
```
HAVING দিয়ে ফিলটার করা হয় আর ORDER BY দিয়ে sort করা হয়... <br>
এখানে আমরা ডিপার্টমেন্ট অনুযায়ী গড় বেতন বের করছি এবং যেগুলোর গড় বেতন \$42,000-এর বেশি, সেগুলোকেই নির্বাচন করছি।

---

### 10. **Find the maximum total salary across all departments.**

```sql
SELECT dept_name
FROM instructor
GROUP BY dept_name
HAVING SUM(salary) = (SELECT MAX(SUM(salary)) FROM instructor GROUP BY dept_name);
```
---
Salary Updates

### 22. **Find the salaries after the following operation: Increase the salary of each instructor in the Comp. Sci. department by 10%.**

```sql
UPDATE instructor
SET salary = salary * 1.10
WHERE dept_name = 'Comp. Sci.';
```

এই কুয়েরির মাধ্যমে, **Comp. Sci.** ডিপার্টমেন্টের প্রতিটি ইনস্ট্রাক্টরের বেতন ১০% বাড়িয়ে দেওয়া হবে।

এখানে `salary * 1.10` দ্বারা বেতনকে ১০% বাড়ানো হয়েছে।


---

🔹 Student Queries
🔸 Course Enrollment
18.	Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result. *
19.	Find out the student ID and grades who take a course(s) in Spring-2009 semester.
20.	Find the room no. and the building where the student from Fall-2009 semester can take a course(s).
21.	Find student names and the number of law courses taken for students who have taken at least half of the available law courses.
22.	Find the rank and name of the 10 students who earned the most A grades (A-, A, A+). Use alphabetical order by name to break ties.
23.	Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result. *
24.	Find the ID and name of each student who has taken at least one Comp. Sci. course; make sure there are no duplicate names in the result.


### 🔸 **18. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.**

```sql
SELECT DISTINCT student_id
FROM enrollment
WHERE course_id IN (
    SELECT course_id
    FROM course
    WHERE instructor_name = 'Einstein'
);
```

---

### 🔸 **19. Find out the student ID and grades who take a course(s) in Spring-2009 semester.**

```sql
SELECT student_id, grade
FROM enrollment
WHERE semester = 'Spring-2009';
```

---

### 🔸 **20. Find the room no. and the building where the student from Fall-2009 semester can take a course(s).**

```sql
SELECT room_no, building
FROM course
WHERE course_id IN (
    SELECT course_id
    FROM enrollment
    WHERE semester = 'Fall-2009'
);
```

---

### 🔸 **21. Find student names and the number of law courses taken for students who have taken at least half of the available law courses.**

```sql
SELECT student_name, 
       COUNT(course_id) AS law_courses_taken
FROM enrollment
WHERE course_id IN (
    SELECT course_id
    FROM course
    WHERE dept_name = 'Law'
)
GROUP BY student_name
HAVING COUNT(course_id) >= (
    SELECT COUNT(course_id) / 2
    FROM course
    WHERE dept_name = 'Law'
);
```

---

### 🔸 **22. Find the rank and name of the 10 students who earned the most A grades (A-, A, A+). Use alphabetical order by name to break ties.**

```sql
SELECT student_name,
       RANK() OVER (ORDER BY COUNT(grade) DESC, student_name) AS rank
FROM enrollment
WHERE grade IN ('A-', 'A', 'A+')
GROUP BY student_name
LIMIT 10;
```

---

### 🔸 **23. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.**

```sql
SELECT DISTINCT student_id
FROM enrollment
WHERE course_id IN (
    SELECT course_id
    FROM course
    WHERE instructor_name = 'Einstein'
);
```

---

### 🔸 **24. Find the ID and name of each student who has taken at least one Comp. Sci. course; make sure there are no duplicate names in the result.**

```sql
SELECT DISTINCT student_id, student_name
FROM enrollment
WHERE course_id IN (
    SELECT course_id
    FROM course
    WHERE dept_name = 'Comp. Sci.'
);
```
Advisors and Identification
7.	Find the advisor of the student with ID 12345.
🔸 Retaken Courses
28.	For each student who has retaken a course at least twice (i.e., the student has taken the course at least three times), show the course ID and the student's ID.
🔸 Misc
9.	Find the enrollment of each section that was offered in Autumn-2009. *

---

### 🔸 **7. Find the advisor of the student with ID 12345.**

```sql
SELECT advisor
FROM student
WHERE student_id = 12345;
```

---

### 🔸 **28. For each student who has retaken a course at least twice (i.e., the student has taken the course at least three times), show the course ID and the student's ID.**

```sql
SELECT student_id, course_id
FROM enrollment
GROUP BY student_id, course_id
HAVING COUNT(course_id) >= 3;
```

---

### 🔸 **9. Find the enrollment of each section that was offered in Autumn-2009.**

```sql
SELECT section_id, course_id, student_id
FROM enrollment
WHERE semester = 'Autumn-2009';
```

---

Department Queries
🔸 Department Basic Info
3.	Find out the department names and their budget at the university.
4.	Find the names of all departments whose building name includes the substring Watson.
5.	List all departments along with the number of instructors in each department.
6.	Find the names of those departments whose budget is higher than that of Astronomy.
🔸 Department Salary Stats
10.	Find the average salary of the instructor in each department. *
11.	Find the number of instructors in each department who teach a course in the Spring-2010 semester.
12.	List out the departments where the average salary of the instructors is more than $42,000.
13.	Find the average instructors' salaries of those departments where the average salary is greater than $42,000. *



---

### 🔸 **3. Find out the department names and their budget at the university.**

```sql
SELECT dept_name, budget
FROM department;
```

---

### 🔸 **4. Find the names of all departments whose building name includes the substring Watson.**

```sql
SELECT dept_name
FROM department
WHERE building LIKE '%Watson%';
```

---

### 🔸 **5. List all departments along with the number of instructors in each department.**

```sql
SELECT dept_name, 
       (SELECT COUNT(*) FROM instructor WHERE dept_name = department.dept_name) AS num_instructors
FROM department;
```

---

### 🔸 **6. Find the names of those departments whose budget is higher than that of Astronomy.**

```sql
SELECT dept_name
FROM department
WHERE budget > (SELECT budget FROM department WHERE dept_name = 'Astronomy');
```

---

### 🔸 **10. Find the average salary of the instructor in each department.**

```sql
SELECT dept_name, 
       (SELECT AVG(salary) FROM instructor WHERE dept_name = department.dept_name) AS avg_salary
FROM department;
```

---

### 🔸 **11. Find the number of instructors in each department who teach a course in the Spring-2010 semester.**

```sql
SELECT dept_name, 
       (SELECT COUNT(DISTINCT instructor_id) FROM enrollment 
        WHERE enrollment.semester = 'Spring-2010' 
        AND enrollment.course_id IN (SELECT course_id FROM course WHERE dept_name = department.dept_name)) AS num_instructors
FROM department;
```

---

### 🔸 **12. List out the departments where the average salary of the instructors is more than \$42,000.**

```sql
SELECT dept_name
FROM department
WHERE (SELECT AVG(salary) FROM instructor WHERE dept_name = department.dept_name) > 42000;
```

---

### 🔸 **13. Find the average instructors' salaries of those departments where the average salary is greater than \$42,000.**

```sql
SELECT dept_name, 
       (SELECT AVG(salary) FROM instructor WHERE dept_name = department.dept_name) AS avg_salary
FROM department
HAVING avg_salary > 42000;
```

---
🔹 Course & Section Queries
🔸 General Course Info
5.	Find the set of all courses taught either in Fall-2009 or in Spring-2010.
6.	Find the set of all courses taught in the Fall-2009 as well as in Spring-2010.
7.	Find all courses taught in the Fall-2009 semester but not in the Spring-2010 semester.
8.	Find the courses taught in Fall-2009 semester. *
9.	Find all courses taught in both the Fall 2009 semester and in the Spring-2010 semester. *
10.	Find all courses that were offered at most once in 2009.
11.	Find out the course titles which start between 10:00 to 12:00.
12.	Find the titles of courses in the Comp. Sci. department that have 3 credits.


---

### ✅ 5. Find the set of all courses taught either in Fall-2009 or in Spring-2010.

```sql
SELECT DISTINCT course_id
FROM course
WHERE semester IN ('Fall-2009', 'Spring-2010');
```

---

### ✅ 6. Find the set of all courses taught in both Fall-2009 and Spring-2010.

```sql
SELECT DISTINCT course_id
FROM course
WHERE semester = 'Fall-2009'
AND course_id IN (SELECT course_id FROM course WHERE semester = 'Spring-2010');
```

---

### ✅ 7. Find all courses taught in Fall-2009 but not in Spring-2010.

```sql
SELECT DISTINCT course_id
FROM course
WHERE semester = 'Fall-2009'
AND course_id NOT IN (SELECT course_id FROM course WHERE semester = 'Spring-2010');
```

---

### ✅ 8. Find all courses taught in the Fall-2009 semester.

```sql
SELECT DISTINCT course_id
FROM course
WHERE semester = 'Fall-2009';
```

---

### ✅ 9. Find all courses taught in both the Fall-2009 semester and the Spring-2010 semester.

```sql
SELECT DISTINCT course_id
FROM course
WHERE semester = 'Fall-2009'
AND course_id IN (SELECT course_id FROM course WHERE semester = 'Spring-2010');
```

---

### ✅ 10. Find all courses that were offered at most once in 2009.

```sql
SELECT course_id
FROM course
WHERE YEAR(semester) = 2009
GROUP BY course_id
HAVING COUNT(course_id) = 1;
```

---

### ✅ 11. Find the course titles that start between 10:00 AM to 12:00 PM.

```sql
SELECT course_title
FROM course
WHERE start_time BETWEEN '10:00' AND '12:00';
```

---

### ✅ 12. Find the titles of courses in the Computer Science department that have 3 credits.

```sql
SELECT course_title
FROM course
WHERE dept_name = 'Comp. Sci.' AND credits = 3;
```

---

Sections and Buildings
20.	List all course sections offered by the Physics department in the Fall-2009 semester, with the building and room number of each section.
21.	Find the student who takes the maximum credit from each department.
22.	Find the sections that had maximum enrollment in Fall 2010.
23.	Find the maximum enrollment, across all sections, in Autumn-2009. *



### 20. **List all course sections offered by the Physics department in Fall-2009 semester, with the building and room number.**

```sql
SELECT section_id, building, room_no
FROM section
WHERE dept_name = 'Physics' 
  AND semester = 'Fall-2009';
```

### 21. **Find the student who takes the maximum credit from each department.**

```sql
SELECT student_id
FROM enrollment, course
WHERE enrollment.course_id = course.course_id
GROUP BY student_id
ORDER BY SUM(course.credits) DESC
LIMIT 1;
```

### 22. **Find the sections that had maximum enrollment in Fall-2010.**

```sql
SELECT section_id
FROM enrollment, section
WHERE enrollment.section_id = section.section_id
  AND semester = 'Fall-2010'
GROUP BY section_id
HAVING COUNT(student_id) = (SELECT MAX(count)
                            FROM (SELECT section_id, COUNT(student_id) AS count
                                  FROM enrollment, section
                                  WHERE enrollment.section_id = section.section_id
                                  AND semester = 'Fall-2010'
                                  GROUP BY section_id) AS subquery);
```

### 23. **Find the maximum enrollment, across all sections, in Autumn-2009.**

```sql
SELECT MAX(count)
FROM (SELECT section_id, COUNT(student_id) AS count
      FROM enrollment, section
      WHERE enrollment.section_id = section.section_id
      AND semester = 'Autumn-2009'
      GROUP BY section_id) AS subquery;
```

Course-Student-Instructor Interactions
🔸 Enrollment Stats
10.	Find all the courses taught in both the Fall-2009 and Spring-2010 semesters. *
11.	Find the total number of (distinct) students who have taken course sections taught by the instructor with ID 110011.
12.	Find the total number of instructors who teach a course in the Spring-2010 semester.
13.	Find out the total salary of the instructors of the Computer Science department who take a course(s) in Watson building. *
🔸 Pre-requisites and Performance
21.	List the course names where CS-1019 is the pre-requisite course.
22.	List the student names who get more than B+ grades in their respective courses.
23.	Find the titles of courses in the Comp. Sci. department that have 3 credits. *
🔸 Unique Enrollments
36.	Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result. *
37.	Find all students who have not taken a course.
38.	Find all students who have taken all the courses offered in the Biology department.



### **Enrollment Stats**

**10. Find all the courses taught in both the Fall-2009 and Spring-2010 semesters.**

```sql
SELECT course_id
FROM section
WHERE semester = 'Fall-2009'
INTERSECT
SELECT course_id
FROM section
WHERE semester = 'Spring-2010';
```

**11. Find the total number of (distinct) students who have taken course sections taught by the instructor with ID 110011.**

```sql
SELECT COUNT(DISTINCT student_id)
FROM enrollment
WHERE section_id IN (
    SELECT section_id
    FROM section
    WHERE instructor_id = 110011
);
```

**12. Find the total number of instructors who teach a course in the Spring-2010 semester.**

```sql
SELECT COUNT(DISTINCT instructor_id)
FROM section
WHERE semester = 'Spring-2010';
```

**13. Find out the total salary of the instructors of the Computer Science department who take a course(s) in the Watson building.**

```sql
SELECT SUM(instructor.salary)
FROM instructor
WHERE instructor.dept_name = 'Comp. Sci.'
AND instructor.instructor_id IN (
    SELECT section.instructor_id
    FROM section
    WHERE section.building = 'Watson'
);
```

### **Pre-requisites and Performance**

**21. List the course names where CS-1019 is the pre-requisite course.**

```sql
SELECT course_name
FROM course
WHERE prerequisite = 'CS-1019';
```

**22. List the student names who get more than B+ grades in their respective courses.**

```sql
SELECT student.name
FROM student, enrollment
WHERE student.student_id = enrollment.student_id
AND enrollment.grade > 'B+';
```

**23. Find the titles of courses in the Comp. Sci. department that have 3 credits.**

```sql
SELECT course_name
FROM course
WHERE dept_name = 'Comp. Sci.' AND credits = 3;
```
Course-Student-Instructor Interactions
🔸 Enrollment Stats
10.	Find all the courses taught in both the Fall-2009 and Spring-2010 semesters. *
11.	Find the total number of (distinct) students who have taken course sections taught by the instructor with ID 110011.
12.	Find the total number of instructors who teach a course in the Spring-2010 semester.
13.	Find out the total salary of the instructors of the Computer Science department who take a course(s) in Watson building. *
🔸 Pre-requisites and Performance
21.	List the course names where CS-1019 is the pre-requisite course.
22.	List the student names who get more than B+ grades in their respective courses.
23.	Find the titles of courses in the Comp. Sci. department that have 3 credits. *
🔸 Unique Enrollments
36.	Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result. *
37.	Find all students who have not taken a course.
38.	Find all students who have taken all the courses offered in the Biology department.


### **Unique Enrollments**

**36. Find the IDs of all students who were taught by an instructor named Einstein; make sure there are no duplicates in the result.**

```sql
SELECT DISTINCT enrollment.student_id
FROM enrollment, instructor, section
WHERE instructor.name = 'Einstein'
AND instructor.instructor_id = section.instructor_id
AND enrollment.section_id = section.section_id;
```

**37. Find all students who have not taken a course.**

```sql
SELECT student_id
FROM student
WHERE student_id NOT IN (SELECT DISTINCT student_id FROM enrollment);
```

**38. Find all students who have taken all the courses offered in the Biology department.**

```sql
SELECT student_id
FROM enrollment
WHERE student_id NOT IN (
    SELECT student_id
    FROM enrollment
    WHERE course_id NOT IN (SELECT course_id FROM course WHERE dept_name = 'Biology')
)
GROUP BY student_id
HAVING COUNT(DISTINCT course_id) = (SELECT COUNT(course_id) FROM course WHERE dept_name = 'Biology');
```
Here are simplified SQL queries for your requests:

### **Enrollment Stats**

**10. Find all the courses taught in both the Fall-2009 and Spring-2010 semesters.**

```sql
SELECT course_id
FROM section
WHERE semester = 'Fall-2009'
AND course_id IN (SELECT course_id FROM section WHERE semester = 'Spring-2010');
```

**11. Find the total number of distinct students who have taken course sections taught by the instructor with ID 110011.**

```sql
SELECT COUNT(DISTINCT student_id)
FROM enrollment
WHERE section_id IN (
    SELECT section_id FROM section WHERE instructor_id = 110011
);
```

**12. Find the total number of instructors who teach a course in the Spring-2010 semester.**

```sql
SELECT COUNT(DISTINCT instructor_id)
FROM section
WHERE semester = 'Spring-2010';
```

**13. Find the total salary of instructors in the Computer Science department who take a course in Watson building.**

```sql
SELECT SUM(salary)
FROM instructor
WHERE dept_name = 'Comp. Sci.'
AND instructor_id IN (
    SELECT instructor_id FROM section WHERE building = 'Watson'
);
```

### **Pre-requisites and Performance**

**21. List the course names where CS-1019 is the pre-requisite course.**

```sql
SELECT course_name
FROM course
WHERE prerequisite = 'CS-1019';
```

**22. List the student names who get more than B+ grades in their respective courses.**

```sql
SELECT student_name
FROM student
WHERE student_id IN (
    SELECT student_id FROM enrollment WHERE grade > 'B+'
);
```

**23. Find the titles of courses in the Comp. Sci. department that have 3 credits.**

```sql
SELECT course_name
FROM course
WHERE dept_name = 'Comp. Sci.' AND credits = 3;
```

### **Unique Enrollments**

**36. Find the IDs of all students who were taught by an instructor named Einstein.**

```sql
SELECT DISTINCT student_id
FROM enrollment
WHERE section_id IN (
    SELECT section_id FROM section WHERE instructor_id IN (
        SELECT instructor_id FROM instructor WHERE name = 'Einstein'
    )
);
```

**37. Find all students who have not taken a course.**

```sql
SELECT student_id
FROM student
WHERE student_id NOT IN (SELECT student_id FROM enrollment);
```

**38. Find all students who have taken all the courses offered in the Biology department.**

```sql
SELECT student_id
FROM enrollment
WHERE student_id NOT IN (
    SELECT student_id FROM enrollment WHERE course_id NOT IN (
        SELECT course_id FROM course WHERE dept_name = 'Biology'
    )
)
GROUP BY student_id
HAVING COUNT(DISTINCT course_id) = (
    SELECT COUNT(course_id) FROM course WHERE dept_name = 'Biology'
);
```






