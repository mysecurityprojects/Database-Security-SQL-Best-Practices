# SQL-Database-Best-Practices

Database security faces an evolving set of challenges stemming from technical vulnerabilities, human error and malicious intent. According to *BREACHSENSE*, there were over 4100 publicly disclosed data breaches in 2024. Nearly 109 million affected accounts of those cyber incidents occurred in the 3rd quarter costing an average of $4.88M per incident.  

The following are important concepts and SQL commands that can be implemented in SQL databases to prevent data from being compromised:

**AUTHENTICATION AND AUTHORIZATION**

(A) **Use Strong Passwords and Password Policies**

  CREATE LOGIN [NewUser] WITH PASSWORD = 'StR0nG_p@ssW0rd!;

(B) **Restrict Database Access to Necessary Users**

CREATE USER [ReadOnlyUser] FOR LOGIN [NewUser];


ALTER ROLE db_datareader ADD MEMBER [ReadOnlyUser];

(C) **Use Role-Based Access Control (RBAC)**


In this example, we create a role and grant specific permissions to the user.

USE YourDatabase;


CREATE ROLE [CustomRole];


GRANT SELECT, UPDATE ON YourTable TO [CustomRole];



ALTER ROLE [CustomRole] ADD MEMBER [NewUser];

(D) **Grant, Revoke and Deny Access to Files**

In the first command, a user in the HR department has been granted access to read and insert data in the HR database. In the second command, their access has been revoked. In the third example. the user is unable to access the database. 


GRANT SELECT, INSERT ON TABLE hr_files TO USER hr_user;


REVOKE SELECT, INSERT ON TABLE hr_files FROM USER hr_user;

DENY SELECT ON TABLE hr_files TO 'username';


**ENCRYPT DATA IN REST AND IN TRANSIT**

(A) **Encrypt Data at Rest**


Data at rest refers to the data stored in your database. Encrypting this data helps protect sensitive information from unauthorized access, even if an attacker gains access to the database files.

USE master;

CREATE DATABASE ENCRYPTION KEY


WITH ALGORITHM = AES_256
ENCRYPTION BY SERVER CERTIFICATE TDE_Certificate;


ALTER DATABASE YourDatabase SET ENCRYPTION ON;


(B) **Encrypt Data in Transit**

Encrypting data in transit means protecting data as it travels between the client application and the database server. One common method of achieving this is by using SSL/TLS encryption. configuration settings for a database server such as PostgreSQL.

ssl = on

ssl_cert_file = 'server.crt'

ssl_key_file = 'server.key'

**PREVENT SQL INJECTION ATTACKS**

(A) **Validate and Sanitize User Input**

The following is an example from the Python re library.


import re

def is_valid_name(name):
    return bool(re.match("^[A-Za-z\s]+$", name))

user_input = "John Doe"

if is_valid_name(user_input):
    # Proceed with query execution
    pass

    
else:
    print("Invalid input")

    

**MONITOR AND AUDIT DATABASE ACTIVITY**

(A) **Enable Auditing and Logging**

USE master;
CREATE SERVER AUDIT [YourAudit]
TO FILE (FILEPATH = 'C:\Audits\')
WITH (QUEUE_DELAY = 1000);

-- Create a database audit specification


USE YourDatabase;


CREATE DATABASE AUDIT SPECIFICATION [YourDatabaseAudit]


FOR SERVER AUDIT [YourAudit]


ADD (SELECT, INSERT, UPDATE, DELETE ON SCHEMA::[dbo] BY [public]);



-- Enable the audit

ALTER SERVER AUDIT [YourAudit] WITH (STATE = ON);

