# ReadySet Rallly demo

This guide will instruct you on setting up a self-hosted [Rallly](github.com/lukevella/rallly-selfhosted) site with ReadySet enabled for faster database queries.

## Prerequisites
- ReadySet local build: See instuctions [here](github.com/readysettech/readyset#development)
- Self-hosted [Rallly](github.com/lukevella/rallly-selfhosted) site

## Procedure
1. Connect your database to your Rallly application by editing your `config.env` file. Uncomment `DATABASE_URL` and set its value to the connection string of ReadySet

```shellsession
DATABASE_URL=<your ReadySet connection string>
```
2. Connect to your database psql in this case

```shellsession
$ psql -h <hostname> -p <port> -d <database type> -U <username>
```

3. From here, run the ReadySet custom `SHOW READYSET TABLES` command. This will confirm that your database did connect to ReadySet, and display the status of your database tables. Do no proceed until until all table status' are `Snapshotted`

!!! note
  This process can take some time depending on how many tables are in your database

```sql
dbname=>SHOW READYSET TABLES;
```
``` {.text .nocopy}
   table    |    status
------------+-------------
table1      |  Snapshotted
table2      |  Snapshotted
(2 rows)
```

4. While still in your database, run any queries you would normally want to run for this data

```sql
dbname=>SELECT * FROM table1;
```

5. Since these queries are not cached, they were proxied to your upstream database. You can pull a list of any proxied queries by running the following ReadySet command

```sql
dbname=>SHOW PROXIED QUERIES;
```
This will output each of your queries, their query id, and if they are ReadySet supported. 

6. Any of your proxied queries you wish to have avaible quickly on demand should be cached using the `CREATE CACHE FROM` command

```sql
dbname=>CREATE CACHE FROM <query1>;
```

!!! Note
  In the above example <query1> can be the query itself, or the query id in the proxied queries list shown in step 5

7. Verify that your caches were created by running the `SHOW CACHES` command
```sql
dbname=>SHOW CACHES;
```
