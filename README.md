# Peace_plsqlauca_27778
Oracle Enterprise Manager Assignment

# Oracle Database Configuration and OEM Express Management Report

## 1. Introduction
This project demonstrates the configuration of an **Oracle 21c Pluggable Database (PDB)** and enabling web-based management via **Oracle Enterprise Manager (OEM) Express**.  
It covers listener setup, database connectivity, OEM configuration, and troubleshooting of common port and connection issues.

---

## 2. System Overview
**Environment**
- Oracle Database 21c (64-bit, Windows host)
- Oracle Listener & XDB services
- SQL\*Plus client interface
- OEM Express (Web UI)

**Database Setup**
- **Container Database (CDB):** `ORCL`
- **Pluggable Database (PDB):** `PE_PDB_27778`
- **Users:**
  - `SYS` – Administrative superuser (SYSDBA role)
  - `SYSTEM` – Standard administrative account

---

##  3. Configuration Procedure

### 3.1 Verify Listener Status
Check if the Oracle listener is running:
```bash
lsnrctl status
### Expected output includes:
Listening on: (ADDRESS=(PROTOCOL=TCP)(HOST=192.168.56.1)(PORT=1521))
Service "pe_pdb_27778" ... status READY
###3.2 Connect to the Database
**Because the listener is bound to 192.168.56.1, connect using:**
sqlplus sys/admin@//192.168.56.1:1521/pe_pdb_27778 AS SYSDBA
##3.3 Enable OEM Express Port
**Check current HTTPS port:**
SELECT DBMS_XDB_CONFIG.GETHTTPSPORT FROM dual;

Configuration Steps with Screenshots 
-Create a Pluggable Database (PDB) 
<img width="550" height="183" alt="Image" src="https://github.com/user-attachments/assets/01f04faf-478c-4bcf-b2ea-550ce38f5cb6" />
Explanation:
This screenshot shows the successful creation of a pluggable database named
pe_PDB_27778 using the PDB$SEED template. The admin user peace_plsql_27778 was
created with a default password. The FILE_NAME_CONVERT clause ensures the
datafiles are copied to the correct directory
-Verify PDB Creation
<img width="371" height="366" alt="Image" src="https://github.com/user-attachments/assets/87c1994d-52eb-4b55-90d0-22e95734b382" />
Explanation:
The query SELECT NAME, OPEN_MODE FROM V$PDBS; confirms that the PDB
pe_PDB_27778 was created and is in READ WRITE mode, indicating it is open
and operational.
-Check Current HTTP and HTTPS Ports
<img width="583" height="476" alt="Image" src="https://github.com/user-attachments/assets/c4d97ec8-6bd5-4152-9019-553898cc6e9e" />
Explanation:
This output shows the initial HTTP and HTTPS ports (8680 and 8443) before
reconfiguration. The ports were later changed to avoid conflicts.
-Change HTTP and HTTPS Ports
<img width="583" height="476" alt="Image" src="https://github.com/user-attachments/assets/c4d97ec8-6bd5-4152-9019-553898cc6e9e" />
Explanation:
After executing DBMS_XDB_CONFIG.SETHTTPSPORT and SETHTTPPORT, the
new ports (8450 and 8081) are confirmed. This step resolves port conflicts
and enables OEM Express access.
-Change OEM Express Port for PDB
<img width="585" height="328" alt="Image" src="https://github.com/user-attachments/assets/70f13086-a02b-499b-a7ec-053e68b58629" />
Explanation:
The session container is set to PE_PDB_27778, and the HTTPS port is changed
to 8450 using DBMS_XDB_CONFIG.SETHTTPSPORT. The change is verified with
a SELECT query.
-Create Additional PDB for Testing
<img width="497" height="166" alt="Image" src="https://github.com/user-attachments/assets/2ec438a2-823d-4d3a-bb52-a2bd3ae585b2" />
Explanation:
A second PDB named peace_PDB_27778 is created for demonstration and
deletion purposes. This shows the flexibility of managing multiple PDBs.
-Drop the Test PDB
<img width="163" height="105" alt="Image" src="https://github.com/user-attachments/assets/80f23270-54f9-4ced-9c2c-0edc9291b2eb" />
<img width="601" height="175" alt="Image" src="https://github.com/user-attachments/assets/19d522ad-4b01-4cf8-8d53-6fe68433ef04" />
Explanation:
The test PDB PE_TO_delete_PDB_27778 is dropped using the DROP
PLUGGABLE DATABASE command with the INCLUDING DATAFILES option to
remove all associated files.
-Access OEM Express Dashboard
<img width="1297" height="604" alt="Image" src="https://github.com/user-attachments/assets/f74cc910-c907-443f-a409-2150a2a76240" />
Explanation:
This is the main OEM Express dashboard for the PDB PE_PDB_27778. It
displays database status, performance metrics, resource usage, and storage
information.
-Monitor OEM Activities and SQL Queries
<img width="591" height="573" alt="Image" src="https://github.com/user-attachments/assets/4d294991-7ad7-4484-b35c-6aa0d2e1920b" />
Explanation:
This view shows real-time resource usage (CPU, memory, storage) and active
SQL sessions. It helps in performance monitoring and identifying long-running
queries. 
-OEM Dashboard with User Context
<img width="1280" height="432" alt="Image" src="https://github.com/user-attachments/assets/66a3361b-ea7a-4aeb-8c8e-99ced6c33b27" />
Explanation:
This screenshot highlights SQL monitoring details, including active sessions
and user-specific queries. It shows SYS@PE_PDB_27778 executing a query with
significant I/O requests.



### 4. Troubleshooting and Solutions
** ORA-12541: TNS:no listener

Cause: Listener not bound to localhost.
Fix: Use the correct IP in connection string (192.168.56.1).

 ORA-44718: Port conflict in XDB Configuration file

Cause: HTTPS port already in use by another container.
Fix:
Find which container is using it:**

###Codes:
SELECT CON_ID, CON_NAME, DBMS_XDB_CONFIG.GETHTTPSPORT() AS HTTPS_PORT FROM V$CONTAINERS;

### 5. OEM Express Access

After enabling port 8450, open in your browser:

**https://192.168.56.1:8450/em**

###Login Credentials:

| Field         | Value        |
| ------------- | ------------ |
| **Username**  | SYSTEM       |
| **Password**  | 1234567890   |
| **Role**      | Normal       |
| **Container** | BP_PDB_27778 |


###6. Results
| Test                   | Expected Outcome          | Result   |
| ---------------------- | ------------------------- | -------- |
| Listener status        | Active on 1521            |  Passed |
| Database connection    | Successful via SYS/SYSTEM |  Passed |
| OEM port configuration | HTTPS 8450 enabled        |  Passed |
| OEM dashboard access   | Web access works          |  Passed |

###7. Issues & Resolutions
| Issue              | Description                        | Resolution                                     |
| ------------------ | ---------------------------------- | ---------------------------------------------- |
| ORA-12541          | “No listener” when using localhost | Used actual host IP (192.168.56.1)             |
| ORA-44718          | Port conflict when setting 8445    | Disabled conflict or chose 8450                |
| Invalid Identifier | `con_name` not recognized          | Used `SHOW CON_NAME` or `V$CONTAINERS` instead |
| HTTPS Port 0       | OEM disabled by default            | Enabled manually with DBMS_XDB_CONFIG          |

###8. Conclusion

**This project successfully configured the Oracle Listener, PDB, and OEM Express.
It highlights how to diagnose listener issues, manage multiple containers, resolve port conflicts, and confirm full web-based administration via OEM Express.**

9.References
1. Oracle Corporation. (2023). Oracle Database 21c Documentation. Retrieved from
https://docs.oracle.com/en/database/oracle/oracledatabase/21/index.html
2. Oracle Help Center. (2023). Using Oracle Enterprise Manager Database Express.
Retrieved from https://docs.oracle.com/en/database/oracle/oracledatabase/21/emxug/index.html
3. Stack Overflow. (n.d.). ORA-44718: Port conflict in XDB Configuration file – Solutions.
Retrieved from https://stackoverflow.com/
4. Oracle Base. (2022). Multitenant : Create and Configure a Pluggable Database (PDB).
Retrieved from https://oracle-base.com/articles/12c/multitenant-createandconfigure-pluggable-database-12cr1 
