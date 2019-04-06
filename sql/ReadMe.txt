#1） 查看mysql的运行时长
show global status like 'uptime';

#2） 查看sql超时设置
show global variables like '%timeout';

show global variables like '%lock';

# 修改设置语法(连接超时)
set global connect_timeout=100;
set session connect_timeout=100;

#3） 查看是否被server端kill
show global status like 'com_kill';

#4） 查看查询语句是否过大 max_allowed_packet
show global variables like 'max_allowed_packet';
# 修改设置语法(查询提过大)
set global max_allowed_packet = 1*1024*1024;
set session max_allowed_packet = 1*1024*1024;

#5）最大连接数
show global variables like 'max_connections';

#5) 导入导出设置
# secure_file_priv 为 NULL 时，表示限制mysqld不允许导入或导出。
# secure_file_priv 为 /tmp 时，表示限制mysqld只能在/tmp目录中执行导入导出，其他目录不能执行。
# secure_file_priv 没有值时，表示不限制mysqld在任意目录的导入导出。
show global variables like '%secure_file_priv%';
# 注 secure_file_priv 为只读变量，无法直接使用 set 命令修改
# vim /etc/my.cnf
# -----------------------
# [mysqld]
# #....
# secure_file_priv = ''
# tmpdir = /tmp
# -----------------------

# chmod 777 /tmp

# 文件导出
# 语法
# SELECT ... INTO OUTFILE 'file_name' 输出路径(my.cnf中注册的tmpdir)
#         [CHARACTER SET charset_name] 字符编码
#         [export_options]
              #     [{FIELDS | COLUMNS}
              #         [TERMINATED BY 'string']  字段分割符
              #         [[OPTIONALLY] ENCLOSED BY 'char'] 奇数补全
              #         [ESCAPED BY 'char'] 转义符
              #     ]
              #     [LINES
              #         [STARTING BY 'string']   行开始
              #         [TERMINATED BY 'string'] 行结束
#        FROM table
# 示例
# 导出 csv
SELECT * INTO OUTFILE '/tmp/car.csv'
CHARACTER SET 'utf8'
COLUMNS
  TERMINATED BY ','
LINES
  TERMINATED BY '\n'
FROM car;

# 导出 tsv
SELECT * INTO OUTFILE '/tmp/car.tsv'
CHARACTER SET 'utf8'
COLUMNS
  TERMINATED BY '\t'
LINES
  TERMINATED BY '\n'
FROM car;

# 导出json
SELECT concat('{"id":',id), concat('"name":',name), concat('"price":',price,'}') INTO OUTFILE '/tmp/car.json'
CHARACTER SET 'utf8'
COLUMNS
  TERMINATED BY ','
LINES
  TERMINATED BY '\n'
FROM car;

# upsert sql
INSERT INTO car(name,price) VALUES (%s,%s) ON DUPLICATE KEY UPDATE name=VALUES(name),id=VALUES(id);
