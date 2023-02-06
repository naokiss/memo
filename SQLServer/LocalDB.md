## LocalDB操作

### SQL Server ExpressLocalDB の新しいインスタンスを作成する

```
SqlLocalDB [ create | c ] <instance-name> <instance-version> [-s]
```

* <instance-name> : インスタンス名
* <instance-version> : SQL Server Express バイナリのバージョン

例
```
SqlLocalDB create "MSSQLLocalDB" 13.0 -s
```

### インスタンスの起動

```
SqlLocalDB [ start | s ] <instance-name> 
```

例
```
SqlLocalDB start "MSSQLLocalDB"
```

[SqlLocalDB ユーティリティ](https://docs.microsoft.com/ja-jp/sql/tools/sqllocaldb-utility?view=sql-server-ver15)