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
