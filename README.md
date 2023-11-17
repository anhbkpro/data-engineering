# Intall Spark and Jupiter Notebook


# Setup Postgresql container
- pull postgresql image: `docker pull postgres:alpine`
- check image: `docker images`
- run container: `docker run --name postgres-spark-in-action -e POSTGRES_PASSWORD=password -d -p 5435:5432 postgres:alpine`
- check if container start successfully: `docker ps`
- If container not start success, list all containers (including the exited ones): `docker ps -a` -> get container_id -> `docker rm container_id` to remove the failed one
- connect to postgresql container: `docker exec -it postgres-spark-in-action bash`
- connect to postgresql: `psql -U postgres`
- create database: `CREATE DATABASE mydb;`
- create user: `CREATE USER myuser WITH ENCRYPTED PASSWORD 'password';`
- grant user to access database: `GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;`
- connect to database: `\c mydb`
- create table: `CREATE TABLE ch02 (id int);`

# Troubleshooting
### If you get error: `pyspark dataframe error due to java.lang.ClassNotFoundException: org.postgresql.Driver`
- Solution: add `.config("spark.jars.packages", "org.postgresql:postgresql:42.6.0")` as below:
```py
spark = SparkSession\
.builder\
.config("spark.jars.packages", "org.postgresql:postgresql:42.6.0") \  # this line very important
.appName("CSV to DB")\
.getOrCreate()
```
- Then `Restart the kernel` -> it will automatically download the Driver: `postgresql:42.6.0`
- Run the code again, it should work now
### When df write data into the table, it must be the owner/ or has permission owner, to check who is the owner of a table, exec into the container, run `\dt` command:
```sql
postgres=# \dt
         List of relations
 Schema | Name  | Type  |  Owner
 public | t1    | table | postgres
```
From above result, `postgres` is the owner of table `t1`
- To update other user has permission owner, exit current exec, connect to the database with owner user, in here is `postgres` by running command: `psql -h localhost -p 5435 -U postgres`, then run command:
```SQL
grant postgres to myuser; --- this will grant myuser has owner permission as postgres.
```
