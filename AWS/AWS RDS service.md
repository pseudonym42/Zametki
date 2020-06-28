# AWS RDS service

---


AWS provides managed DB service (RDS), which are OLTP (online transaction processing) i.e. transaction oriented databases. RDS service has some advantages compared to hosting DB on your own on some EC2. Apart from automatic backup and minor updates. It has the following pros:
1. You can easily create READ replicas. They are automatically updated asynchronously. When data is written to MASTER instance then corresponding logs are transferred to READ replica and it gets updated. If your MATER instance is down you can easily redirect requests to READ replica.

2. RDS can be MultiZoned (MZ) i.e. you can enable secondary DB instance in other zones of the same region. This secondary DB is going to be an exact copy of the MASTER. The instances in other zones unlike READ replicas are updated synchronously.

3. When you have MZ enabled this helps to avoid downtime when upgrading a DB or taking a snapshot, because snapshots always in this case are taken from secondary DBs and upgrades and patches are made to one instance and then the other and the one which is not being patched is online
