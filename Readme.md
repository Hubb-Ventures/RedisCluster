# What is Redis Cluster

Redis is a fast and open-source in-memory data structure store which is used as a database, cache and message broker. Redis supports a wide range of data structures as strings, integers, lists, maps, sets, Hash tables, bitmaps and so on.

# Redis-trib

Redis-trib is the Redis cluster manager which is used to setup a new cluster, once N blank nodes are up and running. It can also adds new nodes to the cluster, either as slaves of an existing master nodes or blank nodes. We can also check if the cluster is inconsistent and fix the clusters.

# Installation of Redis

### Windows Users

1. Download [Redis](https://github.com/ServiceStack/redis-windows/raw/master/downloads/redis-latest.zip)
2. Extract the zip to `C:\redis` folder
3. Now add `C:\redis` to your PATH enivronmental variable. Or `set PATH = %PATH%;C:/redis;`
4. Download and Install [Ruby](https://www.ruby-lang.org/en/downloads/)
5. Download or Save the [redis-trib.rb](http://download.redis.io/redis-stable/src/redis-trib.rb) file in `C:\redis\redis-trib.rb`

### Linux and MacOS Users

1. Open terminal and Download the redis using `wget http://download.redis.io/releases/redis-4.0.1.tar.gz`
2. Extract the tar file using `tar xzf redis-4.0.1.tar.gz`
3. Navigate to the extracted folder using `cd redis-4.0.1`
4. Now compile the binaries using `make`
5. The binaries are compiled and are available in `src` directory
6. Download ruby using `sudo apt-get install ruby-full` (For Linux only) and MacOS ships with Ruby.
7. Download or Save the [redis-trib.rb](http://download.redis.io/redis-stable/src/redis-trib.rb) file in `C:\redis\redis-trib.rb`

# Testing Redis

1. Open Command Prompt in `C:\redis\`
2. Run Command `redis-server.exe redis.windows.conf`
	* Here `redis.windows.conf` is a redis configuration file.
3. Open another Command Prompt and Type command `redis-cli.exe`
4. Now to test, Run command `ping`, you should get a reply `pong` from server

# Playing with Redis

Here are a [list of commands](https://redis.io/commands) which will help you understand, how to work with Redis.

### Sample Play:

1. `SET name redis` - Redis sets the string value of redis to the key name
2. `GET name` - Redis gets the string value of key name
3. `EXISTS name` - Redis returns integer 1, if key name exists. Otherwise integer 0
4. `APPEND name " Commands"` - Redis appends the string of Commands to the existing key name

# Setting up Cluster

Regular nodes can't be part of cluster. So we will create separate redis.config files for our cluster servers. Most importantly, we will setup `cluster-enabled` and `cluster-config-file`. 

1. Navigate to `C:\redis\` folder
2. Find and open `redis.windows.conf` (`redis.conf` for linux and Mac Users) with your favorite text editor. This is a sample redis configuration file. We will generate three configuration files to make it a cluster.
3. Now create three folders, `cluster_4444`, `cluster_4445`, and `cluster_4446`.
4. In each folder create `redis.conf` file with following code and replace PORT with the your port number.


```
daemonize no
bind 127.0.0.1
protected-mode yes
port PORT
tcp-backlog 511
timeout 0
tcp-keepalive 0
loglevel notice
logfile "server_log.txt"
syslog-enabled yes
syslog-ident redis
databases 16

save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir ./cluster_PORT
slave-serve-stale-data yes
slave-read-only yes
appendonly no
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
lua-time-limit 5000

# Cluster
pidfile /var/run/redis-PORT.pid
port PORT
cluster-enabled yes
cluster-config-file redis-cluster-PORT.conf

slowlog-log-slower-than 10000
slowlog-max-len 1024
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-entries 512
list-max-ziplist-value 64
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

```

5. Now open your terminal or command prompt and run the command `gem install redis`
6. Now start all of your cluster nodes in different command prompts with `redis-server.exe cluster_PORT\redis.conf`
7. Once all of your redis servers are up, run the following command to create a cluster: `ruby redis-trib.rb create 127.0.0.1:PORT 127.0.0.1:PORT 127.0.0.1:PORT`. 
  * Here Redis Cluster manager starts and adds blank nodes to the cluster


# Testing the cluster

1. Open another Command Prompts, running the `redis-cli.exe -c -p 4445`
2. Run command, `set name redis`
3. Open another command prompt, running the `redis-cli.exe -c -p 4446`
4. Run command, `get name`, You should be able to get a reply "redis" from server.
5. As you can see, the data is shared between all systems.
