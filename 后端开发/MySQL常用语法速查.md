---
tags:
  - mysql
  - 数据库
  - 语法速查
  - 后端
created: 2026-05-14
updated: 2026-05-14
author: kuma
---
# MySQL 常用语法速查

> 面向后端开发的 MySQL 常用操作速查手册。

---

## 一、库（Database）操作

```sql
-- 创建数据库（指定字符集）
CREATE DATABASE db_name
  DEFAULT CHARACTER SET utf8mb4
  DEFAULT COLLATE utf8mb4_unicode_ci;

-- 查看所有数据库
SHOW DATABASES;

-- 切换/使用数据库
USE db_name;

-- 删除数据库
DROP DATABASE IF EXISTS db_name;
```

---

## 二、表（Table）操作

### 2.1 创建表

```sql
CREATE TABLE users (
    id          BIGINT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键',
    username    VARCHAR(50)     NOT NULL COMMENT '用户名',
    email       VARCHAR(100)    NOT NULL COMMENT '邮箱',
    password    VARCHAR(255)    NOT NULL COMMENT '密码',
    status      TINYINT         NOT NULL DEFAULT 1 COMMENT '状态: 0-禁用 1-启用',
    created_at  DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    updated_at  DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    PRIMARY KEY (id),
    UNIQUE KEY uk_username (username),
    UNIQUE KEY uk_email (email),
    KEY idx_status (status),
    KEY idx_created_at (created_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='用户表';
```

### 2.2 修改表结构

```sql
-- 添加字段
ALTER TABLE users ADD COLUMN phone VARCHAR(20) DEFAULT NULL COMMENT '手机号' AFTER email;

-- 修改字段
ALTER TABLE users MODIFY COLUMN username VARCHAR(100) NOT NULL COMMENT '用户名';
ALTER TABLE users CHANGE COLUMN username user_name VARCHAR(100) NOT NULL COMMENT '用户名';

-- 删除字段
ALTER TABLE users DROP COLUMN phone;

-- 添加索引
ALTER TABLE users ADD INDEX idx_email_status (email, status);
ALTER TABLE users ADD UNIQUE KEY uk_phone (phone);

-- 删除索引
ALTER TABLE users DROP INDEX idx_email_status;

-- 重命名表
RENAME TABLE users TO user_accounts;
```

### 2.3 查看表信息

```sql
-- 查看表结构
DESC users;
SHOW COLUMNS FROM users;
SHOW CREATE TABLE users;

-- 查看索引
SHOW INDEX FROM users;

-- 查看所有表
SHOW TABLES;
```

---

## 三、增删改（CRUD）

### 3.1 插入（INSERT）

```sql
-- 单行插入
INSERT INTO users (username, email, password) VALUES ('zhangsan', 'zs@example.com', '123456');

-- 批量插入
INSERT INTO users (username, email, password) VALUES
  ('lisi', 'ls@example.com', '123456'),
  ('wangwu', 'ww@example.com', '123456');

-- 插入或更新（主键/唯一键冲突时更新）
INSERT INTO users (username, email, password) VALUES ('zhangsan', 'zs_new@example.com', '654321')
  ON DUPLICATE KEY UPDATE email = VALUES(email), password = VALUES(password);

-- 从查询结果插入
INSERT INTO user_backup (id, username, email) SELECT id, username, email FROM users WHERE status = 1;
```

### 3.2 删除（DELETE）

```sql
-- 条件删除
DELETE FROM users WHERE id = 1;

-- 批量删除
DELETE FROM users WHERE status = 0 AND created_at < '2024-01-01';

-- 清空表（DDL，不可回滚，重置自增）
TRUNCATE TABLE user_logs;
```

### 3.3 更新（UPDATE）

```sql
-- 条件更新
UPDATE users SET status = 0 WHERE id = 1;

-- 多字段更新
UPDATE users SET email = 'new@example.com', updated_at = NOW() WHERE id = 1;

-- 关联更新
UPDATE orders o
  INNER JOIN users u ON o.user_id = u.id
  SET o.user_name = u.username
  WHERE o.user_name IS NULL;
```

---

## 四、查询（SELECT）

### 4.1 基础查询

```sql
-- 全字段
SELECT * FROM users;

-- 指定字段
SELECT id, username, email FROM users;

-- 去重
SELECT DISTINCT status FROM users;

-- 别名
SELECT u.id AS user_id, u.username AS name FROM users AS u;
```

### 4.2 条件查询（WHERE）

```sql
-- 比较
SELECT * FROM users WHERE id = 1;
SELECT * FROM users WHERE id > 10;
SELECT * FROM users WHERE created_at >= '2025-01-01';

-- 逻辑运算
SELECT * FROM users WHERE status = 1 AND email LIKE '%@example.com';
SELECT * FROM users WHERE status = 0 OR id IN (1, 2, 3);

-- 范围
SELECT * FROM users WHERE id BETWEEN 10 AND 20;
SELECT * FROM users WHERE created_at BETWEEN '2025-01-01' AND '2025-12-31';

-- 集合
SELECT * FROM users WHERE id IN (1, 2, 3, 5, 8);
SELECT * FROM users WHERE id NOT IN (1, 2, 3);

-- 空值
SELECT * FROM users WHERE phone IS NULL;
SELECT * FROM users WHERE phone IS NOT NULL;
```

### 4.3 模糊查询（LIKE）

```sql
-- 以 zh 开头
SELECT * FROM users WHERE username LIKE 'zh%';

-- 包含 san
SELECT * FROM users WHERE username LIKE '%san%';

-- 第二个字符为 a
SELECT * FROM users WHERE username LIKE '_a%';

-- 正则表达式
SELECT * FROM users WHERE email REGEXP '^[a-z]+@example\\.com$';
```

### 4.4 排序与分页

```sql
-- 排序
SELECT * FROM users ORDER BY created_at DESC;
SELECT * FROM users ORDER BY status ASC, created_at DESC;

-- 分页（LIMIT offset, count）
SELECT * FROM users LIMIT 10;           -- 前10条
SELECT * FROM users LIMIT 10, 20;       -- 跳过10条，取20条（第11-30条）
SELECT * FROM users ORDER BY id DESC LIMIT 20 OFFSET 0;  -- 第1页，每页20条
```

### 4.5 聚合查询

```sql
-- 聚合函数
SELECT COUNT(*) FROM users WHERE status = 1;
SELECT AVG(age) FROM users;
SELECT SUM(amount) FROM orders WHERE order_date = CURDATE();
SELECT MAX(score), MIN(score) FROM exam_results;

-- 分组
SELECT status, COUNT(*) AS cnt FROM users GROUP BY status;

-- 分组后过滤
SELECT status, COUNT(*) AS cnt FROM users GROUP BY status HAVING cnt > 5;

-- 常用组合
SELECT
  DATE(created_at) AS date,
  COUNT(*)         AS total,
  SUM(amount)      AS revenue
FROM orders
WHERE created_at >= '2025-01-01'
GROUP BY DATE(created_at)
HAVING total >= 10
ORDER BY date DESC;
```

---

## 五、多表查询

### 5.1 连接查询（JOIN）

```sql
-- 内连接：只返回匹配的行
SELECT u.username, o.order_no, o.amount
FROM users u
  INNER JOIN orders o ON u.id = o.user_id;

-- 左连接：返回左表所有行
SELECT u.username, o.order_no
FROM users u
  LEFT JOIN orders o ON u.id = o.user_id;

-- 右连接：返回右表所有行
SELECT u.username, o.order_no
FROM users u
  RIGHT JOIN orders o ON u.id = o.user_id;

-- 多表连接
SELECT u.username, o.order_no, p.name AS product_name
FROM users u
  INNER JOIN orders o    ON u.id = o.user_id
  INNER JOIN products p  ON o.product_id = p.id;
```

### 5.2 子查询

```sql
-- WHERE 子查询
SELECT * FROM users WHERE id IN (SELECT DISTINCT user_id FROM orders WHERE amount > 100);

-- FROM 子查询（派生表）
SELECT t.status, t.cnt FROM (SELECT status, COUNT(*) AS cnt FROM users GROUP BY status) AS t WHERE t.cnt > 5;

-- SELECT 子查询（标量子查询）
SELECT username, (SELECT COUNT(*) FROM orders WHERE user_id = users.id) AS order_count FROM users;

-- EXISTS / NOT EXISTS
SELECT * FROM users u WHERE EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);
SELECT * FROM users u WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);
```

### 5.3 合并查询（UNION）

```sql
-- UNION（去重合并）
SELECT username FROM users WHERE status = 1
UNION
SELECT username FROM deleted_users WHERE status = 1;

-- UNION ALL（不去重，性能更好）
SELECT username FROM users
UNION ALL
SELECT username FROM deleted_users;
```

---

## 六、常用函数

### 6.1 字符串函数

| 函数 | 示例 | 结果 |
|------|------|------|
| `CONCAT` | `CONCAT('Hello', ' ', 'World')` | `Hello World` |
| `CONCAT_WS` | `CONCAT_WS('-', '2025', '01', '01')` | `2025-01-01` |
| `SUBSTRING` | `SUBSTRING('MySQL', 1, 2)` | `My` |
| `LENGTH` | `LENGTH('你好')` | `6` (字节) |
| `CHAR_LENGTH` | `CHAR_LENGTH('你好')` | `2` (字符) |
| `UPPER / LOWER` | `UPPER('mysql')` | `MYSQL` |
| `TRIM` | `TRIM('  a b  ')` | `a b` |
| `REPLACE` | `REPLACE('abc', 'b', 'x')` | `axc` |
| `LEFT` | `LEFT('MySQL', 3)` | `MyS` |
| `GROUP_CONCAT` | `GROUP_CONCAT(username SEPARATOR ',')` | `zhangsan,lisi,wangwu` |

### 6.2 数值函数

| 函数 | 说明 |
|------|------|
| `ROUND(n, d)` | 四舍五入保留 d 位小数 |
| `CEIL(n)` / `FLOOR(n)` | 向上/向下取整 |
| `ABS(n)` | 绝对值 |
| `MOD(n, m)` | 取模 |
| `RAND()` | 随机数 (0-1) |

### 6.3 日期函数

```sql
-- 当前时间
SELECT NOW();           -- 2025-05-14 15:30:00
SELECT CURDATE();       -- 2025-05-14
SELECT CURTIME();       -- 15:30:00

-- 日期格式化
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s');   -- 2025-05-14 15:30:00
SELECT DATE_FORMAT(NOW(), '%Y年%m月%d日');          -- 2025年05月14日

-- 日期计算
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);    -- 7天后
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);  -- 1个月前
SELECT DATEDIFF('2025-12-31', '2025-01-01'); -- 364（天数差）

-- 提取日期部分
SELECT YEAR(NOW()), MONTH(NOW()), DAY(NOW());
SELECT WEEKDAY(NOW());   -- 0=周一, 6=周日
```

### 6.4 条件函数

```sql
-- IF
SELECT username, IF(status = 1, '启用', '禁用') AS status_name FROM users;

-- IFNULL / COALESCE
SELECT username, IFNULL(phone, '未填写') AS phone FROM users;       -- 替换 NULL
SELECT username, COALESCE(nickname, username, '匿名') AS name FROM users; -- 返回第一个非 NULL

-- CASE WHEN
SELECT username,
  CASE
    WHEN score >= 90 THEN '优秀'
    WHEN score >= 60 THEN '及格'
    ELSE '不及格'
  END AS grade
FROM exam_results;
```

---

## 七、事务与锁

### 7.1 事务控制

```sql
-- 开启事务
START TRANSACTION;
-- 或
BEGIN;

-- 执行操作
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;

-- 提交
COMMIT;

-- 回滚
ROLLBACK;

-- 设置回滚点
SAVEPOINT sp1;
-- ... 操作 ...
ROLLBACK TO sp1;
```

### 7.2 行锁

```sql
-- 排他锁（SELECT ... FOR UPDATE）
START TRANSACTION;
SELECT * FROM accounts WHERE user_id = 1 FOR UPDATE;  -- 锁定该行，阻止其他事务修改
UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
COMMIT;

-- 共享锁（SELECT ... LOCK IN SHARE MODE）
START TRANSACTION;
SELECT * FROM accounts WHERE user_id = 1 LOCK IN SHARE MODE; -- 允许读，阻止写
COMMIT;
```

---

## 八、视图、存储过程、触发器

### 8.1 视图（VIEW）

```sql
-- 创建视图
CREATE VIEW v_user_orders AS
SELECT u.id, u.username, COUNT(o.id) AS order_count, COALESCE(SUM(o.amount), 0) AS total_amount
FROM users u
  LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.username;

-- 使用视图
SELECT * FROM v_user_orders WHERE order_count > 5;

-- 删除视图
DROP VIEW IF EXISTS v_user_orders;
```

### 8.2 存储过程（PROCEDURE）

```sql
DELIMITER //

CREATE PROCEDURE get_user_orders(IN uid BIGINT, IN limit_count INT)
BEGIN
  SELECT o.order_no, o.amount, o.created_at
  FROM orders o
  WHERE o.user_id = uid
  ORDER BY o.created_at DESC
  LIMIT limit_count;
END //

DELIMITER ;

-- 调用
CALL get_user_orders(1, 10);
```

### 8.3 触发器（TRIGGER）

```sql
-- 记录更新日志
CREATE TRIGGER trg_users_update
  AFTER UPDATE ON users
  FOR EACH ROW
BEGIN
  INSERT INTO users_log (user_id, old_email, new_email, changed_at)
  VALUES (OLD.id, OLD.email, NEW.email, NOW());
END;
```

---

## 九、性能优化

### 9.1 索引建议

```sql
-- 查看执行计划（日常排障第一步）
EXPLAIN SELECT * FROM users WHERE email = 'test@example.com';

-- 常用索引规则：
--   1. WHERE 条件列加索引
--   2. JOIN 关联列加索引
--   3. ORDER BY / GROUP BY 列加索引
--   4. 联合索引遵循"最左前缀"原则
--   5. 高选择性的列优先（区分度高的列）

-- 慢查询定位
SHOW VARIABLES LIKE 'slow_query%';
SHOW VARIABLES LIKE 'long_query_time';
```

### 9.2 批量操作

```sql
-- 批量插入（远快于逐条 INSERT）
INSERT INTO users (username, email, password) VALUES
  ('user1', 'u1@test.com'),
  ('user2', 'u2@test.com'),
  ('user3', 'u3@test.com');

-- 批量更新
UPDATE users SET status = 1 WHERE id IN (1, 2, 3, 4, 5);

-- 分页优化（大偏移量时用子查询定位）
SELECT * FROM users WHERE id > 10000 ORDER BY id LIMIT 20;  -- 优于 LIMIT 10000, 20
```

### 9.3 常见坑

```sql
-- 避免 SELECT *
-- 明确需要的字段

-- WHERE 中避免对列做函数/计算（无法使用索引）
-- 错误: WHERE DATE(created_at) = '2025-01-01'
-- 正确: WHERE created_at >= '2025-01-01' AND created_at < '2025-01-02'

-- LIKE 前置 % 无法使用索引
-- 错误: WHERE username LIKE '%zhang%'
-- 正确: WHERE username LIKE 'zhang%'（前缀模糊仍可使用索引）

-- 隐式类型转换会导致索引失效
-- 错误: WHERE phone = 13800138000  （phone 是 VARCHAR）
-- 正确: WHERE phone = '13800138000'
```

---

## 十、用户与权限

```sql
-- 创建用户
CREATE USER 'app_user'@'%' IDENTIFIED BY 'strong_password';

-- 授权
GRANT SELECT, INSERT, UPDATE, DELETE ON db_name.* TO 'app_user'@'%';

-- 只读用户
GRANT SELECT ON db_name.* TO 'readonly_user'@'%' IDENTIFIED BY 'password';

-- 刷新权限
FLUSH PRIVILEGES;

-- 查看权限
SHOW GRANTS FOR 'app_user'@'%';

-- 撤销权限
REVOKE DELETE ON db_name.* FROM 'app_user'@'%';
```

---

## 十一、数据导入导出

```sql
-- 导出（命令行）
-- mysqldump -u root -p db_name > backup.sql
-- mysqldump -u root -p db_name users > users.sql       # 仅导出指定表
-- mysqldump -u root -p --no-data db_name > schema.sql  # 仅结构

-- 导入（命令行）
-- mysql -u root -p db_name < backup.sql
-- mysql -u root -p db_name < backup.sql.gz  # 压缩导入
```

---

> **常用字符集：** 推荐统一使用 `utf8mb4`（支持 emoji 等四字节字符，`utf8` 在 MySQL 中最多只支持三字节）
