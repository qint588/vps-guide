Here’s how you can import and dump a MySQL database using the command line:

---

### **1. Dump MySQL Database**

To create a backup (dump) of a MySQL database, you can use the `mysqldump` command.

#### Basic Command for Dumping a Database:
```bash
mysqldump -u <username> -p <database_name> > <output_file.sql>
```
- Replace `<username>` with your MySQL username.
- Replace `<database_name>` with the name of the database you want to dump.
- Replace `<output_file.sql>` with the name of the SQL file where the dump will be saved.

After running the command, you'll be prompted to enter the MySQL password for the specified user.

#### Example:
```bash
mysqldump -u root -p my_database > my_database_backup.sql
```

#### Dump a Specific Table:
If you want to dump a specific table from the database, use the following command:
```bash
mysqldump -u <username> -p <database_name> <table_name> > <output_file.sql>
```

#### Dump All Databases:
To dump all databases, use the `--all-databases` flag:
```bash
mysqldump -u <username> -p --all-databases > all_databases_backup.sql
```

#### Dump Database with Compression:
To compress the backup, you can use `gzip`:
```bash
mysqldump -u <username> -p <database_name> | gzip > <output_file.sql.gz>
```

---

### **2. Import MySQL Database**

To import a MySQL database from a `.sql` dump file, use the `mysql` command.

#### Basic Command for Importing a Database:
```bash
mysql -u <username> -p <database_name> < <input_file.sql>
```
- Replace `<username>` with your MySQL username.
- Replace `<database_name>` with the name of the database into which you want to import the data.
- Replace `<input_file.sql>` with the SQL file containing the dump.

After running the command, you’ll be prompted to enter the MySQL password.

#### Example:
```bash
mysql -u root -p my_database < my_database_backup.sql
```

#### Create a New Database and Import:
If the database doesn’t exist yet, you can create it and then import the data:
```bash
mysql -u root -p
CREATE DATABASE new_database;
exit;
mysql -u root -p new_database < my_database_backup.sql
```

#### Import Compressed Dump File (gzip):
If you have a compressed dump file (e.g., `.sql.gz`), you can use the following command to import it:
```bash
gunzip < <input_file.sql.gz> | mysql -u <username> -p <database_name>
```

---

### **3. Additional Options**

- **To dump specific user privileges**:
  ```bash
  mysqldump -u root -p --no-create-db --skip-triggers --routines --all-databases > all_databases_privileges.sql
  ```
- **To include stored procedures and functions**:
  ```bash
  mysqldump -u <username> -p --routines <database_name> > <output_file.sql>
  ```
