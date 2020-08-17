

## python3-flask-部署-db创建错误

python3 -m flask init-db sqlite3.OperationalError: unable to open database file

实际问题是，pip3将应用以root的身份装到了/usr/local/python3.7/lib/python3.7/site-packages
当以普通用户运行的时候，没法创建数据库，导致报错
