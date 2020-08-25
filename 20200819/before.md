## 配置

## 部署
```
work:/opt/tiup$ tiup playground 
```
### sysbench

- 创建database
```
work:/opt/tiup$ mysql --host 127.0.0.1 --port 4000 -u root
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| INFORMATION_SCHEMA |
| METRICS_SCHEMA     |
| PERFORMANCE_SCHEMA |
| mysql              |
| test               |
+--------------------+
5 rows in set (0.01 sec)

mysql> create database tidb_test;
Query OK, 0 rows affected (0.07 sec)

```
- prepare
```
 sysbench oltp_read_only     --mysql-host=127.0.0.1     --mysql-port=4000  --db-driver=mysql --mysql-db=tidb_test --mysql-user=root      --threads=1    --time=150     --report-interval=10   --skip_trx=off --db-ps-mode=disable  prepare
```
- run  

只读查询 10个线程
```
sysbench oltp_read_only     --mysql-host=127.0.0.1     --mysql-port=4000  --db-driver=mysql --mysql-db=tidb_test --mysql-user=root      --threads=10    --time=150     --report-interval=10   --skip_trx=off --db-ps-mode=disable  run
...
[ 150s ] thds: 10 tps: 905.10 qps: 14482.10 (r/w/o: 12671.90/0.00/1810.20) lat (ms,95%): 13.95 err/s: 0.00 reconn/s: 0.00
SQL statistics:
    queries performed:
        read:                            1943214
        write:                           0
        other:                           277602
        total:                           2220816
    transactions:                        138801 (925.27 per sec.)
    queries:                             2220816 (14804.34 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          150.0099s
    total number of events:              138801

Latency (ms):
         min:                                    5.81
         avg:                                   10.80
         max:                                   39.88
         95th percentile:                       13.70
         sum:                              1499742.38

Threads fairness:
    events (avg/stddev):           13880.1000/8.32
    execution time (avg/stddev):   149.9742/0.00
-----------------------------------------------------------------------------
```
点查 100个线程
```
root@work-pc:/opt/tiup# sysbench oltp_point_select     --mysql-host=127.0.0.1     --mysql-port=4000  --db-driver=mysql --mysql-db=tidb_test --mysql-user=root  --time=150     --report-interval=10   --skip_trx=off --db-ps-mode=disable --table-size=10000000 --threads=100 run 
SQL statistics:
    queries performed:
        read:                            8186894
        write:                           0
        other:                           0
        total:                           8186894
    transactions:                        8186894 (54576.34 per sec.)
    queries:                             8186894 (54576.34 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          150.0070s
    total number of events:              8186894

Latency (ms):
         min:                                    0.12
         avg:                                    1.83
         max:                                   30.91
         95th percentile:                        4.49
         sum:                             14996050.43

Threads fairness:
    events (avg/stddev):           81868.9400/206.08
    execution time (avg/stddev):   149.9605/0.01
```

### go-ycsb
```
$ git clone git@github.com:pingcap/go-ycsb.git
$ make 


```
