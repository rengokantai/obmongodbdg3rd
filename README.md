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
```


automatic failover. If the primary goes down, one of the secondaries will automatically be elected primary.

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
#### Member Configuraion Options

