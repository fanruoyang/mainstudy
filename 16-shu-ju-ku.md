#### 1 可视化工具   Navicat Premium 综合
#### 2 DDL
```
-- CREATE TABLE t_student_details(name text,age int,addressname text) 创建表
-- DROP TABLE IF EXISTS _t_student_old_20181114  删除表
-- ALTER TABLE t_student RENAME TO t_tablenew   修改表名
-- ALTER TABLE aaa ADD COLUMN sex text 增加参数列
```
#### 3 DML

```
INSERT INTO 表名(字段一，字段二....) VALUES (字段一，字段二的值...) //插入数据
UPDATE 表名 SET 字段一 = 字段一的值 ，字段二 = 字段二的值... //更新数据
DELETE FROM 表名      //删除表
//条件语句的常见格式
- 如果想要更新或者删除默写的固定记录，就必须在DML后面加上一些条件
WHERE 字段 = 某个值 ；//不想用俩个 = 
WHERE 字段 IS 某个值 ；//is 相当于 = 
WHERE 字段 ！=某个表
WHERE 字段 IS NOT 某个值 
 > 
 字段一 = a ADD 字段二 = b //&&
  字段一 = a OR 字段二 = b //||
```