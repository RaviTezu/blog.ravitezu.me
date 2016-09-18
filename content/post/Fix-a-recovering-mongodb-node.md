+++
author = ""
date = "2014-08-04T17:54:35+05:30"
description = ""
tags = []
title = "Fixing a recovering(forever?) Secondary mongodb node"

+++
I have recently come across a Secondary MongoDB Node in one of our clusters in *RECOVERING* state for more than 5 days?
This Article will help you in figuring out whether the node will be back to Normal state or not.

**Steps:**

- SSH into the *PRIMARY* MongoDB node of the cluster and open its Mongo Shell:

``` bash 
ravitezu@mongo_pri:~ $ mongo MongoDB 
shell version: 2.4.4 connecting to: test 
rs1:PRIMARY> use blogdb switched to db blogdb

rs1:PRIMARY> db.printReplicationInfo() 
configured oplog size: 2048MB log length start to end: 202615secs (56.28hrs) 
oplog first event time: Mon Aug 02 2014 13:06:36 GMT+0530 (IST) 
oplog last event time: Wed Aug 04 2014 21:23:31 GMT+0530 (IST) 
now: Wed Aug 04 2014 21:23:33 GMT+0530 (IST)
```

So, OPLOG on PRIMARY node is rotated every 56.28 hrs.

- Now SSH into the *SECONDARY* node which is in *RECEOVERING* state and open its Mongo Shell: 

``` bash 
ravitezu@mongo_sec:~ $ mongo MongoDB 
rs1:RECOVERING> use blogdb 
switched to db blogdb 
rs1:RECOVERING> db.printReplicationInfo() 
configured oplog size: 2048MB log length start to end: 602532secs (167.37hrs)
oplog first event time: Mon Jul 21 2014 20:51:49 GMT+0530 (IST)
oplog last event time: Mon Jul 28 2014 20:14:01 GMT+0530 (IST)
now: Mon Aug 04 2014 21:24:38 GMT+0530 (IST) 
```
Here, the last event on this *SECONDARY* node happened at `Mon Jul 28 2014 20:14:01` and the time difference between the oplog last event(Wed Aug 04 2014 21:23:31 GMT+0530) of *PRIMARY* node is more than 56.28 hours and so this *SECONDARY* cannot catchup with the *PRIMARY* node. 

You can also confirm this by grep-ing for “RS102” in your mongodb logs of SECONDARY node. If the time gap between the last event of *PRIMARY* and *SECONDARY* is less the current oplog time(56.28hrs) of *PRIMARY* node, the SECONDARY node may catchup with your *PRIMARY*.

- What If the time difference between them is more than the current oplog time? - All you need to do is, empty your data directory on the secondary node and restart the `monogd` service to trigger a initial sync. However this will impact the *PRIMARY* node’s Performance and may take more time depending on the amount of data to sync. So, it is recommended to do this sync-ing during the times which your *PRIMARY* node is handling low traffic.

- You can also manually copy the data from any of the other *SECONDARY* nodes and start the mongod process. However you have to make sure the data files are sufficiently recent to allow the *RECOVERING* member to catch up with the oplog.