**开启mysql服务**

```
net start mysql
```

**停止mysql服务**

```
net stop mysql
```

**修改密码**

```
mysqladmin -u root -p password
# 提示输入密码，直接按回车
```

**进入MySQL命令行**

```
mysql -uroot -p
```

### node连接mysql

 在目录中下载mysql包

```shell
npm i mysql
```

```js
// 1. 引入
const mysql = require('mysql');
// 2. 填写信息
const connection = mysql.createConnection({
  host: "localhost", //主机名
  user: 'root',	// 用户名
  password: 'admin13', // 密码
  database: 'demo1', // 库名称
  // port: '3306' // 端口号
  
})
// 3. 连接
connection.connect();
// 4. 查询user表中所有的数据
connection.query('select * from user');
// 5. 关闭
connection.end();
```

#### 增删改查

**增**

```js
db.query('INSERT INTO sensor_data value (?,?,?,?,?,?)',
[0, 'a1wcvoNRurl', time, timestamp, temperature, humidity],
  (err, data) => {
  	if (err) throw err;
  	else console.log("[" + time + "]" + ": 添加一条记录到数据库");
});
```

**删**

```js
const d = 'delete from user where uid = ?';
conn.query(d,3,(err,result)=>{
	if(err) {console.log(err.message)}
	else {
			console.log(result);
			console.log('数据删除成功');
		}
});
```

**改**

```js
const update = 'update user set username = ?,pwd = ?,address = ? where uid = ?';
const params = ['Toby','admin','江苏省无锡市',1];
conn.query(update,params,(err,result)=>{
	if(err) {console.log(err.message)}
	else {
			console.log(result);
			console.log('数据修改成功');
		}
});
```

