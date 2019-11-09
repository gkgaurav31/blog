# Sample code to access SQL Database from Azure WebApp using Managed Identity

## How to get the access token:
- Azure WebApp has two environment variables which are used for Managed Identity:
1. MSI_ENDPOINT
2. MSI_SECRET
- The access token can be retrieved by sending a GET request to the MSI_ENDPOINT with the required parameters:
1. resource : query parameter
2. api-version: query paramter (The only supported version as of November 2019 is "2017-09-01")
3. secret : Header
4. clientid : query paramter (Optional for System Managed Identity)

### Example of a cURL request:
```curl
curl $MSI_ENDPOINT'?api-version=2017-09-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -H Metadata:true -H secret:$MSI_SECRET -s
```

```php
<?php

$accToken='';

$azureServer = 'mydb.database.windows.net';
$azureDatabase = 'mydb';
$connectionInfo = array('Database'=>$azureDatabase,
                        'AccessToken'=>$accToken);
$conn = sqlsrv_connect($azureServer, $connectionInfo);

if ($conn === false) {
    echo "Could not connect with Authentication=AccessToken (system-assigned).\n";
    print_r(sqlsrv_errors());
} else {
    echo "Connected successfully with Authentication=AccessToken (system-assigned).\n";
    
    $tsql = "SELECT @@Version AS SQL_VERSION";
    $stmt = sqlsrv_query($conn, $tsql);
    if ($stmt === false) {
        echo "Failed to run the simple query (system-assigned).\n";
        print_r(sqlsrv_errors());
    } else {
        while ($row = sqlsrv_fetch_array($stmt, SQLSRV_FETCH_ASSOC)) {
            echo $row['SQL_VERSION'] . PHP_EOL;
        }

        sqlsrv_free_stmt($stmt);
    }
    
    sqlsrv_close($conn);
}
?>

```