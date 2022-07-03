---
title: "SqlServer in docker in less than 5 minutes"
date: 2022-07-04T08:30:23+09:30
draft: false
toc: false
images:
tags:
  - sql server
  - sql
  - docker
  - ssms
---

Who does not want to spin up an instance of SQL Server Development version?

If you don't want to go through the pain of downloading and following the installation wizard in windows you can have an instance of SQL server 2022 running on your system in less than 5 min in docker.

I am assuming 2 things you already have installed in your computer.

- [Docker Desktop](https://docs.docker.com/desktop/windows/wsl/)
- [WSL](https://docs.microsoft.com/en-us/windows/wsl/install)
- SQL server [management studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16)

This version runs on a linux container.

with all that then follow these steps.

All credit to this article.

article [link](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-bash)

make sure the password meet SQL server requirements

at least 10 characters long
1 number
1 capital/lower
1 special character

open your bash prompt on your WSL.

```bash
sudo docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=StrongP@ssword42" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

Ger inside the container

```bash
sudo docker exec -it sql1 "bash"
```

inside the container

```bash
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "StrongP@ssword42"
```

create a DB, Table and insert some data

```sql
CREATE DATABASE TestDB;

GO
```

```sql
USE TestDB;

CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT);

INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);

GO
```

```sql
SELECT * FROM Inventory

GO
```

To connect via SQL Server Management Studio from your local machine.

server name : `127.0.0.1,1433`

user name : `sa`

password: `StrongP@ssword42`

{{< figure src="/blog/img/connection.png" title="connection" >}}

before pressing connect make sure you mark trust server certificate

{{< figure src="/blog/img/trust_certificate.png" title="trust certificate" >}}

then your server is ready to go

{{< figure src="/blog/img/connected.png" title="connected" >}}

have fun ! 😀
