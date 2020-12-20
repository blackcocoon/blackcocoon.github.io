---
sort: 1
---

# mysql port


## [MySQL Port Diagram](https://dev.mysql.com/doc/mysql-port-reference/en/mysql-port-diagram.html)

![MySQL Port Diagram](/assets\images\db\mysql-ports-diagram.png)


## [MySQL Port Reference Tables](https://dev.mysql.com/doc/mysql-port-reference/en/mysql-ports-reference-tables.html#mysql-client-server-ports)

The following tables describe ports used by MySQL products and features. Port information is applicable to MySQL 5.7 and MySQL 8.0.


### Client - Server Connection Ports

Port 3306 is the default port for the classic MySQL protocol ([`port`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_port)), which is used by the **mysql** client, MySQL Connectors, and utilities such as **mysqldump** and **mysqlpump**. The port for X Protocol ([`mysqlx_port`](https://dev.mysql.com/doc/refman/8.0/en/x-plugin-options-system-variables.html#sysvar_mysqlx_port)), supported by clients such as MySQL Shell, MySQL Connectors and MySQL Router, is calculated by multiplying the port used for classic MySQL protocol by 10. For example if the classic MySQL protocol port is the default value of 3306 then the X Protocol port is 33060.


**Table 3.1 Client - Server Connection Ports**

| Default Port/Protocol | Description                                                | SSL or other Encryption | Required                                  | Direction                                 |
| :-------------------- | :--------------------------------------------------------- | :---------------------- | :---------------------------------------- | :---------------------------------------- |
| 3306/TCP              | MySQL clients to the MySQL server (classic MySQL protocol) | Yes                     | Yes, unless you are only using X Protocol | From the MySQL client to the MySQL server |
| 33060/TCP             | MySQL clients to the MySQL server (X Protocol)             | Yes                     | Yes, unless you are only using port 3306  | From the MySQL client to the MySQL server |



To verify the value of these ports on MySQL server, issue:

```sql
mysql> SHOW VARIABLES LIKE 'port';
mysql> SHOW VARIABLES LIKE 'mysqlx_port';
```

### MySQL Administrative Connection Port

As of MySQL 8.0.14, the server permits a TCP/IP port to be configured specifically for administrative connections. This provides an alternative to the single administrative connection that is permitted on the network interfaces used for ordinary connections. For more information, see [Administrative Connection Management](https://dev.mysql.com/doc/refman/8.0/en/administrative-connection-interface.html).


**Table 3.2 MySQL Administrative Connection Port**

| Default Port/Protocol | Description                                                                                  | SSL or other Encryption | Required | Direction                                 |
| :-------------------- | :------------------------------------------------------------------------------------------- | :---------------------- | :------- | :---------------------------------------- |
| 33062/TCP (default)   | A port configured specifically for MySQL administrative connections (classic MySQL protocol) | Yes                     | No       | From the MySQL client to the MySQL server |


To verify the value of this port on MySQL server, issue:

```sql
mysql> SHOW VARIABLES LIKE 'admin_port';
```

### MySQL Shell Ports

MySQL Shell supports both X Protocol and classic MySQL protocol. For more information, see [MySQL Shell 8.0 (part of MySQL 8.0)](https://dev.mysql.com/doc/mysql-shell/8.0/en/).



**Table 3.3 MySQL Shell Ports**

| Default Port/Protocol | Description                                                                        | SSL or other Encryption | Required                                  | Direction                                          |
| :-------------------- | :--------------------------------------------------------------------------------- | :---------------------- | :---------------------------------------- | :------------------------------------------------- |
| 3306/TCP              | MySQL client to the MySQL server (classic MySQL protocol)                          | Yes                     | Yes, unless you are only using X Protocol | From MySQL Shell to the MySQL server               |
| 33060/TCP             | MySQL client to the MySQL server (X Protocol)                                      | Yes                     | Yes, unless you are only using port 3306  | From MySQL Shell to the MySQL server               |
| 33061/TCP             | The port used by MySQL Shell to check a server during InnoDB Cluster configuration | Yes                     | Yes, if running InnoDB Cluster            | From MySQL Shell to instances in an InnoDB Cluster |



### MySQL Workbench Ports

**Table 3.4 MySQL Workbench Ports**

| Default Port/Protocol | Description                                               | SSL or other Encryption | Required                          | Direction                                |
| :-------------------- | :-------------------------------------------------------- | :---------------------- | :-------------------------------- | :--------------------------------------- |
| 3306/TCP              | MySQL client to the MySQL server (classic MySQL protocol) | Yes                     | Optional (use 3306, 33060, or 22) | From MySQL Workbench to the MySQL server |
| 33060/TCP             | MySQL client to the MySQL server (X Protocol)             | Yes                     | Optional (use 3306, 33060, or 22) | From MySQL Workbench to the MySQL server |
| 22/TCP                | Connection via SSH tunnel                                 | Yes                     | Optional (use 3306, 33060, or 22) | From MySQL Workbench to the MySQL server |



### MySQL Client - MySQL Router Connection Ports

**Table 3.5 Client - Router Connection Ports**

| Default Port/Protocol | Description                                                  | SSL or other Encryption                                      | Required                                            | Direction                               |
| :-------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :-------------------------------------------------- | :-------------------------------------- |
| 6446/TCP              | Read-write SQL from the MySQL client to MySQL Router (classic MySQL protocol) | Yes. Inherited from the MySQL client and server. If the client [`--ssl-mode`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode) is `VERIFY_IDENTITY`, the router must reside at the same IP address as the server. | Required if MySQL Router provides read-write access | MySQL client read-write to MySQL Router |
| 6447/TCP              | Read-only SQL from the MySQL client to MySQL Router (classic MySQL protocol) | Same as above                                                | Required if MySQL Router provides read-only access  | MySQL client read-only to MySQL Router  |
| 6448/TCP              | Read-write API calls from the MySQL client to MySQL Router (X Protocol) | Same as above                                                | Required if MySQL Router provides read-write access | MySQL client to MySQL Router            |
| 6449/TCP              | Read-only calls from the MySQL client to MySQL Router (X Protocol) | Same as above                                                | Required if MySQL Router provides read-only access  | MySQL client to MySQL Router            |
| 3306/TCP              | MySQL Router to the MySQL server (classic MySQL protocol)    | Same as above                                                | Required                                            | MySQL Router to the MySQL server        |
| 33060/TCP             | MySQL Router to the MySQL server (X Protocol)                | Same as above                                                | Required                                            | MySQL Router to the MySQL server        |



### High Availability Ports

**Table 3.6 High Availability Ports**

| Default Port/Protocol | Description                                          | SSL or other Encryption | Required | Direction                                                    |
| :-------------------- | :--------------------------------------------------- | :---------------------- | :------- | :----------------------------------------------------------- |
| 33061/TCP             | MySQL Group Replication internal communications port | Yes                     | Yes      | Group Replication communication between group members (InnoDB Cluster instances) |
| 3306/TCP              | MySQL Replication                                    | Yes                     | Yes      | Replica connection to the source                             |



### External Authentication Ports

**Table 3.7 External Authentication Ports**

| Default Port/Protocol | Description                                        | SSL or other Encryption | Required                                                     | Direction                                                    |
| :-------------------- | :------------------------------------------------- | :---------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 389/TCP               | MySQL Enterprise Authentication (LDAP)             | Yes                     | Only if using external authentication to LDAP. Also supports use of SASL | MySQL Enterprise Authentication in MySQL server to LDAP      |
| 389/TCP               | MySQL Enterprise Authentication (Active Directory) | Yes                     | Only if using external authentication to LDAP                | MySQL Enterprise Authentication in MySQL server to Active Directory |



### Key Management Ports

Key management ports are used for the MySQL Keyring features and Transparent Data Encryption (TDE).

**Table 3.8 Key Management Ports**

| Default Port/Protocol                                  | Description                                                  | SSL or other Encryption | Required                                | Direction |
| :----------------------------------------------------- | :----------------------------------------------------------- | :---------------------- | :-------------------------------------- | :-------- |
| Varies. Refer to your key manager/vault documentation. | KMIP. Used with Oracle Key Vault, Gemalto KeySecure, Thales Vormetric key management server, and Fornetix Key Orchestration. | Yes                     | Only required if TDE uses a KMIP server | N/A       |
| 443/TCP                                                | Key Services - AWS Key Management Service (AWS KMS)          | Yes                     | Only required if TDE uses AWS KMS       | N/A       |


### MySQL Enterprise Backup Ports

**Table 3.9 MySQL Enterprise Backup Ports**

| Default Port/Protocol                                        | Description                                                  | SSL or other Encryption | Required                                                 | Direction                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :---------------------- | :------------------------------------------------------- | :----------------------------------------------------------- |
| 3306/TCP                                                     | Communication with the local instance                        | Yes                     | Optional. Can connect with TCP, socket, pipe, or memory. | To the local instance                                        |
| 3306/TCP                                                     | For InnoDB Cluster/Group Replication                         | Yes                     | Required for InnoDB Cluster Backup                       | To members of the cluster/group                              |
| 443/TCP                                                      | Oracle Object Store                                          | Yes                     | Optional                                                 | From MySQL Enterprise Backup to Object Store                 |
| 443/TCP                                                      | Amazon S3                                                    | Yes                     | Optional                                                 | From MySQL Enterprise Backup to Amazon S3                    |
| Varies. Refer to your media management system documentation. | Backup to Media Management System (MMS) through System Backup to Tape (SBT) | Vendor dependent        | Optional                                                 | From the memory management library to the media management server. Refer to your media management system documentation. |



### Memcached Protocol Port

**Table 3.10 Memcached Protocol Port**

| Default Port/Protocol | Description             | SSL or other Encryption | Required | Direction                                        |
| :-------------------- | :---------------------- | :---------------------- | :------- | :----------------------------------------------- |
| 11211/TCP             | InnoDB memcached Plugin | No                      | Optional | From memcached client to InnoDB memcached plugin |






