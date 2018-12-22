# mysql-master-slave-docker
mysql主从数据库构建

### 第一步

将两个容器启动起来,没有镜像会自己下

	docker-compose up -d 

### 第二步

本地navacat分别连接到两个mysql

- 容器mysql101(主数据库)

	随便点进一个数据，创建查询 

	```sql
		show master status
	```

	为从数据库创建用户

	```sql
		SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
		
		GRANT REPLICATION SLAVE ON *.* to 'backup'@'%' identified by '123456';
	```

- 容器mysql102(从数据库)
	
	```sql
		SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
		change master to master_host='www.niezhiliang.com',master_user='backup',master_password='123456',master_log_file='mysql-bin.000004',master_log_pos=958;

		master_host为docker的地址不能写127.0.0.1
		master_user是在主库创建的用户
		master_log_pos是主库show master status;查询出的Position
	```

	启动服务

	```sql
		start slave;

		//查看服务状态
		show slave status;

		//Waiting for master to send event 就是成功了 
		//Connecting to master 多半是连接不通
	```