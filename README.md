# obmongodbdg3rd

## 8. Setting Up a Replica Set
### Introduction to Replication
create 3 replicas in single machine.
```
mongod --replSet mdbDefGuide --dbpath ~/data/rs1 --port 27017 --smallfiles --oplogSize 200
mongod --replSet mdbDefGuide --dbpath ~/data/rs2 --port 27018 --smallfiles --oplogSize 200
mongod --replSet mdbDefGuide --dbpath ~/data/rs3 --port 27019 --smallfiles --oplogSize 200
```
