# Теория баз данных.

## Тема: Объединения.

## Данные в таблицах:

<img width="738" alt="image" src="https://github.com/user-attachments/assets/1f62be6b-9f94-4fae-81aa-590365276730">

<img width="787" alt="image" src="https://github.com/user-attachments/assets/b8500f48-65da-4e76-8a10-402942ea42af">

<img width="779" alt="image" src="https://github.com/user-attachments/assets/2eff016f-6fc2-461b-bb48-04b3daffbe79">

<img width="770" alt="image" src="https://github.com/user-attachments/assets/91233b01-e478-47fb-a1dd-3c761d25e5ad">

<img width="769" alt="image" src="https://github.com/user-attachments/assets/9145b1c9-392a-4149-9f68-5c4059b31277">


## Запросы по заданию:

1. Вывести названия аудиторий, в которых читает лекции преподаватель “Edward Hopper”.

### Скрипт запроса:
* SELECT lr.Name
* FROM LectureRooms lr
* JOIN Schedules s ON lr.Id = s.LectureRoomId
* JOIN Lectures l ON s.LectureId = l.Id
* JOIN Teachers t ON l.TeacherId = t.Id
* WHERE t.Name = N'Edward' AND t.Surname = N'Hopper';
  
### Результат:

<img width="614" alt="image" src="https://github.com/user-attachments/assets/87c363ac-46cf-4644-af44-92e00ef037f4">

2. Вывести фамилии ассистентов, читающих лекции в группе “F505”.

### Скрипт запроса:
* SELECT t.Surname
* FROM Assistants a
* JOIN Teachers t ON a.TeacherId = t.Id
* JOIN Lectures l ON t.Id = l.TeacherId
* JOIN GroupsLectures gl ON l.Id = gl.LectureId
* JOIN Groups g ON gl.GroupId = g.Id
* WHERE g.Name = N'F505';

### Результат:

<img width="638" alt="image" src="https://github.com/user-attachments/assets/a72b083d-cebb-418a-8ae1-e21c911a913a">

3. Вывести дисциплины, которые читает преподаватель “Alex Carmack” для групп 5-го курса.

### Скрипт запроса:
* SELECT s.Name
* FROM Subjects s
* JOIN Lectures l ON s.Id = l.SubjectId
* JOIN Teachers t ON l.TeacherId = t.Id
* JOIN GroupsLectures gl ON l.Id = gl.LectureId
* JOIN Groups g ON gl.GroupId = g.Id
* WHERE t.Name = N'Alex' AND t.Surname = N'Carmack' AND g.Year = 5;

### Результат:

<img width="664" alt="image" src="https://github.com/user-attachments/assets/edf410c1-b9b0-46b9-ba47-7d950fa3a27c">

4. Вывести фамилии преподавателей, которые не читают лекции по понедельникам.

### Скрипт запроса:
* SELECT DISTINCT t.Surname
* FROM Teachers t
* WHERE t.Id NOT IN (
  * SELECT l.TeacherId
    * FROM Lectures l
    * JOIN Schedules s ON l.Id = s.LectureId
    * WHERE s.DayOfWeek = 1);

### Результат:

<img width="741" alt="image" src="https://github.com/user-attachments/assets/8f7c120a-568b-4643-b7ff-10c956b6f141">

5. Вывести названия аудиторий, с указанием их корпусов,в которых нет лекций в среду второй недели на третьей паре.

### Скрипт запроса:
* SELECT lr.Name, lr.Building
* FROM LectureRooms lr
* WHERE lr.Id NOT IN (
    * SELECT s.LectureRoomId
    * FROM Schedules s
    * WHERE s.DayOfWeek = 3 AND s.Week = 2 AND s.Class = 3);

### Результат:

<img width="668" alt="image" src="https://github.com/user-attachments/assets/99c90936-a68f-47ae-a460-24377679210d">

6. Вывести полные имена преподавателей факультета “Computer Science”, которые не курируют группы кафедры “Software Development”.

### Скрипт запроса:
* SELECT DISTINCT t.Name, t.Surname
* FROM Teachers t
* JOIN Deans d ON t.Id = d.TeacherId
* JOIN Faculties f ON d.Id = f.DeanId
* LEFT JOIN GroupsCurators gc ON t.Id = gc.CuratorId
* LEFT JOIN Groups g ON gc.GroupId = g.Id
* LEFT JOIN Departments dpt ON g.DepartmentId = dpt.Id
* WHERE f.Name = N'Computer Science'
* AND (dpt.Name <> N'Software Development' OR dpt.Name IS NULL);

### Результат:

<img width="710" alt="image" src="https://github.com/user-attachments/assets/12924b0c-6bcf-435a-8909-94e157540919">

7. Вывести список номеров всех корпусов, которые имеются в таблицах факультетов, кафедр и аудиторий.

### Скрипт запроса:
* SELECT DISTINCT Building
* FROM (
    * SELECT Building FROM Faculties
    * UNION
    * SELECT Building FROM Departments
    * UNION
    * SELECT Building FROM LectureRooms) AS Buildings;

### Результат:

<img width="663" alt="image" src="https://github.com/user-attachments/assets/d1c81fee-faa2-4878-8db5-bf77857c9498">

8. Вывести полные имена преподавателей в следующем порядке: деканы факультетов, заведующие кафедрами, преподаватели, кураторы, ассистенты.

### Скрипт запроса:
* SELECT t.Name, t.Surname
* FROM Teachers t
* JOIN Deans d ON t.Id = d.TeacherId
* UNION ALL
* SELECT t.Name, t.Surname
* FROM Teachers t
* JOIN Heads h ON t.Id = h.TeacherId
* UNION ALL
* SELECT t.Name, t.Surname
* FROM Teachers t
* WHERE t.Id NOT IN (SELECT TeacherId FROM Deans) AND t.Id NOT IN (SELECT TeacherId FROM Heads)
* UNION ALL
* SELECT t.Name, t.Surname
* FROM Teachers t
* JOIN Curators c ON t.Id = c.TeacherId
* UNION ALL
* SELECT t.Name, t.Surname
* FROM Teachers t
* JOIN Assistants a ON t.Id = a.TeacherId;

### Результат:

<img width="796" alt="image" src="https://github.com/user-attachments/assets/03fdfaf6-dd2e-4f1d-bd52-d10990c78995">

<img width="809" alt="image" src="https://github.com/user-attachments/assets/6b5052a4-9b96-41e7-a5d7-b0e2295dafa6">

<img width="808" alt="image" src="https://github.com/user-attachments/assets/2cfae367-0268-4075-b6c1-9cb64e8e656d">

9. Вывести дни недели (без повторений), в которые имеются занятия в аудиториях “A311” и “A104” корпуса 6.

### Скрипт запроса:
* SELECT DISTINCT s.DayOfWeek
* FROM Schedules s
* JOIN LectureRooms lr ON s.LectureRoomId = lr.Id
* WHERE (lr.Name = N'A311' OR lr.Name = N'A104') AND lr.Building = 6;

### Результат:

<img width="713" alt="image" src="https://github.com/user-attachments/assets/8969f126-3316-4f03-80c0-a34ec0e2acf9">



   
