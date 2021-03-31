---
layout: post
title: Access SQL Database via Azure Function App - Python
date: 2019-11-17 00:01 +0530
categories: sql python azure_functions
---

# Sample Code - Access Azure SQL Database via Python Function App 

```python
import logging

import azure.functions as func
import pypyodbc as pyodbc

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    server = 'tcp:mydb.database.windows.net' 
    database = 'mydb' 
    username = 'myusername' 
    password = '******' 
    cnxn = pyodbc.connect('DRIVER={ODBC Driver 17 for SQL Server};SERVER='+server+';DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()

    cursor.execute("SELECT @@version;") 
    row = cursor.fetchone() 

    version = row[0]

    return func.HttpResponse(f"Hello {version}!")

```

__Reference:__   
  
[https://docs.microsoft.com/en-us/sql/connect/python/pyodbc/step-3-proof-of-concept-connecting-to-sql-using-pyodbc?view=sql-server-ver15#step-1--connect](https://docs.microsoft.com/en-us/sql/connect/python/pyodbc/step-3-proof-of-concept-connecting-to-sql-using-pyodbc?view=sql-server-ver15#step-1--connect)


__Deploy to Function App:__
```
func azure functionapp publish myFunctionAppName --build remote --build-native-deps
```