---
title: "Connect Python Azure SQL DB using Pandas"
date: 2022-05-21T20:58:05+09:30
draft: false
toc: false
images:
tags:
  - sql
  - azure
  - python
  - linux
---

The other day I wanted to connect `pandas` to Azure SQL DB and boy took me longer than I wanted.
In the end I solved my problem. Although, connecting to DBs always involves more complex things like DB drives and OS dependencies the part I struggled the most was with the connection string 😅.
Microsoft, in its [documentation](https://docs.microsoft.com/en-us/sql/connect/python/pyodbc/python-sql-driver-pyodbc?view=sql-server-ver15), which by the way is good, gives good example and steps on setting up the environnement and how to connect through `pyodbc`.

Nevertheless, I wanted to connect using `sqlalchemy`.

Both works fine with `pandas` but I specifically wanted to connect with `sqlalchemy` for reasons I will get into later in this post.

I run my code in my Windows working computer always in WSL (Ubuntu) so I will go step by step on what you need to get your environment running and also a starting code to get you connected.

steps are

1. Prepare the environment
2. Connect

## 1. Prepare the environnement

### OS dependencies

#### SQL server driver.

To install in linux the driver "Microsoft ODBC 18" just follow this [instructions](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-ver15#18)

for [Ubuntu](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-ver15#ubuntu18) here is the script

```bash
if ! [[ "18.04 20.04 21.04" == *"$(lsb_release -rs)"* ]];
then
    echo "Ubuntu $(lsb_release -rs) is not currently supported.";
    exit;
fi

sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

curl https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/prod.list > /etc/apt/sources.list.d/mssql-release.list

exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql18
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools18
echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install -y unixodbc-dev

```

#### Python environment

for the examples below install `pyodbc` `sqlalchemy` and `pandas`

```bash
pip install pyodbc SQLAlchemy pandas
```

## 2. Connect

Now comes the easy... part... the code

### Connecting using `pyodbc`

```python
import os

import pandas as pd
import pyodbc

server = '<my-server-name>.database.windows.net'
database = '<my-db-name>'
driver= '{ODBC Driver 18 for SQL Server}'

username = os.getenv('SQL_AZURE_USERNAME')
password = os.getenv('SQL_AZURE_PASSWORD')

connection_string = f'DRIVER={driver};SERVER=tcp:{server};PORT=1433;DATABASE={database};UID={username};PWD={password}'

# query system views

with pyodbc.connect(connection_string) as conn:
    query = '''
            SELECT
                [name]
                ,[database_id]
            FROM [sys].[databases];
    '''

    df = pd.read_sql(sql=query, con=conn)
    print(df.head())

```

This will print to the output the first 10 rows of the query result of the data frame.

It works fine but `pandas` will issue the following warning

```bash
UserWarning: pandas only support SQLAlchemy connectable(engine/connection) ordatabase string URI or sqlite3 DBAPI2 connectionother DBAPI2 objects are not tested, please consider using SQLAlchemy
```

### Connecting using `sqlalchemy`

The documentation in SQLAlchemy does not goes great length explaining the connection string with SQL Server DB and after a few stack overflow posts here is a working code.

```python
import os

import pandas as pd
from sqlalchemy import create_engine

server = '<my-server-name>.database.windows.net'
database = '<my-db-name>'
driver= '{ODBC Driver 18 for SQL Server}'

username = os.getenv('SQL_AZURE_USERNAME')
password = os.getenv('SQL_AZURE_PASSWORD')

odbc_params = f'DRIVER={driver};SERVER=tcp:{server};PORT=1433;DATABASE={database};UID={username};PWD={password}'

connection_string = f'mssql+pyodbc:///?odbc_connect={odbc_params}'

engine = create_engine(connection_string)

# query sys.databases view

query = '''
        SELECT
               [name]
              ,[database_id]
       FROM [sys].[databases];
'''

df = pd.read_sql(query, engine)
print(df.head())

engine.dispose()
```

It is very similar. You just need to "attach" the same pyodbc string after `'mssql+pyodbc:///?odbc_connect='`

for more detail on sql and `pandas` the
`pandas` [documentation](https://pandas.pydata.org/docs/reference/api/pandas.read_sql.html) is very good.

this is it.

If I had this post on my google search my life would be simpler but I would have learned less
😃
