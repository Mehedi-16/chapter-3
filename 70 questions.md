-- 🔹 Instructor Queries

-- 1. Find out the ID and salary of the instructors.
SELECT ID, salary FROM instructor;

-- 2. Find out the ID and salary of the instructor who gets more than $85,000.
SELECT ID, salary FROM instructor WHERE salary > 85000;

-- 3. List out the names of the instructors from Computer Science who have more than $90,000.
SELECT name FROM instructor WHERE dept_name = 'Computer Science' AND salary > 90000;

-- 4. Find the average salary of all instructors.
SELECT AVG(salary) AS average_salary FROM instructor;

-- 5. Find the names of instructors with salary amounts between $90,000 and $100,000.
SELECT name FROM instructor WHERE salary BETWEEN 90000 AND 100000;

-- 6. Find the highest salary of any instructor.
SELECT MAX(salary) FROM instructor;

-- 7. Find all instructors earning the highest salary.
SELECT name FROM instructor WHERE salary = (SELECT MAX(salary) FROM instructor);


-- 🔸 Instructor and Course Relations

-- 4. For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught.
SELECT instructor.name, teaches.course_id FROM instructor JOIN teaches ON instructor.ID = teaches.ID;

-- 5. Find the instructor names and the courses they taught for all instructors in the Biology department who have taught some course.
SELECT instructor.name, teaches.course_id FROM instructor JOIN teaches ON instructor.ID = teaches.ID WHERE instructor.dept_name = 'Biology';

-- 6. Display a list of all instructors with the number of sections taught. Show 0 if no sections taught.
SELECT instructor.ID, COUNT(teaches.section_id) AS num_sections FROM instructor LEFT JOIN teaches ON instructor.ID = teaches.ID GROUP BY instructor.ID;

-- 7. Output instructor names and buildings where they have taught. Show NULL for building if not taught any classes.
SELECT instructor.name, section.building FROM instructor LEFT JOIN teaches ON instructor.ID = teaches.ID LEFT JOIN section ON teaches.course_id = section.course_id AND teaches.sec_id = section.sec_id AND teaches.semester = section.semester AND teaches.year = section.year;


-- 🔸 Salary Comparisons

-- 5. Find the names of all instructors whose salary is greater than at least one instructor in the Biology department.
SELECT name FROM instructor WHERE salary > ANY (SELECT salary FROM instructor WHERE dept_name = 'Biology');

-- 6. Find the ID and names of all instructors whose salary is greater than at least one instructor in the History department.
SELECT ID, name FROM instructor WHERE salary > ANY (SELECT salary FROM instructor WHERE dept_name = 'History');

-- 7. Find the names of all instructors whose salary is greater than every instructor in the Biology department.
SELECT name FROM instructor WHERE salary > ALL (SELECT salary FROM instructor WHERE dept_name = 'Biology');

-- 8. Find the departments with the highest average salary.
SELECT dept_name, AVG(salary) FROM instructor GROUP BY dept_name ORDER BY AVG(salary) DESC LIMIT 1;

-- 9. Find the average instructors' salaries of departments where average salary > $42,000.
SELECT dept_name, AVG(salary) FROM instructor GROUP BY dept_name HAVING AVG(salary) > 42000;

-- 10. Find the department with the highest total salary.
SELECT dept_name, SUM(salary) FROM instructor GROUP BY dept_name ORDER BY SUM(salary) DESC LIMIT 1;


-- 🔸 Salary Updates

-- 22. Increase salary by 10% for all instructors in the Comp. Sci. department.
UPDATE instructor SET salary = salary * 1.10 WHERE dept_name = 'Comp. Sci.';


-- 🔸 Miscellaneous Student Queries

-- 18. Find the IDs of all students taught by instructor named Einstein (no duplicates).
SELECT DISTINCT takes.ID FROM teaches JOIN instructor ON teaches.ID = instructor.ID JOIN takes ON teaches.course_id = takes.course_id AND teaches.sec_id = takes.sec_id AND teaches.semester = takes.semester AND teaches.year = takes.year WHERE instructor.name = 'Einstein';

-- 19. Find student ID and grades for courses taken in Spring 2009.
SELECT ID, grade FROM takes WHERE semester = 'Spring' AND year = 2009;

-- 20. Find the room no. and building for students in Fall 2009.
SELECT DISTINCT section.building, section.room_number FROM section JOIN takes ON section.course_id = takes.course_id AND section.sec_id = takes.sec_id AND section.semester = takes.semester AND section.year = takes.year WHERE takes.semester = 'Fall' AND takes.year = 2009;

-- 21. Students who took at least half of the Law courses; show name and number taken.
SELECT student.name, COUNT(takes.course_id) AS num_law_courses FROM student JOIN takes ON student.ID = takes.ID JOIN course ON takes.course_id = course.course_id WHERE course.dept_name = 'Law' GROUP BY student.ID, student.name HAVING COUNT(takes.course_id) >= (SELECT COUNT(*)/2 FROM course WHERE dept_name = 'Law');

-- 22. Rank and name of top 10 students with most A grades.
SELECT RANK() OVER (ORDER BY COUNT(*) DESC, student.name) AS rank, student.name FROM takes JOIN student ON takes.ID = student.ID WHERE grade IN ('A', 'A-', 'A+') GROUP BY student.ID, student.name LIMIT 10;

-- 24. ID and name of students who took at least one Comp. Sci. course.
SELECT DISTINCT student.ID, student.name FROM student JOIN takes ON student.ID = takes.ID JOIN course ON takes.course_id = course.course_id WHERE course.dept_name = 'Comp. Sci.';


-- 🔸 Advisors and Identification

-- 7. Advisor of student with ID 12345.
SELECT advisor FROM student WHERE ID = 12345;


-- 🔸 Retaken Courses

-- 28. Students who retook a course at least twice (i.e., 3 times or more).
SELECT course_id, ID FROM takes GROUP BY course_id, ID HAVING COUNT(*) >= 3;


-- 🔸 Section Enrollment

-- 9. Enrollment count for each section in Fall 2009.
SELECT course_id, sec_id, semester, year, COUNT(ID) AS enrollment FROM takes WHERE semester = 'Fall' AND year = 2009 GROUP BY course_id, sec_id, semester, year;


-- 🔸 Department Information

-- 3. Department names and budgets.
SELECT dept_name, budget FROM department;

-- 4. Departments where building name contains 'Watson'.
SELECT dept_name FROM department WHERE building LIKE '%Watson%';

-- 5. Departments with number of instructors.
SELECT dept_name, COUNT(ID) AS num_instructors FROM instructor GROUP BY dept_name;

-- 6. Departments with a higher budget than Astronomy.
SELECT dept_name FROM department WHERE budget > (SELECT budget FROM department WHERE dept_name = 'Astronomy');
