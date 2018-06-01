# obmongodbdg3rd

## 8. Setting Up a Replica Set
### Introduction to Replication
create 3 replicas in single machine.
```
mongod --replSet mdbDefGuide --dbpaSetting up a Replica Set, Part 2th ~/data/rs1 --port 27017 --smallfiles --oplogSize 200
mongod --replSet mdbDefGuide --dbpath ~/data/rs2 --port 27018 --smallfiles --oplogSize 200
mongod --replSet mdbDefGuide --dbpath ~/data/rs3 --port 27019 --smallfiles --oplogSize 200
```
#### bind_ip
If the mongod we started above were running on a server with a network interface having an ip address of 198.51.100.1 and we wanted to run this mongod as a member of replica set with each member on different servers, we could specify the command-line parameter bind_ip or use bind_ip in the configuration file for this mongod.
```
mongod --bind_ip localhost,192.51.100.1 --replSet mdbDefGuide --dbpath ~/data/rs1 
       --port 27017 --smallfiles --oplogSize 200
```

### Security Considerations
[TODO]

### Setting up a Replica Set, Part 2
launch a fourth host.
```
mongo --port 27017
```
then initiate
```
rsconf = {
    _id: "mdbDefGuide",
    members: [
      {_id: 0, host: "localhost:27017"},
      {_id: 1, host: "localhost:27018"},
      {_id: 2, host: "localhost:27019"} 
    ]
}
rs.initiate(rsconf)
```

You can view the status of replica set using ```rs.status()```

### rs Helper Functions
The following are identical
```
rs.initiate(config);
rs.adminCommand({"replSetInitiate":config})
```

```
db.isMaster()
```


read secondary replica data will return error.
```
> secondaryConn = new Mongo("localhost:21709")
secondaryConn.getDB("test")
secondary.coll.find()
```
To allow queries on the secondary
```
secondaryConn.setSlaveOk()
```
Note that slaveOk is set on the connection,not the database  
You cannot write to secondary replica, the secondary will only perform writes
that writes that it gets through replication, not from clients.



###### automatic failover. 
If the primary goes down, one of the secondaries will automatically be elected primary.

```
db.adminCommand({"shutdown":1})
```
Few key concepts to remember:
- Clients can send a primary all the same operations they could send a standalone server (reads, writes, commands, index builds, etc.).
- Clients cannot write to secondaries.
- Clients, by default, cannot read from secondaries. Use```setSlaveOk()```


### Changing Your Replica Set Configuration
```
rs.add("localhost:4")
rs.remove(..")
```
reconfig
```
var config = rs.config()
config.members[0].host = "localhost:27000"
rs.reconfig(config)
```

With a minority of the set available, all members will be seconndaries.
So at least 3 members required.



### Member Configuration Options 
The replica sets we have set up so far have been fairly uniform in that every member
#### Priority
Priority is how strongly this member wants to become primary.  
priority 0 can never become primary (passive members)
```
rs.add({"host" : "server-4:27017", "priority" : 1.5})
```



#### Hidden
Must set priority to 0 first.
```
config.members[2].hidden=1
config.members[2].priority=0
rs.reconfig(config)
```
#### Creating Election Arbiters
If due to financial costs, we want to keep two copys of data,
Arbiters hold no data and aren't used by clients. they just provide a majority for two-member sets
__You can not reconfigure an arbiter to become a nonarbiter, or vice versa__

#### Building Indexes
note:
- Sometimes a secondary does not need to have the same (or any) indexes that exist on the primary. If you are using a secondary only for backup data or offline batch jobs, you might want to specify "buildIndexes" : false in the member’s configuration. This option prevents the secondary from building any indexes.

- This is a permanent setting: members that have "buildIndexes" : false specified can never be reconfigured to be “normal” index-building members again. If you want to change a non-index-building member to an index-building one, you must remove it from the set, delete all of its data, re-add it to the set, and allow it to resync from scratch.

Again, this option requires the member’s priority to be 0.
