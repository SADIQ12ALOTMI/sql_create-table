## السوال الاول

**إنشاء جدول الموظفين:**

يُنشئ كود SQL التالي جدول `employees` ويضيف إليه بيانات أولية:

```sql
CREATE TABLE employees (
  employee_id int PRIMARY KEY,
  first_name varchar(50) NOT NULL,
  salary decimal(10,2) NOT NULL
);

INSERT INTO employees (employee_id, first_name, salary)
VALUES (1, 'John Doe', 50000.00),
       (2, 'Jane Doe', 45000.00),
       (3, 'Mike Smith', 60000.00);
```

يُنشئ هذا الكود جدولًا باسم `employees` بثلاثة أعمدة:

* `employee_id`: رقم صحيح يحدد كل موظف بشكل فريد (المفتاح الأساسي)
* `first_name`: سلسلة تحتوي على اسم الموظف الأول
* `salary`: قيمة عشرية تمثل راتب الموظف

**تحديث الرواتب باستخدام مؤشرات ضمنية:**

يُستخدم كود SQL التالي لتحديث الرواتب باستخدام المؤشرات الضمنية:

```sql
DECLARE employee_cursor CURSOR FOR
SELECT employee_id, salary FROM employees;

OPEN employee_cursor;

FETCH employee_cursor INTO @employee_id, @salary;

WHILE @employee_id IS NOT NULL DO
  IF @salary < 50000.00 THEN
    UPDATE employees
    SET salary = salary * 1.1
    WHERE employee_id = @employee_id;
  END IF;

  FETCH employee_cursor INTO @employee_id, @salary;
END WHILE;

CLOSE employee_cursor;
```

يقوم هذا الكود بالعمليات التالية:

* **يعلن عن مؤشر**: المؤشر هو منطقة عمل مؤقتة تحتوي على البيانات المستردة من استعلام.
* **فتح المؤشر**: هذا يجعل المؤشر جاهزًا للاستخدام.
* **جلب صف**: تقوم جملة `FETCH` باسترداد الصف التالي من المؤشر وتخزينه في المتغيرات `@employee_id` و `@salary`.
* **التكرار عبر البيانات**: يستمر تكرار حلقة `WHILE` طالما يتبقى صفوف في المؤشر.
* **تحديث الرواتب**: إذا كان الراتب الحالي أقل من 50000، يتم زيادته بمقدار 10%.
* **جلب الصف التالي**: يستمر التكرار حتى لا يتبقى المزيد من الصفوف لاستردادها.
* **إغلاق المؤشر**: يؤدي هذا إلى تحرير الموارد المرتبطة بالمؤشر.

يوضح هذا الكود كيفية استخدام المؤشرات الضمنية لتحديث جدول استنادًا إلى شروط معينة. يمكنك تعديل الكود لتطبيق منطق مختلف على تحديثات الراتب.




## السوال الثاني:

**-- إنشاء جدول الموظفين**

```sql
CREATE TABLE employees (
  employee_id INT PRIMARY KEY,
  dept_no INT NOT NULL,
  first_name VARCHAR(50) NOT NULL,
  salary DECIMAL(10, 2) NOT NULL
);
```

**-- إدخال البيانات**

```sql
INSERT INTO employees (employee_id, dept_no, first_name, salary)
VALUES (1, 1, 'John Doe', 50000.00),
       (2, 1, 'Jane Doe', 45000.00),
       (3, 1, 'Mike Smith', 60000.00),
       (4, 2, 'Alice Smith', 42000.00),
       (5, 2, 'Bob Johnson', 35000.00),
       (6, 2, 'Charlie Brown', 48000.00);
```

**-- إنشاء إجراء لتحديث الراتب**

```sql
CREATE PROCEDURE update_salary (IN dept_no INT)
BEGIN
  DECLARE updated_salary DECIMAL(10, 2) DEFAULT 0.00;
  DECLARE total_updated_salary DECIMAL(10, 2) DEFAULT 0.00;

  DECLARE employee_cursor CURSOR FOR
  SELECT employee_id, salary FROM employees WHERE dept_no = dept_no;

  DECLARE CONTINUE HANDLER FOR NOT FOUND SET total_updated_salary = total_updated_salary + updated_salary;

  OPEN employee_cursor;

  FETCH employee_cursor INTO @employee_id, @salary;

  WHILE @employee_id IS NOT NULL DO
    IF dept_no = 1 THEN
      SET updated_salary = @salary + 1000.00;
    ELSEIF dept_no = 2 THEN
      SET updated_salary = @salary + 2000.00;
    END IF;

    UPDATE employees SET salary = updated_salary WHERE employee_id = @employee_id;

    FETCH employee_cursor INTO @employee_id, @salary;
  END WHILE;

  CLOSE employee_cursor;

  SELECT total_updated_salary INTO @total_updated_salary;

  SELECT CONCAT('إجمالي الراتب المحدث للقسم ', dept_no, ': ', @total_updated_salary) AS message;
END;
```

**-- مثال على استخدام الإجراء**

```sql
CALL update_salary(1);
```

**هذا الكود يقوم بما يلي:**

1. **إنشاء جدول "employees"** مع تفاصيل الموظفين ورقم القسم.
2. **إدخال بيانات العينة** في الجدول.
3. **إنشاء إجراء "update_salary"** الذي يأخذ رقم قسم كمدخلات.
4. **داخل الإجراء:**
    * **يعلن عن المؤشرات والمتغيرات** لتخزين معلومات الموظفين والقيم المحسوبة.
    * **يفتح مؤشرًا** لاسترداد الموظفين المنتمين إلى القسم المحدد.
    * **يجوب على كل موظف** في المؤشر.
    * **يحسب الراتب المحدث** بناءً على رقم القسم.
    * **يقوم بتحديث الراتب في قاعدة البيانات** للموظف الحالي.
    * **يحتفظ بإجمالي الراتب المحدث** للقسم.
    * **بعد التكرار، يعرض إجمالي الراتب المحدث** للقسم المحدد.
5. **يوفر مثالاً على استدعاء** الإجراء برقم قسم 1.

هذا مثال أساسي على استخدام إجراء لتحديث وتجميع البيانات في قاعدة بيانات. يمكنك تعديل هذا الكود وتوسيعه لتناسب متطلباتك المحددة.
