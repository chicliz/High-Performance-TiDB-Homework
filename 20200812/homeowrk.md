## 描述
本地下载 TiDB，TiKV，PD 源代码，改写源码并编译部署以下环境：

- 1 TiDB
- 2 PD
- 3 TiKV  

改写后：使得 TiDB 启动事务时，能打印出一个 “hello transaction” 的 日志。

## 环境
ubuntu 20.04LTS  
go 1.13.4  
rust 1.45.2  
cmake 3.16.3

## 修改代码

跟踪代码,分析事务执行流程
```
// Context is an interface for transaction and executive args environment.
type Context interface {
    // NewTxn creates a new transaction for further execution.
	// If old transaction is valid, it is committed first.
	// It's used in BEGIN statement and DDL statements to commit old transaction.
	// NewTxn为之后运行的执行器提前创建一个新事务,如果旧的事务可用,就先提交.
	// 它经常在begin语句或者ddl语句中使用,用来提交旧事务
	NewTxn(context.Context) error

	// Txn returns the current transaction which is created before executing a statement.
	// The returned kv.Transaction is not nil, but it maybe pending or invalid.
	// If the active parameter is true, call this function will wait for the pending txn
	// to become valid.
	Txn(active bool) (kv.Transaction, error)
        ...
}
```
因此修改session/session.go的这两个方法, 添加
```
	logutil.Logger(ctx).Info("hello transaction")
```


## 部署

### 部署pd
git clone git@github.com:pingcap/pd.git  
make all  
./bin/pd-server --data-dir=pd --log-file=pd.log &

### 部署tikv
git clone git@github.com:tikv/tikv.git  
make build  
./bin/tikv-server --pd="127.0.0.1:2379" --data-dir=tikv --log-file=tikv.log &

### 部署tidb-server
git clone git@github.com:pingcap/tidb.git  
make all  
make server   
./bin/tidb-server --store=tikv --path="127.0.0.1:2379" --log-file=tidb.log &

连接
mysql -h 127.0.0.1 -P 4000 -u root -D test

```
mysql> set autocommit=0;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
| 0            |
+--------------+
1 row in set (0.00 sec)

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into tbl_user_info (uid, user_name, age, hobboy) values(26, "lisi", 65, "play");
Query OK, 1 row affected (0.02 sec)

mysql> commit;

```

## 结果

发现有定时三秒执行的一批SQL, 这些SQL也会开启事务,因此日志较为混乱.

```
[2020/08/16 17:45:37.424 +08:00] [INFO] [session.go:1421] ["hello transaction"]
[2020/08/16 17:45:37.424 +08:00] [INFO] [session.go:1496] ["hello transaction"] [conn=1]
[2020/08/16 17:45:37.424 +08:00] [INFO] [session.go:1504] ["NewTxn() inside a transaction auto commit"] [conn=1] [schemaVersion=25] [txnStartTS=418793687855136772]

```
