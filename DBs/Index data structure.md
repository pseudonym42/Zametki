# Index data structure

---


Different databases have different types of indexes: clustered, nonclustered, unique, filtered etc. but almost all of them have clustered and nonclustered (or secondary in MySQL) indexes.

Table in the database can have several nonclustered indexes unlike clustered index which can be only one per table, but note that both clustered and nonclustered indexes are organised in the tree-like structure. Clustered index can be considered as a skeleton of the table.
Main purpose of the table index is to provide ordered representation of the table data (due to clustered index) and to provide a quick search on them (due to both clustered/nonclustered index).

Index data structure cannot be sequential like a simple array because an INSERT query would need to move a lot of entries in the data structure to make a room for new entry. Moving large amounts of data is very time-consuming so the INSERT statement would be very slow. The solution to the problem is to establish a logical order that is independent of physical order in memory, so the data is organised like LinkedList and BST data structures. 
Linked lists (doubly ones as each node refers to the preceding and the following node) are used to connect leaf node. This let’s you to get data from a range of leaf nodes much faster that is if not all entries are in one node you can quickly move to the next one as entries within the node are ordered.
Each leaf node stored on the database's smallest storage unit called block. The block contains as many index records as possible, stored in the ordered fashion. Each record is a key-value format data e.g. `{key:value}` In case of nonclustered index the `key` is the value of indexed column and the `value` is the pointer to the entry in the real table (note that in case of clustered index the `value` is not the pointer but the row of data from the table itself). Note that this pointer is normally the value of the primary key, so the primary key value can be used to get the data from the clustered index now quick fast. In case there’s no primary key or any unique key then the pointer is actually a reference to the real row in the table. As stated earlier the leaf nodes linked to each other in the linked list data structure.
With the above you can have your data references keep ordered. But to quickly search, insert or delete  you need to use BST. The way the tree is implemented: it has a root node which has references to different branch nodes and each of these branch nodes has references to a set of leaf nodes. This structure is called balanced tree b’cos it takes the same amount of steps to get to each node. There are a few methods to keep the tree balanced.


More info regarding clustered vs non-clustered indexes:
<iframe width="560" height="315" src="https://www.youtube.com/embed/ITcOiLSfVJQ" frameborder="0" allowfullscreen></iframe>
