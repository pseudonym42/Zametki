# Concurrency in DB

---


Databases operate in very high concurrent environments. Because of this there are various types of issues (anomalies) which might happen when a lot of data gets read/written from/to a database at the same time, and there are some techniques database developers use to resolve these concurrency problems. These techniques fall into “Isolation” property of database transactions, as know transactions have to be ACID (Atomic, Consistent, Isolated, Durable). So these techniques are used to make transactions Isolated, and satisfy I in ACID.
These techniques also called Isolation levels. There are 4 main types of isolation levels and below you can see what isolation level used by which database and what issues (anomalies) they prevent and which they don’t (X):


| Isolation Level | Dirty read | Non-repeatable read | Phantom read |
| ------------- |:-------------:| :-----:| :-----:|
| READ_UNCOMMITTED| X | X | X |
| READ_COMMITTED (Oracle, Microsoft SQL, Postgres)| Prevents | X | X |
| REPEATABLE_READ (MySQL)| Prevents | Prevents | X |
| SERIALIZABLE | Prevents | Prevents | Prevents |


Let’s look at the anomalies now.

### Dirty read
This happens when a transaction is allowed to read uncommitted changes of some other running transaction. This happens if there is no locking preventing it. For example,
1. Transaction A changes property X of some record from False to True, but does not commit the change yet
2. Transaction B reads (dirty read) property X as True
3. Transaction A rollbacked, thus returning X to False
4. Now Transaction B holds incorrect value for X


### Non-repeatable read
This means that transaction reads some record more than once and the value it gets for property X is not consistent (i.e. non-repeatable) so say first read gives True and second gives False. For example,
1. Transactions A and B both read property X of some record, the value now is False
2. Transaction A changes the property to True and commits
3. Transaction B not committed yet and for some reasons needs to read again and now the value it gets is True, so the reads are not repeatable

### Phantom read
This means that transaction reads some records more than once and the values in the records stay the same but there are some additional records or some records missing. This happens normally when some other transaction inserts or deletes one or more record. For example,
1. Transactions A and B both read some records with the same criteria, say WHERE id>5 and id<10
2. Transaction A deletes one of the records and commits
3. Transaction B reads using the same criteria but number of records differ (one is missing)

To prevent the above anomalies various locks are used: shared locks (also called "read locks"), exclusive locks (also called "write locks"), predicate locks and others. In short: write lock on a record will not let you write or read, read lock will not let you write but will let you read, this is why read locks also called shared locks - there could be multiple read locks on record, until they all released write is not possible.

See this for predicate locks:
https://vladmihalcea.com/how-does-database-pessimistic-locking-interact-with-insert-update-and-delete-sql-statements/


The anomalies listed above are the most popular ones. There are also less known anomalies, e.g.:
Lost Update:
* https://vladmihalcea.com/a-beginners-guide-to-database-locking-and-the-lost-update-phenomena/

Read Skew
* https://vladmihalcea.com/a-beginners-guide-to-read-and-write-skew-phenomena/


Write Skew
* https://vladmihalcea.com/a-beginners-guide-to-read-and-write-skew-phenomena/




