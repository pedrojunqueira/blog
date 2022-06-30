---
title: "Do not use pandas use with pyodbc"
date: 2022-06-30T22:17:57+09:30
draft: false
toc: false
images:
tags:
  - pandas
  - sql
  - pyodbc
  - data factory
---

Yesterday I published a post on and shared on LinkedIn and had a quite big reaction as an popular PBI [influencer ](https://www.linkedin.com/in/mhalkjaer/) commented on my post.

One of the comments by [Jesus Jayaro](https://www.linkedin.com/in/jayaro/) called my attention saying that if `pyodbc` is used with execute many it is faster than data factory.

So I did a quick research and in reality it is fast I am impressed.

On my quick research I could put together a working script. It failed at first and did not want to spend too much time on this.

I tried to do the insert into one chunk and it failed using a script i found in [medium](https://towardsdatascience.com/how-i-made-inserts-into-sql-server-100x-faster-with-pyodbc-5a0b5afdba5) and then I tried in small chunks and it worked.

but took 500 seconds. Not bad 👍🏻 compared to the initial 55,000 seconds estimate. So it is 0.8% of the time. So more than 100x faster.

At least in this example data factory was faster it was a 5 minutes 35 seconds against 8 minutes 20 seconds of `pyodbc`.

here is the script.

```python
import os
import logging

import pandas as pd
import pyodbc

from utils import timing

SQL_SERVER = os.getenv('SQL_SERVER')
SQL_DATABASE = os.getenv('SQL_DATABASE')
SQL_USER_NAME = os.getenv('SQL_AZURE_USERNAME')
SQL_PASSWORD = os.getenv('SQL_AZURE_PASSWORD')

driver= '{ODBC Driver 18 for SQL Server}'


def fast_load(chunk: pd.DataFrame)->None:

    connection_string = f'DRIVER={driver};SERVER=tcp:{SQL_SERVER};PORT=1433;DATABASE={SQL_DATABASE};UID={SQL_USER_NAME};PWD={SQL_PASSWORD}'

    MY_TABLE = "taxi"

    with pyodbc.connect(connection_string) as conn:

        try:

            insert_to_tmp_tbl_stmt = f"INSERT INTO {MY_TABLE} VALUES (?,?,?,?,?,?,?,?,?,?,?)"
            cursor = conn.cursor()
            cursor.fast_executemany = True
            tuples = chunk.values.tolist()
            if tuples:
                cursor.executemany(insert_to_tmp_tbl_stmt, tuples)
                cursor.commit()
        except Exception as e:
            logging.debug(e)

def upload_batches(batch_size:int, df:pd.DataFrame)-> None:
    batches =  int(round(df.shape[0] / batch_size,0))
    reminder =  df.shape[0] % batch_size
    end = 0
    for i in range(batches + 1):
        start = end
        end += batch_size if i < batches else (reminder+1)
        print(f"uploading batch {i+1}/{batches+1}")
        fast_load(df[start:end])
        start = end

@timing
def run_script()->None:

    taxi = pd.read_csv('taxi.csv')
    upload_batches(10000, taxi)

run_script()

# func: run_script took: 500.3895 sec

```

If you can provide a faster script of challenge data factory please contact me on [LinkedIn](https://www.linkedin.com/in/pedro-junqueira-537184/)
