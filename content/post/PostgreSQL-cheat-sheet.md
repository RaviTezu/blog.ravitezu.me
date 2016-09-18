+++
author = ""
date = "2015-02-15T18:27:05+05:30"
description = ""
tags = []
title = "PostgreSQL cheat sheet"

+++

This article mainly focuses on the Ownership/Permissions of database objects. I had to collect these for migrating a standalone PostgreSQL database to [Amazon RDS](http://aws.amazon.com/rds/)(Relational Database Service) PostgreSQL and I will be updating this article as long as I find something which belongs here. <br/>
All these commands are supposed to be executed in the `psql` command line. Adding “+” sign to most of the command gives you more information of those objects.

- How to get help in psql command line:
```
  \? 
```

- How to list the databases:
``` bash
  \l or \l+
```

- How to connect to a different database with in the shell:
``` bash
  \c <database-name> or \connect <database-name>
```
- How to list all the schemas in a database:
``` bash
  \dn or \dn+
```
- How to list all tables in a schema:
``` bash
  \dt <schema-name>.* or \dt+ <schema-name>.*
```
- How to list all tables in a database:
``` bash
  \dt *.* or \dt+ *.*
```
- How to list all views in a schema:
``` bash
  \dv or \dv+
```
- How to list all views in a database:
``` bash
  \dv *.* or \dv+ *.*
```
- How to know the permissions on tables and views:
``` bash
  \dp+ <schema-name>.*;
```
- How to list all the functions:
``` bash
  \df or \df+
```
- How to list all the sequences in all schemas:
``` bash
  \ds+ *.*;
```
- How to create a role in postgres:
``` bash
  CREATE ROLE bob WITH PASSWORD 'bobpassword';
```
- How to create a user in posrgres:
``` bash
  CREATE USER alice WITH PASSWORD 'alicepassword';
```
*Note:* The keywords *ROLE* and *USER* are synonyms in PostgreSQL, however the difference between them is, when you use *USER*, the role will be created with *LOGIN* privileges.

- How to create a group in postgres:
``` bash
  CREATE GROUP developers; 
```
- How to create a schema in a database:
``` bash
  CREATE SCHEMA minions AUTHORIZATION gru;
```
- How to grant SELECT privileges on all tables to a group:<br/>
*Note: Before you grant SELECT privileges to a role/group you need to give USAGE privileges to that role on the schemas those tables are in.* 
``` bash
  GRANT USAGE ON SCHEMA application TO developers; 
  GRANT SELECT ON ALL TABLES IN SCHEMA application TO developers;
```
- How to add roles to a group:
``` bash
  GRANT developers TO bob,alice; 
```
- How to grant ALL permissions to a role on tables in a schema:
``` bash
  GRANT ALL ON SCHEMA application TO developers;
  GRANT ALL ON ALL TABLES IN SCHEMA application TO app_user; 
```
- How to know the current database:
``` bash
  SELECT current_database();
```
- How to know the current user:
``` bash
  SELECT current_user;
```
- How to list all the functions in a schema with arguments it take:
``` bash
  SELECT proname || '(' || pg_catalog.pg_get_function_identity_arguments(pr.oid) || ')', n.nspname FROM pg_proc pr join pg_namespace n ON pr.pronamespace = n.oid WHERE n.nspname IN ('<schema1>','<schema2>');
```
- How to change the ownership on a function:
``` bash
  ALTER FUNCTION <schema-name, if-needed>.<function name with arguments, from the output of above query> OWNER TO 'new-owner';
```
- How to grant EXECUTE permission on a function:
``` bash
  GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA application TO app_user;
``` 
- How to list all the functions in a schema along with the permissions on them:
``` bash 
  SELECT oid::regprocedure, proacl FROM pg_proc WHERE proacl IS NOT NULL;
```
- How to GRANT ALL permissions to sequences:
``` bash
  GRANT ALL ON ALL SEQUENCES IN SCHEMA appplication to admin;
``` 
- How to change the search path for user:
``` bash 
  ALTER ROLE app_user SET search_path TO application;
```
- How to show the default permissions on schema:
``` bash
  SELECT nspname, defaclobjtype, defaclacl FROM pg_default_acl a JOIN pg_namespace b ON a.defaclnamespace=b.oid;
```
- How to set default permissions on a schema(on Tables):
``` bash
  ALTER DEFAULT PRIVILEGES IN SCHEMA application GRANT SELECT ON TABLES TO developers;
```
- How to revoke default permissions on a schema(on Tables):
``` bash
  ALTER DEFAULT PRIVILEGES IN SCHEMA application REVOKE ALL ON TABLES FROM developers;
```
- How to get the start time of the database from PSQL:
``` bash
  SELECT pg_postmaster_start_time();
```
- How to get the uptime, execute the query below:
``` bash
  SELECT now() - pg_postmaster_start_time();
```
- How to get long running queries:
``` bash
  SELECT blockeda.pid AS blocked_pid, blockinga.pid AS blocking_pid, blockinga.query as blocking_query, blockeda.query as blocked_query FROM pg_catalog.pg_locks blockedl JOIN pg_stat_activity blockeda ON blockedl.pid = blockeda.pid JOIN pg_catalog.pg_locks blockingl ON(blockingl.transactionid=blockedl.transactionid AND blockedl.pid != blockingl.pid) JOIN pg_stat_activity blockinga ON blockingl.pid = blockinga.pid WHERE NOT blockedl.granted;
```