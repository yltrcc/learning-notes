### 命令行查看和修改最大连接数

```SQL
show variables like 'max_connections';(查可以看当前的最大连接数)
set global max_connections=1000;(设置最大连接数为1000，可以再次查看是否设置成功)

缺点：
1. 设置的最大连接数只在当前mysql服务进程有效

```

### 修改配置文件来修改mysql最大连接数

```text
修改MySQL配置文件my.ini 或 my.cnf的参数：配置文件一般存在于mysql安装目录下。
  max_connections=1000
  
然后重启MySQL即可。

```

