# DNS  Basics

---


When you click on a URL link your computer checks the memory first - if the requested domain name has already been accessed earlier and corresponding IP address is stored in memory, if yes it uses that IP address, if not it goes to DNS (domain name system) via http protocol to get the IP address (sometimes if DNS doesn’t have the requested IP it will go to next DNS for the address etc). Once IP address is received the browser uses that address via TCP/IP.

DNS contains various records and these records can be of different types (CNAME, A, AAAA, MX etc). For example, CNAME - is used to specify that a domain name is an alias for another domain (the 'canonical' domain e.g. 66.249.66.218) so it just redirects to another domain name. A - is used for 'canonical' IPv4 domains, AAAA - for IPv6 domains etc. See example below:


| Name | Type | Value |
| - |:-:| -:|
| bar.example.com  | CNAME | foo.example.com |
| foo.example.com  | A  | 192.0.2.23 |
