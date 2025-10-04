# PeaceB_plsqlauca_27778
Oracle Enterprise Manager
# Peace_plsqlauca_27778
Oracle Enterprise Manager Assignment

# 🧩 Oracle Database Configuration and OEM Express Management Report

## 📘 1. Introduction
This project demonstrates the configuration of an **Oracle 21c Pluggable Database (PDB)** and enabling web-based management via **Oracle Enterprise Manager (OEM) Express**.  
It covers listener setup, database connectivity, OEM configuration, and troubleshooting of common port and connection issues.

---

## ⚙️ 2. System Overview
**Environment**
- Oracle Database 21c (64-bit, Windows host)
- Oracle Listener & XDB services
- SQL\*Plus client interface
- OEM Express (Web UI)

**Database Setup**
- **Container Database (CDB):** `ORCL`
- **Pluggable Database (PDB):** `BP_PDB_27778`
- **Users:**
  - `SYS` – Administrative superuser (SYSDBA role)
  - `SYSTEM` – Standard administrative account

---

## 🔧 3. Configuration Procedure

### 3.1 Verify Listener Status
Check if the Oracle listener is running:
```bash
lsnrctl status
### Expected output includes:
Listening on: (ADDRESS=(PROTOCOL=TCP)(HOST=192.168.56.1)(PORT=1521))
Service "bp_pdb_27778" ... status READY
###3.2 Connect to the Database
**Because the listener is bound to 192.168.56.1, connect using:**
sqlplus sys/admin@//192.168.56.1:1521/bp_pdb_27778 AS SYSDBA
##3.3 Enable OEM Express Port
**Check current HTTPS port:**
SELECT DBMS_XDB_CONFIG.GETHTTPSPORT FROM dual;

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
