{
    "metadata": {
        "kernelspec": {
            "name": "SQL",
            "display_name": "SQL",
            "language": "sql"
        },
        "language_info": {
            "name": "sql",
            "version": ""
        }
    },
    "nbformat_minor": 2,
    "nbformat": 4,
    "cells": [
        {
            "cell_type": "markdown",
            "source": "<h1>Monitorización de Base de Datos mediante Auditoría</h1>",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "<h2>Ejercicio N° 01: Utilizando tablas temporales de auditoría</h2>",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "Step 1 - connect this query window to your copy of AdventureWorksLT",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "Step 2 - create a system-versioned temporary table",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "CREATE TABLE dbo.Manager\r\n( ManagerId int NOT NULL PRIMARY KEY,\r\n  ManagerName nvarchar(50) NOT NULL,\r\n  ManagerPassword varbinary(200) NOT NULL,\r\n  ValidFrom datetime2 GENERATED ALWAYS AS ROW START NOT NULL, \r\n  ValidTo datetime2 GENERATED ALWAYS AS ROW END NOT NULL, \r\n  ChangedBy sysname NOT NULL CONSTRAINT DF_Employee_ChangedBy DEFAULT  (SUSER_SNAME()),\r\n  PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo) \r\n) WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.ManagerHistory));\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.074"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 1
        },
        {
            "cell_type": "markdown",
            "source": "Step 3 - insert example data",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "INSERT dbo.Manager (ManagerId, ManagerName, ManagerPassword)\r\nVALUES (1, N'superuser', 0x3EED6B62548EC64A90E5D5D186FC9E5C),\r\n(2, N'PeledYael', 0x19EF561D56A24D42A54169BD3DE23652),\r\n(3, N'DavisSara', 0xD42025F8D7A67248AC36C5C8E955FA71);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(3 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.004"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 2
        },
        {
            "cell_type": "markdown",
            "source": "Step 4 - update a row",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "UPDATE dbo.Manager \r\nSET ManagerPassword = 0x3EED6B62548EC64A90E5D5D186FCFFFF,\r\nManagerName = 'administrator'\r\nWHERE ManagerId = 1",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.008"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 3
        },
        {
            "cell_type": "markdown",
            "source": "Step 5 - examine temporal table component tables",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT * FROM dbo.Manager;\r\n\r\nSELECT * FROM dbo.ManagerHistory;",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(3 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.096"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "execution_count": 4,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "ManagerId"
                                    },
                                    {
                                        "name": "ManagerName"
                                    },
                                    {
                                        "name": "ManagerPassword"
                                    },
                                    {
                                        "name": "ValidFrom"
                                    },
                                    {
                                        "name": "ValidTo"
                                    },
                                    {
                                        "name": "ChangedBy"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "1",
                                    "1": "administrator",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FCFFFF",
                                    "3": "2019-07-01 20:22:25.1872377",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "2",
                                    "1": "PeledYael",
                                    "2": "0x19EF561D56A24D42A54169BD3DE23652",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "3",
                                    "1": "DavisSara",
                                    "2": "0xD42025F8D7A67248AC36C5C8E955FA71",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>ManagerId</th><th>ManagerName</th><th>ManagerPassword</th><th>ValidFrom</th><th>ValidTo</th><th>ChangedBy</th></tr><tr><td>1</td><td>administrator</td><td>0x3EED6B62548EC64A90E5D5D186FCFFFF</td><td>2019-07-01 20:22:25.1872377</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>2</td><td>PeledYael</td><td>0x19EF561D56A24D42A54169BD3DE23652</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>3</td><td>DavisSara</td><td>0xD42025F8D7A67248AC36C5C8E955FA71</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr></table>"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "execution_count": 4,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "ManagerId"
                                    },
                                    {
                                        "name": "ManagerName"
                                    },
                                    {
                                        "name": "ManagerPassword"
                                    },
                                    {
                                        "name": "ValidFrom"
                                    },
                                    {
                                        "name": "ValidTo"
                                    },
                                    {
                                        "name": "ChangedBy"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "1",
                                    "1": "superuser",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FC9E5C",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "2019-07-01 20:22:25.1872377",
                                    "5": "CMACT\\pcuadros_dev"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>ManagerId</th><th>ManagerName</th><th>ManagerPassword</th><th>ValidFrom</th><th>ValidTo</th><th>ChangedBy</th></tr><tr><td>1</td><td>superuser</td><td>0x3EED6B62548EC64A90E5D5D186FC9E5C</td><td>2019-07-01 20:21:53.1216323</td><td>2019-07-01 20:22:25.1872377</td><td>CMACT\\pcuadros_dev</td></tr></table>"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 4
        },
        {
            "cell_type": "markdown",
            "source": "Step 6 - demonstrate FOR SYSTEM TIME ALL when querying a temporal table\r\nALL shows all data in both tables",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT * FROM dbo.Manager \r\nFOR SYSTEM_TIME ALL;",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(4 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.011"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "execution_count": 5,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "ManagerId"
                                    },
                                    {
                                        "name": "ManagerName"
                                    },
                                    {
                                        "name": "ManagerPassword"
                                    },
                                    {
                                        "name": "ValidFrom"
                                    },
                                    {
                                        "name": "ValidTo"
                                    },
                                    {
                                        "name": "ChangedBy"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "1",
                                    "1": "administrator",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FCFFFF",
                                    "3": "2019-07-01 20:22:25.1872377",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "2",
                                    "1": "PeledYael",
                                    "2": "0x19EF561D56A24D42A54169BD3DE23652",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "3",
                                    "1": "DavisSara",
                                    "2": "0xD42025F8D7A67248AC36C5C8E955FA71",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "1",
                                    "1": "superuser",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FC9E5C",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "2019-07-01 20:22:25.1872377",
                                    "5": "CMACT\\pcuadros_dev"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>ManagerId</th><th>ManagerName</th><th>ManagerPassword</th><th>ValidFrom</th><th>ValidTo</th><th>ChangedBy</th></tr><tr><td>1</td><td>administrator</td><td>0x3EED6B62548EC64A90E5D5D186FCFFFF</td><td>2019-07-01 20:22:25.1872377</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>2</td><td>PeledYael</td><td>0x19EF561D56A24D42A54169BD3DE23652</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>3</td><td>DavisSara</td><td>0xD42025F8D7A67248AC36C5C8E955FA71</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>1</td><td>superuser</td><td>0x3EED6B62548EC64A90E5D5D186FC9E5C</td><td>2019-07-01 20:21:53.1216323</td><td>2019-07-01 20:22:25.1872377</td><td>CMACT\\pcuadros_dev</td></tr></table>"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 5
        },
        {
            "cell_type": "markdown",
            "source": "Step 7 - demonstrate FOR SYSTEM TIME AS OF when querying a temporal table\r\nAS OF shows a point in time\r\nNote that this returns the original data",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "DECLARE @t datetime2 = (SELECT TOP(1) ValidFrom FROM dbo.ManagerHistory WHERE ManagerId = 1)\r\nSELECT * FROM dbo.Manager \r\nFOR SYSTEM_TIME AS OF @t\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(3 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.008"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "execution_count": 6,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "ManagerId"
                                    },
                                    {
                                        "name": "ManagerName"
                                    },
                                    {
                                        "name": "ManagerPassword"
                                    },
                                    {
                                        "name": "ValidFrom"
                                    },
                                    {
                                        "name": "ValidTo"
                                    },
                                    {
                                        "name": "ChangedBy"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "2",
                                    "1": "PeledYael",
                                    "2": "0x19EF561D56A24D42A54169BD3DE23652",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "3",
                                    "1": "DavisSara",
                                    "2": "0xD42025F8D7A67248AC36C5C8E955FA71",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "1",
                                    "1": "superuser",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FC9E5C",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "2019-07-01 20:22:25.1872377",
                                    "5": "CMACT\\pcuadros_dev"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>ManagerId</th><th>ManagerName</th><th>ManagerPassword</th><th>ValidFrom</th><th>ValidTo</th><th>ChangedBy</th></tr><tr><td>2</td><td>PeledYael</td><td>0x19EF561D56A24D42A54169BD3DE23652</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>3</td><td>DavisSara</td><td>0xD42025F8D7A67248AC36C5C8E955FA71</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>1</td><td>superuser</td><td>0x3EED6B62548EC64A90E5D5D186FC9E5C</td><td>2019-07-01 20:21:53.1216323</td><td>2019-07-01 20:22:25.1872377</td><td>CMACT\\pcuadros_dev</td></tr></table>"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 6
        },
        {
            "cell_type": "markdown",
            "source": "Step 8 - demonstrate that the history table cannot be edited (both commands will generate an error)",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "UPDATE dbo.ManagerHistory SET ChangedBy = 'maliciousUser';\r\nGO\r\nINSERT dbo.ManagerHistory (ManagerId, ManagerName, ManagerPassword)\r\nVALUES (99, N'superuser', 0x3EED6B62548EC64A90E5D5D186FC9E5C)\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "error",
                    "ename": "",
                    "evalue": "Msg 13561, Level 16, State 1, Line 1\r\nNo se pueden actualizar filas en una tabla temporal de historial 'AdventureWorks.dbo.ManagerHistory'.",
                    "traceback": []
                },
                {
                    "output_type": "error",
                    "ename": "",
                    "evalue": "Msg 13559, Level 16, State 1, Line 2\r\nNo se pueden insertar filas en una tabla temporal de historial 'AdventureWorks.dbo.ManagerHistory'.",
                    "traceback": []
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.010"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 7
        },
        {
            "cell_type": "markdown",
            "source": "Step 9 - demonstrate that a user with sufficient permissions can insert misleading data into the ChangedBy column:",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "UPDATE dbo.Manager \r\nSET ManagerPassword = 0x0A0B,\r\nManagerName = 'hacked', ChangedBy = 'maliciousUser'\r\nWHERE ManagerId = 1",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.004"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 8
        },
        {
            "cell_type": "markdown",
            "source": "Step 10 - examine temporal table component tables",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT * FROM dbo.Manager;\r\n\r\nSELECT * FROM dbo.ManagerHistory;",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(3 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(2 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.021"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "execution_count": 9,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "ManagerId"
                                    },
                                    {
                                        "name": "ManagerName"
                                    },
                                    {
                                        "name": "ManagerPassword"
                                    },
                                    {
                                        "name": "ValidFrom"
                                    },
                                    {
                                        "name": "ValidTo"
                                    },
                                    {
                                        "name": "ChangedBy"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "1",
                                    "1": "hacked",
                                    "2": "0x0A0B",
                                    "3": "2019-07-01 20:28:53.0942941",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "maliciousUser"
                                },
                                {
                                    "0": "2",
                                    "1": "PeledYael",
                                    "2": "0x19EF561D56A24D42A54169BD3DE23652",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "3",
                                    "1": "DavisSara",
                                    "2": "0xD42025F8D7A67248AC36C5C8E955FA71",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "9999-12-31 23:59:59.9999999",
                                    "5": "CMACT\\pcuadros_dev"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>ManagerId</th><th>ManagerName</th><th>ManagerPassword</th><th>ValidFrom</th><th>ValidTo</th><th>ChangedBy</th></tr><tr><td>1</td><td>hacked</td><td>0x0A0B</td><td>2019-07-01 20:28:53.0942941</td><td>9999-12-31 23:59:59.9999999</td><td>maliciousUser</td></tr><tr><td>2</td><td>PeledYael</td><td>0x19EF561D56A24D42A54169BD3DE23652</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>3</td><td>DavisSara</td><td>0xD42025F8D7A67248AC36C5C8E955FA71</td><td>2019-07-01 20:21:53.1216323</td><td>9999-12-31 23:59:59.9999999</td><td>CMACT\\pcuadros_dev</td></tr></table>"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "execution_count": 9,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "ManagerId"
                                    },
                                    {
                                        "name": "ManagerName"
                                    },
                                    {
                                        "name": "ManagerPassword"
                                    },
                                    {
                                        "name": "ValidFrom"
                                    },
                                    {
                                        "name": "ValidTo"
                                    },
                                    {
                                        "name": "ChangedBy"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "1",
                                    "1": "superuser",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FC9E5C",
                                    "3": "2019-07-01 20:21:53.1216323",
                                    "4": "2019-07-01 20:22:25.1872377",
                                    "5": "CMACT\\pcuadros_dev"
                                },
                                {
                                    "0": "1",
                                    "1": "administrator",
                                    "2": "0x3EED6B62548EC64A90E5D5D186FCFFFF",
                                    "3": "2019-07-01 20:22:25.1872377",
                                    "4": "2019-07-01 20:28:53.0942941",
                                    "5": "CMACT\\pcuadros_dev"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>ManagerId</th><th>ManagerName</th><th>ManagerPassword</th><th>ValidFrom</th><th>ValidTo</th><th>ChangedBy</th></tr><tr><td>1</td><td>superuser</td><td>0x3EED6B62548EC64A90E5D5D186FC9E5C</td><td>2019-07-01 20:21:53.1216323</td><td>2019-07-01 20:22:25.1872377</td><td>CMACT\\pcuadros_dev</td></tr><tr><td>1</td><td>administrator</td><td>0x3EED6B62548EC64A90E5D5D186FCFFFF</td><td>2019-07-01 20:22:25.1872377</td><td>2019-07-01 20:28:53.0942941</td><td>CMACT\\pcuadros_dev</td></tr></table>"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 9
        },
        {
            "cell_type": "markdown",
            "source": "Step 11 - tear down demonstration objects",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "ALTER TABLE dbo.Manager SET (SYSTEM_VERSIONING = OFF);\r\nDROP TABLE dbo.Manager;\r\nDROP TABLE dbo.ManagerHistory;",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.065"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 10
        },
        {
            "cell_type": "markdown",
            "source": "<h2>Ejercicio N° 02: Utilizando auditorias</h2>",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "Step 1 - create an audit",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE master;\r\nGO\r\nCREATE SERVER AUDIT MIASQL_Audit \r\n    TO FILE (FILEPATH='F:\\Data\\')\r\n    WITH (QUEUE_DELAY = 5000);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.002"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 35
        },
        {
            "cell_type": "markdown",
            "source": "Step 2 - enable the audit",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "ALTER SERVER AUDIT MIASQL_Audit WITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.006"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 36
        },
        {
            "cell_type": "markdown",
            "source": "Step 3 - create a server audit specification",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "CREATE SERVER AUDIT SPECIFICATION AuditLogins\r\nFOR SERVER AUDIT MIASQL_Audit\r\nADD (FAILED_LOGIN_GROUP),\r\nADD (SUCCESSFUL_LOGIN_GROUP)\r\nWITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.002"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 37
        },
        {
            "cell_type": "markdown",
            "source": "Step 4 - create a database audit specification",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE salesapp1;\r\nGO\r\nCREATE DATABASE AUDIT SPECIFICATION salesapp1_audit_spec\r\nFOR SERVER AUDIT MIASQL_Audit\r\nADD (INSERT,UPDATE ON DATABASE::salesapp1 BY public),\r\nADD (SELECT ON SCHEMA::HR BY public),\r\nADD (SCHEMA_OBJECT_CHANGE_GROUP)\r\nWITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.001"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 38
        },
        {
            "cell_type": "markdown",
            "source": "Step 5 - alter the database audit specification",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE salesapp1\r\nGO\r\nALTER DATABASE AUDIT SPECIFICATION salesapp1_audit_spec WITH (STATE = OFF);\r\nGO\r\nALTER DATABASE AUDIT SPECIFICATION salesapp1_audit_spec\r\nADD (SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP)\r\nWITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.002"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 39
        },
        {
            "cell_type": "markdown",
            "source": "Step 6 - examine audit metadata",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT * FROM sys.server_audits;",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.024"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 40,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "audit_id"
                                    },
                                    {
                                        "name": "name"
                                    },
                                    {
                                        "name": "audit_guid"
                                    },
                                    {
                                        "name": "create_date"
                                    },
                                    {
                                        "name": "modify_date"
                                    },
                                    {
                                        "name": "principal_id"
                                    },
                                    {
                                        "name": "type"
                                    },
                                    {
                                        "name": "type_desc"
                                    },
                                    {
                                        "name": "on_failure"
                                    },
                                    {
                                        "name": "on_failure_desc"
                                    },
                                    {
                                        "name": "is_state_enabled"
                                    },
                                    {
                                        "name": "queue_delay"
                                    },
                                    {
                                        "name": "predicate"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "65539",
                                    "1": "MIASQL_Audit",
                                    "2": "415d2580-f4ab-4091-82e2-60b3518b93f4",
                                    "3": "2019-07-01 16:07:49.730",
                                    "4": "2019-07-01 16:07:49.730",
                                    "5": "1",
                                    "6": "FL",
                                    "7": "FILE",
                                    "8": "0",
                                    "9": "CONTINUE",
                                    "10": "1",
                                    "11": "5000",
                                    "12": "NULL"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>audit_id</th><th>name</th><th>audit_guid</th><th>create_date</th><th>modify_date</th><th>principal_id</th><th>type</th><th>type_desc</th><th>on_failure</th><th>on_failure_desc</th><th>is_state_enabled</th><th>queue_delay</th><th>predicate</th></tr><tr><td>65539</td><td>MIASQL_Audit</td><td>415d2580-f4ab-4091-82e2-60b3518b93f4</td><td>2019-07-01 16:07:49.730</td><td>2019-07-01 16:07:49.730</td><td>1</td><td>FL</td><td>FILE</td><td>0</td><td>CONTINUE</td><td>1</td><td>5000</td><td>NULL</td></tr></table>"
                    }
                }
            ],
            "execution_count": 40
        },
        {
            "cell_type": "markdown",
            "source": "Step 7 - examine server audit specification metadata",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT * FROM sys.server_audit_specifications;\r\n\r\nSELECT *\r\nFROM sys.server_audit_specification_details AS sd\r\nJOIN sys.dm_audit_actions AS aa\r\nON aa.name = sd.audit_action_name COLLATE Latin1_General_CI_AS_KS_WS",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(2 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.041"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 41,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "server_specification_id"
                                    },
                                    {
                                        "name": "name"
                                    },
                                    {
                                        "name": "create_date"
                                    },
                                    {
                                        "name": "modify_date"
                                    },
                                    {
                                        "name": "audit_guid"
                                    },
                                    {
                                        "name": "is_state_enabled"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "65539",
                                    "1": "AuditLogins",
                                    "2": "2019-07-01 16:07:53.530",
                                    "3": "2019-07-01 16:07:53.530",
                                    "4": "415d2580-f4ab-4091-82e2-60b3518b93f4",
                                    "5": "1"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>server_specification_id</th><th>name</th><th>create_date</th><th>modify_date</th><th>audit_guid</th><th>is_state_enabled</th></tr><tr><td>65539</td><td>AuditLogins</td><td>2019-07-01 16:07:53.530</td><td>2019-07-01 16:07:53.530</td><td>415d2580-f4ab-4091-82e2-60b3518b93f4</td><td>1</td></tr></table>"
                    }
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 41,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "server_specification_id"
                                    },
                                    {
                                        "name": "audit_action_id"
                                    },
                                    {
                                        "name": "audit_action_name"
                                    },
                                    {
                                        "name": "class"
                                    },
                                    {
                                        "name": "class_desc"
                                    },
                                    {
                                        "name": "major_id"
                                    },
                                    {
                                        "name": "minor_id"
                                    },
                                    {
                                        "name": "audited_principal_id"
                                    },
                                    {
                                        "name": "audited_result"
                                    },
                                    {
                                        "name": "is_group"
                                    },
                                    {
                                        "name": "action_id"
                                    },
                                    {
                                        "name": "name"
                                    },
                                    {
                                        "name": "class_desc"
                                    },
                                    {
                                        "name": "covering_action_name"
                                    },
                                    {
                                        "name": "parent_class_desc"
                                    },
                                    {
                                        "name": "covering_parent_action_name"
                                    },
                                    {
                                        "name": "configuration_level"
                                    },
                                    {
                                        "name": "containing_group_name"
                                    },
                                    {
                                        "name": "action_in_log"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "65539",
                                    "1": "LGFL",
                                    "2": "FAILED_LOGIN_GROUP",
                                    "3": "100",
                                    "4": "SERVER",
                                    "5": "0",
                                    "6": "0",
                                    "7": "2",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "1",
                                    "10": "LGFL",
                                    "11": "FAILED_LOGIN_GROUP",
                                    "12": "SERVER",
                                    "13": "NULL",
                                    "14": "NULL",
                                    "15": "NULL",
                                    "16": "Group",
                                    "17": "FAILED_LOGIN_GROUP",
                                    "18": "0"
                                },
                                {
                                    "0": "65539",
                                    "1": "LGSD",
                                    "2": "SUCCESSFUL_LOGIN_GROUP",
                                    "3": "100",
                                    "4": "SERVER",
                                    "5": "0",
                                    "6": "0",
                                    "7": "2",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "1",
                                    "10": "LGSD",
                                    "11": "SUCCESSFUL_LOGIN_GROUP",
                                    "12": "SERVER",
                                    "13": "NULL",
                                    "14": "NULL",
                                    "15": "NULL",
                                    "16": "Group",
                                    "17": "SUCCESSFUL_LOGIN_GROUP",
                                    "18": "0"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>server_specification_id</th><th>audit_action_id</th><th>audit_action_name</th><th>class</th><th>class_desc</th><th>major_id</th><th>minor_id</th><th>audited_principal_id</th><th>audited_result</th><th>is_group</th><th>action_id</th><th>name</th><th>class_desc</th><th>covering_action_name</th><th>parent_class_desc</th><th>covering_parent_action_name</th><th>configuration_level</th><th>containing_group_name</th><th>action_in_log</th></tr><tr><td>65539</td><td>LGFL</td><td>FAILED_LOGIN_GROUP</td><td>100</td><td>SERVER</td><td>0</td><td>0</td><td>2</td><td>SUCCESS AND FAILURE</td><td>1</td><td>LGFL</td><td>FAILED_LOGIN_GROUP</td><td>SERVER</td><td>NULL</td><td>NULL</td><td>NULL</td><td>Group</td><td>FAILED_LOGIN_GROUP</td><td>0</td></tr><tr><td>65539</td><td>LGSD</td><td>SUCCESSFUL_LOGIN_GROUP</td><td>100</td><td>SERVER</td><td>0</td><td>0</td><td>2</td><td>SUCCESS AND FAILURE</td><td>1</td><td>LGSD</td><td>SUCCESSFUL_LOGIN_GROUP</td><td>SERVER</td><td>NULL</td><td>NULL</td><td>NULL</td><td>Group</td><td>SUCCESSFUL_LOGIN_GROUP</td><td>0</td></tr></table>"
                    }
                }
            ],
            "execution_count": 41
        },
        {
            "cell_type": "markdown",
            "source": "Step 8 - examine database audit specification metadata",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT * FROM sys.database_audit_specifications;\r\n\r\nSELECT *\r\nFROM sys.database_audit_specification_details AS sd\r\nJOIN sys.dm_audit_actions AS aa\r\nON aa.name = sd.audit_action_name COLLATE Latin1_General_CI_AS_KS_WS\r\nAND aa.class_desc = sd.class_desc COLLATE Latin1_General_CI_AS_KS_WS",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(5 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.048"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 42,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "database_specification_id"
                                    },
                                    {
                                        "name": "name"
                                    },
                                    {
                                        "name": "create_date"
                                    },
                                    {
                                        "name": "modify_date"
                                    },
                                    {
                                        "name": "audit_guid"
                                    },
                                    {
                                        "name": "is_state_enabled"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "65540",
                                    "1": "salesapp1_audit_spec",
                                    "2": "2019-07-01 16:07:54.680",
                                    "3": "2019-07-01 16:07:54.680",
                                    "4": "415d2580-f4ab-4091-82e2-60b3518b93f4",
                                    "5": "1"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>database_specification_id</th><th>name</th><th>create_date</th><th>modify_date</th><th>audit_guid</th><th>is_state_enabled</th></tr><tr><td>65540</td><td>salesapp1_audit_spec</td><td>2019-07-01 16:07:54.680</td><td>2019-07-01 16:07:54.680</td><td>415d2580-f4ab-4091-82e2-60b3518b93f4</td><td>1</td></tr></table>"
                    }
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 42,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "database_specification_id"
                                    },
                                    {
                                        "name": "audit_action_id"
                                    },
                                    {
                                        "name": "audit_action_name"
                                    },
                                    {
                                        "name": "class"
                                    },
                                    {
                                        "name": "class_desc"
                                    },
                                    {
                                        "name": "major_id"
                                    },
                                    {
                                        "name": "minor_id"
                                    },
                                    {
                                        "name": "audited_principal_id"
                                    },
                                    {
                                        "name": "audited_result"
                                    },
                                    {
                                        "name": "is_group"
                                    },
                                    {
                                        "name": "action_id"
                                    },
                                    {
                                        "name": "name"
                                    },
                                    {
                                        "name": "class_desc"
                                    },
                                    {
                                        "name": "covering_action_name"
                                    },
                                    {
                                        "name": "parent_class_desc"
                                    },
                                    {
                                        "name": "covering_parent_action_name"
                                    },
                                    {
                                        "name": "configuration_level"
                                    },
                                    {
                                        "name": "containing_group_name"
                                    },
                                    {
                                        "name": "action_in_log"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "65540",
                                    "1": "GRO ",
                                    "2": "SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP",
                                    "3": "0",
                                    "4": "DATABASE",
                                    "5": "0",
                                    "6": "0",
                                    "7": "0",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "1",
                                    "10": "GRO ",
                                    "11": "SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP",
                                    "12": "DATABASE",
                                    "13": "NULL",
                                    "14": "SERVER",
                                    "15": "SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP",
                                    "16": "Group",
                                    "17": "SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP",
                                    "18": "0"
                                },
                                {
                                    "0": "65540",
                                    "1": "IN  ",
                                    "2": "INSERT",
                                    "3": "0",
                                    "4": "DATABASE",
                                    "5": "0",
                                    "6": "0",
                                    "7": "0",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "0",
                                    "10": "IN  ",
                                    "11": "INSERT",
                                    "12": "DATABASE",
                                    "13": "SCHEMA_OBJECT_ACCESS_GROUP",
                                    "14": "SERVER",
                                    "15": "NULL",
                                    "16": "Action",
                                    "17": "SCHEMA_OBJECT_ACCESS_GROUP",
                                    "18": "0"
                                },
                                {
                                    "0": "65540",
                                    "1": "MNO ",
                                    "2": "SCHEMA_OBJECT_CHANGE_GROUP",
                                    "3": "0",
                                    "4": "DATABASE",
                                    "5": "0",
                                    "6": "0",
                                    "7": "0",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "1",
                                    "10": "MNO ",
                                    "11": "SCHEMA_OBJECT_CHANGE_GROUP",
                                    "12": "DATABASE",
                                    "13": "NULL",
                                    "14": "SERVER",
                                    "15": "SCHEMA_OBJECT_CHANGE_GROUP",
                                    "16": "Group",
                                    "17": "SCHEMA_OBJECT_CHANGE_GROUP",
                                    "18": "0"
                                },
                                {
                                    "0": "65540",
                                    "1": "UP  ",
                                    "2": "UPDATE",
                                    "3": "0",
                                    "4": "DATABASE",
                                    "5": "0",
                                    "6": "0",
                                    "7": "0",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "0",
                                    "10": "UP  ",
                                    "11": "UPDATE",
                                    "12": "DATABASE",
                                    "13": "SCHEMA_OBJECT_ACCESS_GROUP",
                                    "14": "SERVER",
                                    "15": "NULL",
                                    "16": "Action",
                                    "17": "SCHEMA_OBJECT_ACCESS_GROUP",
                                    "18": "0"
                                },
                                {
                                    "0": "65540",
                                    "1": "SL  ",
                                    "2": "SELECT",
                                    "3": "3",
                                    "4": "SCHEMA",
                                    "5": "5",
                                    "6": "0",
                                    "7": "0",
                                    "8": "SUCCESS AND FAILURE",
                                    "9": "0",
                                    "10": "SL  ",
                                    "11": "SELECT",
                                    "12": "SCHEMA",
                                    "13": "NULL",
                                    "14": "DATABASE",
                                    "15": "SELECT",
                                    "16": "Action",
                                    "17": "SCHEMA_OBJECT_ACCESS_GROUP",
                                    "18": "0"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>database_specification_id</th><th>audit_action_id</th><th>audit_action_name</th><th>class</th><th>class_desc</th><th>major_id</th><th>minor_id</th><th>audited_principal_id</th><th>audited_result</th><th>is_group</th><th>action_id</th><th>name</th><th>class_desc</th><th>covering_action_name</th><th>parent_class_desc</th><th>covering_parent_action_name</th><th>configuration_level</th><th>containing_group_name</th><th>action_in_log</th></tr><tr><td>65540</td><td>GRO </td><td>SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP</td><td>0</td><td>DATABASE</td><td>0</td><td>0</td><td>0</td><td>SUCCESS AND FAILURE</td><td>1</td><td>GRO </td><td>SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP</td><td>DATABASE</td><td>NULL</td><td>SERVER</td><td>SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP</td><td>Group</td><td>SCHEMA_OBJECT_PERMISSION_CHANGE_GROUP</td><td>0</td></tr><tr><td>65540</td><td>IN  </td><td>INSERT</td><td>0</td><td>DATABASE</td><td>0</td><td>0</td><td>0</td><td>SUCCESS AND FAILURE</td><td>0</td><td>IN  </td><td>INSERT</td><td>DATABASE</td><td>SCHEMA_OBJECT_ACCESS_GROUP</td><td>SERVER</td><td>NULL</td><td>Action</td><td>SCHEMA_OBJECT_ACCESS_GROUP</td><td>0</td></tr><tr><td>65540</td><td>MNO </td><td>SCHEMA_OBJECT_CHANGE_GROUP</td><td>0</td><td>DATABASE</td><td>0</td><td>0</td><td>0</td><td>SUCCESS AND FAILURE</td><td>1</td><td>MNO </td><td>SCHEMA_OBJECT_CHANGE_GROUP</td><td>DATABASE</td><td>NULL</td><td>SERVER</td><td>SCHEMA_OBJECT_CHANGE_GROUP</td><td>Group</td><td>SCHEMA_OBJECT_CHANGE_GROUP</td><td>0</td></tr><tr><td>65540</td><td>UP  </td><td>UPDATE</td><td>0</td><td>DATABASE</td><td>0</td><td>0</td><td>0</td><td>SUCCESS AND FAILURE</td><td>0</td><td>UP  </td><td>UPDATE</td><td>DATABASE</td><td>SCHEMA_OBJECT_ACCESS_GROUP</td><td>SERVER</td><td>NULL</td><td>Action</td><td>SCHEMA_OBJECT_ACCESS_GROUP</td><td>0</td></tr><tr><td>65540</td><td>SL  </td><td>SELECT</td><td>3</td><td>SCHEMA</td><td>5</td><td>0</td><td>0</td><td>SUCCESS AND FAILURE</td><td>0</td><td>SL  </td><td>SELECT</td><td>SCHEMA</td><td>NULL</td><td>DATABASE</td><td>SELECT</td><td>Action</td><td>SCHEMA_OBJECT_ACCESS_GROUP</td><td>0</td></tr></table>"
                    }
                }
            ],
            "execution_count": 42
        },
        {
            "cell_type": "markdown",
            "source": "Step 9 - remove the audit ",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE master;\r\nGO\r\nALTER SERVER AUDIT MIASQL_Audit WITH (STATE = OFF);\r\nDROP SERVER AUDIT MIASQL_Audit;\r\nGO\r\n\r\nALTER SERVER AUDIT SPECIFICATION AuditLogins WITH (STATE = OFF);\r\nDROP SERVER AUDIT SPECIFICATION AuditLogins\r\nGO\r\n\r\nUSE salesapp1;\r\nGO\r\nALTER DATABASE AUDIT SPECIFICATION salesapp1_audit_spec WITH (STATE = OFF);\r\nDROP DATABASE AUDIT SPECIFICATION salesapp1_audit_spec\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.055"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 43
        },
        {
            "cell_type": "markdown",
            "source": "<h2>Ejercicio N° 03: Utilizando auditorías personzalizadas</h2>",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "Step 1 - create an audit",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE master;\r\nGO\r\nCREATE SERVER AUDIT Custom_Audit \r\n    TO FILE (FILEPATH='F:\\Data\\')\r\n    WITH (QUEUE_DELAY = 5000);\r\nGO\r\nALTER SERVER AUDIT Custom_Audit WITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.005"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 45
        },
        {
            "cell_type": "markdown",
            "source": "Step 2 - create a server audit specification which includes the USER_DEFINED_AUDIT_GROUP action group",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "CREATE SERVER AUDIT SPECIFICATION UserDefinedEvents\r\nFOR SERVER AUDIT Custom_Audit\r\nADD (USER_DEFINED_AUDIT_GROUP)\r\nWITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.003"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 46
        },
        {
            "cell_type": "markdown",
            "source": "Step 3 - call sp_audit_write directly",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "EXEC sp_audit_write @user_defined_event_id = 999, \r\n                    @succeeded = 1,\r\n                    @user_defined_information = N'Example call to sp_audit_write';",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.001"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 47
        },
        {
            "cell_type": "markdown",
            "source": "Step 4 - demonstrate how a custom event appears in the audit ",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT user_defined_event_id, succeeded, user_defined_information\r\nFROM sys.fn_get_audit_file ('F:\\Data\\Custom_Audit*',default,default)\r\nWHERE user_defined_event_id = 999;\r\n",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.074"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 50,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "user_defined_event_id"
                                    },
                                    {
                                        "name": "succeeded"
                                    },
                                    {
                                        "name": "user_defined_information"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "999",
                                    "1": "1",
                                    "2": "Example call to sp_audit_write"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>user_defined_event_id</th><th>succeeded</th><th>user_defined_information</th></tr><tr><td>999</td><td>1</td><td>Example call to sp_audit_write</td></tr></table>"
                    }
                }
            ],
            "execution_count": 50
        },
        {
            "cell_type": "markdown",
            "source": "Step 5 - demonstrate the use of sp_audit_write in a stored procedure",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE salesapp1;\r\nGO\r\n\r\nCREATE PROC usp_OrderDetailDiscount\r\n\t@orderid int,\r\n\t@productid int,\r\n\t@discount numeric(4,3)\r\nAS\r\n\tSET NOCOUNT ON\r\n\r\n\tIF @discount > 0.3\r\n\tBEGIN\r\n\t\tDECLARE @msg nvarchar(4000) = \r\n\t\t  CONCAT('Order=',@orderid,':Product=',@productid,\r\n\t\t         ':discount=', @discount)\r\n\r\n\t\t\r\n\t\tEXEC sp_audit_write @user_defined_event_id = 998, \r\n\t\t\t\t            @succeeded = 1,\r\n\t\t\t\t\t\t    @user_defined_information = @msg;\r\n\tEND\r\n\r\n\tUPDATE Sales.OrderDetails\r\n\tSET discount = @discount\r\n\tWHERE orderid = @orderid\r\n\tAND productid = @productid\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.006"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 51
        },
        {
            "cell_type": "markdown",
            "source": "Step 6 - call the stored procedure twice the first call should not generate a custom audit event the second call should generate a custom audit event",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "EXEC dbo.usp_OrderDetailDiscount @orderid = 10248,@productid =\t11, @discount = 0.05\r\nEXEC dbo.usp_OrderDetailDiscount @orderid = 10248,@productid =\t42, @discount = 0.45",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.013"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 52
        },
        {
            "cell_type": "markdown",
            "source": "Step 7 - examine the audit data",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT user_defined_event_id, succeeded, user_defined_information\r\nFROM sys.fn_get_audit_file ('F:\\Data\\Custom_Audit*',default,default)\r\nWHERE user_defined_event_id = 998;",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(1 row affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.046"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 53,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "user_defined_event_id"
                                    },
                                    {
                                        "name": "succeeded"
                                    },
                                    {
                                        "name": "user_defined_information"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "998",
                                    "1": "1",
                                    "2": "Order=10248:Product=42:discount=0.450"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>user_defined_event_id</th><th>succeeded</th><th>user_defined_information</th></tr><tr><td>998</td><td>1</td><td>Order=10248:Product=42:discount=0.450</td></tr></table>"
                    }
                }
            ],
            "execution_count": 53
        },
        {
            "cell_type": "markdown",
            "source": "Step 8 - drop the audit",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE master;\r\nGO\r\nALTER SERVER AUDIT Custom_Audit WITH (STATE = OFF);\r\nDROP SERVER AUDIT Custom_Audit;\r\nGO\r\n\r\nALTER SERVER AUDIT SPECIFICATION UserDefinedEvents WITH (STATE = OFF);\r\nDROP SERVER AUDIT SPECIFICATION UserDefinedEvents\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.060"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 54
        },
        {
            "cell_type": "markdown",
            "source": "<h2>Ejercicio N° 04: Administrando auditorías</h2>",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "Step 1 - create an audit with a file target",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE master;\r\nGO\r\nCREATE SERVER AUDIT File_Audit \r\n    TO FILE (FILEPATH='F:\\Data\\')\r\n    WITH (QUEUE_DELAY = 5000);\r\nGO\r\nALTER SERVER AUDIT File_Audit WITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.007"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 55
        },
        {
            "cell_type": "markdown",
            "source": "Step 2 - create an audit with a Windows application log target",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "CREATE SERVER AUDIT AppLog_Audit \r\n    TO APPLICATION_LOG\r\n    WITH (QUEUE_DELAY = 5000);\r\nGO\r\nALTER SERVER AUDIT AppLog_Audit WITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.003"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 56
        },
        {
            "cell_type": "markdown",
            "source": "Step 3 - add the same database audit specification to both audits",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "USE salesapp1;\r\nGO\r\nCREATE DATABASE AUDIT SPECIFICATION sales_select_spec_file\r\nFOR SERVER AUDIT File_Audit\r\nADD (SELECT ON SCHEMA::Sales BY public)\r\nWITH (STATE = ON);\r\nGO\r\n\r\nCREATE DATABASE AUDIT SPECIFICATION sales_select_spec_applog\r\nFOR SERVER AUDIT AppLog_Audit\r\nADD (SELECT ON SCHEMA::Sales BY public)\r\nWITH (STATE = ON);\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.003"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 57
        },
        {
            "cell_type": "markdown",
            "source": "Step 4 - execute a simple select statement which matches the audit specification",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT TOP 10 * FROM Sales.Customers;\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(10 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.014"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 58,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "custid"
                                    },
                                    {
                                        "name": "companyname"
                                    },
                                    {
                                        "name": "contactname"
                                    },
                                    {
                                        "name": "contacttitle"
                                    },
                                    {
                                        "name": "address"
                                    },
                                    {
                                        "name": "city"
                                    },
                                    {
                                        "name": "region"
                                    },
                                    {
                                        "name": "postalcode"
                                    },
                                    {
                                        "name": "country"
                                    },
                                    {
                                        "name": "phone"
                                    },
                                    {
                                        "name": "fax"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "1",
                                    "1": "Customer NRZBB",
                                    "2": "Allen, Michael",
                                    "3": "Sales Representative",
                                    "4": "Obere Str. 0123",
                                    "5": "Berlin",
                                    "6": "NULL",
                                    "7": "10092",
                                    "8": "Germany",
                                    "9": "030-3456789",
                                    "10": "030-0123456"
                                },
                                {
                                    "0": "2",
                                    "1": "Customer MLTDN",
                                    "2": "Hassall, Mark",
                                    "3": "Owner",
                                    "4": "Avda. de la Constitución 5678",
                                    "5": "México D.F.",
                                    "6": "NULL",
                                    "7": "10077",
                                    "8": "Mexico",
                                    "9": "(5) 789-0123",
                                    "10": "(5) 456-7890"
                                },
                                {
                                    "0": "3",
                                    "1": "Customer KBUDE",
                                    "2": "Peoples, John",
                                    "3": "Owner",
                                    "4": "Mataderos  7890",
                                    "5": "México D.F.",
                                    "6": "NULL",
                                    "7": "10097",
                                    "8": "Mexico",
                                    "9": "(5) 123-4567",
                                    "10": "NULL"
                                },
                                {
                                    "0": "4",
                                    "1": "Customer HFBZG",
                                    "2": "Arndt, Torsten",
                                    "3": "Sales Representative",
                                    "4": "7890 Hanover Sq.",
                                    "5": "London",
                                    "6": "NULL",
                                    "7": "10046",
                                    "8": "UK",
                                    "9": "(171) 456-7890",
                                    "10": "(171) 456-7891"
                                },
                                {
                                    "0": "5",
                                    "1": "Customer HGVLZ",
                                    "2": "Higginbotham, Tom",
                                    "3": "Order Administrator",
                                    "4": "Berguvsvägen  5678",
                                    "5": "Luleå",
                                    "6": "NULL",
                                    "7": "10112",
                                    "8": "Sweden",
                                    "9": "0921-67 89 01",
                                    "10": "0921-23 45 67"
                                },
                                {
                                    "0": "6",
                                    "1": "Customer XHXJV",
                                    "2": "Poland, Carole",
                                    "3": "Sales Representative",
                                    "4": "Forsterstr. 7890",
                                    "5": "Mannheim",
                                    "6": "NULL",
                                    "7": "10117",
                                    "8": "Germany",
                                    "9": "0621-67890",
                                    "10": "0621-12345"
                                },
                                {
                                    "0": "7",
                                    "1": "Customer QXVLA",
                                    "2": "Bansal, Dushyant",
                                    "3": "Marketing Manager",
                                    "4": "2345, place Kléber",
                                    "5": "Strasbourg",
                                    "6": "NULL",
                                    "7": "10089",
                                    "8": "France",
                                    "9": "67.89.01.23",
                                    "10": "67.89.01.24"
                                },
                                {
                                    "0": "8",
                                    "1": "Customer QUHWH",
                                    "2": "Ilyina, Julia",
                                    "3": "Owner",
                                    "4": "C/ Araquil, 0123",
                                    "5": "Madrid",
                                    "6": "NULL",
                                    "7": "10104",
                                    "8": "Spain",
                                    "9": "(91) 345 67 89",
                                    "10": "(91) 012 34 56"
                                },
                                {
                                    "0": "9",
                                    "1": "Customer RTXGC",
                                    "2": "Raghav, Amritansh",
                                    "3": "Owner",
                                    "4": "6789, rue des Bouchers",
                                    "5": "Marseille",
                                    "6": "NULL",
                                    "7": "10105",
                                    "8": "France",
                                    "9": "23.45.67.89",
                                    "10": "23.45.67.80"
                                },
                                {
                                    "0": "10",
                                    "1": "Customer EEALV",
                                    "2": "Bassols, Pilar Colome",
                                    "3": "Accounting Manager",
                                    "4": "8901 Tsawassen Blvd.",
                                    "5": "Tsawassen",
                                    "6": "BC",
                                    "7": "10111",
                                    "8": "Canada",
                                    "9": "(604) 901-2345",
                                    "10": "(604) 678-9012"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>custid</th><th>companyname</th><th>contactname</th><th>contacttitle</th><th>address</th><th>city</th><th>region</th><th>postalcode</th><th>country</th><th>phone</th><th>fax</th></tr><tr><td>1</td><td>Customer NRZBB</td><td>Allen, Michael</td><td>Sales Representative</td><td>Obere Str. 0123</td><td>Berlin</td><td>NULL</td><td>10092</td><td>Germany</td><td>030-3456789</td><td>030-0123456</td></tr><tr><td>2</td><td>Customer MLTDN</td><td>Hassall, Mark</td><td>Owner</td><td>Avda. de la Constitución 5678</td><td>México D.F.</td><td>NULL</td><td>10077</td><td>Mexico</td><td>(5) 789-0123</td><td>(5) 456-7890</td></tr><tr><td>3</td><td>Customer KBUDE</td><td>Peoples, John</td><td>Owner</td><td>Mataderos  7890</td><td>México D.F.</td><td>NULL</td><td>10097</td><td>Mexico</td><td>(5) 123-4567</td><td>NULL</td></tr><tr><td>4</td><td>Customer HFBZG</td><td>Arndt, Torsten</td><td>Sales Representative</td><td>7890 Hanover Sq.</td><td>London</td><td>NULL</td><td>10046</td><td>UK</td><td>(171) 456-7890</td><td>(171) 456-7891</td></tr><tr><td>5</td><td>Customer HGVLZ</td><td>Higginbotham, Tom</td><td>Order Administrator</td><td>Berguvsvägen  5678</td><td>Luleå</td><td>NULL</td><td>10112</td><td>Sweden</td><td>0921-67 89 01</td><td>0921-23 45 67</td></tr><tr><td>6</td><td>Customer XHXJV</td><td>Poland, Carole</td><td>Sales Representative</td><td>Forsterstr. 7890</td><td>Mannheim</td><td>NULL</td><td>10117</td><td>Germany</td><td>0621-67890</td><td>0621-12345</td></tr><tr><td>7</td><td>Customer QXVLA</td><td>Bansal, Dushyant</td><td>Marketing Manager</td><td>2345, place Kléber</td><td>Strasbourg</td><td>NULL</td><td>10089</td><td>France</td><td>67.89.01.23</td><td>67.89.01.24</td></tr><tr><td>8</td><td>Customer QUHWH</td><td>Ilyina, Julia</td><td>Owner</td><td>C/ Araquil, 0123</td><td>Madrid</td><td>NULL</td><td>10104</td><td>Spain</td><td>(91) 345 67 89</td><td>(91) 012 34 56</td></tr><tr><td>9</td><td>Customer RTXGC</td><td>Raghav, Amritansh</td><td>Owner</td><td>6789, rue des Bouchers</td><td>Marseille</td><td>NULL</td><td>10105</td><td>France</td><td>23.45.67.89</td><td>23.45.67.80</td></tr><tr><td>10</td><td>Customer EEALV</td><td>Bassols, Pilar Colome</td><td>Accounting Manager</td><td>8901 Tsawassen Blvd.</td><td>Tsawassen</td><td>BC</td><td>10111</td><td>Canada</td><td>(604) 901-2345</td><td>(604) 678-9012</td></tr></table>"
                    }
                }
            ],
            "execution_count": 58
        },
        {
            "cell_type": "markdown",
            "source": "Step 5 - examine the file-based audit output. Demonstrate some of the most useful fields",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "SELECT *\r\nFROM sys.fn_get_audit_file ('F:\\Data\\File_Audit*',default,default)",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "(2 rows affected)"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.044"
                    },
                    "metadata": {}
                },
                {
                    "output_type": "execute_result",
                    "metadata": {},
                    "execution_count": 60,
                    "data": {
                        "application/vnd.dataresource+json": {
                            "schema": {
                                "fields": [
                                    {
                                        "name": "event_time"
                                    },
                                    {
                                        "name": "sequence_number"
                                    },
                                    {
                                        "name": "action_id"
                                    },
                                    {
                                        "name": "succeeded"
                                    },
                                    {
                                        "name": "permission_bitmask"
                                    },
                                    {
                                        "name": "is_column_permission"
                                    },
                                    {
                                        "name": "session_id"
                                    },
                                    {
                                        "name": "server_principal_id"
                                    },
                                    {
                                        "name": "database_principal_id"
                                    },
                                    {
                                        "name": "target_server_principal_id"
                                    },
                                    {
                                        "name": "target_database_principal_id"
                                    },
                                    {
                                        "name": "object_id"
                                    },
                                    {
                                        "name": "class_type"
                                    },
                                    {
                                        "name": "session_server_principal_name"
                                    },
                                    {
                                        "name": "server_principal_name"
                                    },
                                    {
                                        "name": "server_principal_sid"
                                    },
                                    {
                                        "name": "database_principal_name"
                                    },
                                    {
                                        "name": "target_server_principal_name"
                                    },
                                    {
                                        "name": "target_server_principal_sid"
                                    },
                                    {
                                        "name": "target_database_principal_name"
                                    },
                                    {
                                        "name": "server_instance_name"
                                    },
                                    {
                                        "name": "database_name"
                                    },
                                    {
                                        "name": "schema_name"
                                    },
                                    {
                                        "name": "object_name"
                                    },
                                    {
                                        "name": "statement"
                                    },
                                    {
                                        "name": "additional_information"
                                    },
                                    {
                                        "name": "file_name"
                                    },
                                    {
                                        "name": "audit_file_offset"
                                    },
                                    {
                                        "name": "user_defined_event_id"
                                    },
                                    {
                                        "name": "user_defined_information"
                                    },
                                    {
                                        "name": "audit_schema_version"
                                    },
                                    {
                                        "name": "sequence_group_id"
                                    },
                                    {
                                        "name": "transaction_id"
                                    },
                                    {
                                        "name": "client_ip"
                                    },
                                    {
                                        "name": "application_name"
                                    },
                                    {
                                        "name": "duration_milliseconds"
                                    },
                                    {
                                        "name": "response_rows"
                                    },
                                    {
                                        "name": "affected_rows"
                                    }
                                ]
                            },
                            "data": [
                                {
                                    "0": "2019-07-01 21:27:52.6889285",
                                    "1": "1",
                                    "2": "AUSC",
                                    "3": "1",
                                    "4": "0x00000000000000000000000000000000",
                                    "5": "0",
                                    "6": "51",
                                    "7": "259",
                                    "8": "0",
                                    "9": "0",
                                    "10": "0",
                                    "11": "0",
                                    "12": "A ",
                                    "13": "CMACT\\pcuadros_dev",
                                    "14": "CMACT\\pcuadros_dev",
                                    "15": "0x010500000000000515000000BC6001619635B771C9F611C1713E0000",
                                    "16": "",
                                    "17": "",
                                    "18": "NULL",
                                    "19": "",
                                    "20": "TID-01NS3-107",
                                    "21": "",
                                    "22": "",
                                    "23": "",
                                    "24": "",
                                    "25": "<action_info xmlns=\"http://schemas.microsoft.com/sqlserver/2008/sqlaudit_data\"><session><![CDATA[File_Audit$A]]></session><action>event enabled</action><startup_type>manual</startup_type><object><![CDATA[audit_event]]></object></action_info>",
                                    "26": "F:\\Data\\File_Audit_F2D34EF6-0C81-45E8-B683-983064C683A7_0_132064900726860000.sqlaudit",
                                    "27": "5632",
                                    "28": "0",
                                    "29": "",
                                    "30": "1",
                                    "31": "0x00000000000000000000000000000000",
                                    "32": "0",
                                    "33": "local machine",
                                    "34": "",
                                    "35": "0",
                                    "36": "0",
                                    "37": "0"
                                },
                                {
                                    "0": "2019-07-01 21:29:12.5533939",
                                    "1": "1",
                                    "2": "SL  ",
                                    "3": "1",
                                    "4": "0x00000000000000000000000000000001",
                                    "5": "1",
                                    "6": "51",
                                    "7": "259",
                                    "8": "1",
                                    "9": "0",
                                    "10": "0",
                                    "11": "485576768",
                                    "12": "U ",
                                    "13": "CMACT\\pcuadros_dev",
                                    "14": "CMACT\\pcuadros_dev",
                                    "15": "0x010500000000000515000000BC6001619635B771C9F611C1713E0000",
                                    "16": "dbo",
                                    "17": "",
                                    "18": "NULL",
                                    "19": "",
                                    "20": "TID-01NS3-107",
                                    "21": "salesapp1",
                                    "22": "Sales",
                                    "23": "Customers",
                                    "24": "SELECT TOP 10 * FROM Sales.Customers",
                                    "25": "",
                                    "26": "F:\\Data\\File_Audit_F2D34EF6-0C81-45E8-B683-983064C683A7_0_132064900726860000.sqlaudit",
                                    "27": "6656",
                                    "28": "0",
                                    "29": "",
                                    "30": "1",
                                    "31": "0x0BB719ACE1A30E4CBEC1773B431A9946",
                                    "32": "141251",
                                    "33": "local machine",
                                    "34": "azdata-Query",
                                    "35": "0",
                                    "36": "0",
                                    "37": "0"
                                }
                            ]
                        },
                        "text/html": "<table><tr><th>event_time</th><th>sequence_number</th><th>action_id</th><th>succeeded</th><th>permission_bitmask</th><th>is_column_permission</th><th>session_id</th><th>server_principal_id</th><th>database_principal_id</th><th>target_server_principal_id</th><th>target_database_principal_id</th><th>object_id</th><th>class_type</th><th>session_server_principal_name</th><th>server_principal_name</th><th>server_principal_sid</th><th>database_principal_name</th><th>target_server_principal_name</th><th>target_server_principal_sid</th><th>target_database_principal_name</th><th>server_instance_name</th><th>database_name</th><th>schema_name</th><th>object_name</th><th>statement</th><th>additional_information</th><th>file_name</th><th>audit_file_offset</th><th>user_defined_event_id</th><th>user_defined_information</th><th>audit_schema_version</th><th>sequence_group_id</th><th>transaction_id</th><th>client_ip</th><th>application_name</th><th>duration_milliseconds</th><th>response_rows</th><th>affected_rows</th></tr><tr><td>2019-07-01 21:27:52.6889285</td><td>1</td><td>AUSC</td><td>1</td><td>0x00000000000000000000000000000000</td><td>0</td><td>51</td><td>259</td><td>0</td><td>0</td><td>0</td><td>0</td><td>A </td><td>CMACT\\pcuadros_dev</td><td>CMACT\\pcuadros_dev</td><td>0x010500000000000515000000BC6001619635B771C9F611C1713E0000</td><td></td><td></td><td>NULL</td><td></td><td>TID-01NS3-107</td><td></td><td></td><td></td><td></td><td>&lt;action_info xmlns=&quot;http://schemas.microsoft.com/sqlserver/2008/sqlaudit_data&quot;&gt;&lt;session&gt;&lt;![CDATA[File_Audit$A]]&gt;&lt;/session&gt;&lt;action&gt;event enabled&lt;/action&gt;&lt;startup_type&gt;manual&lt;/startup_type&gt;&lt;object&gt;&lt;![CDATA[audit_event]]&gt;&lt;/object&gt;&lt;/action_info&gt;</td><td>F:\\Data\\File_Audit_F2D34EF6-0C81-45E8-B683-983064C683A7_0_132064900726860000.sqlaudit</td><td>5632</td><td>0</td><td></td><td>1</td><td>0x00000000000000000000000000000000</td><td>0</td><td>local machine</td><td></td><td>0</td><td>0</td><td>0</td></tr><tr><td>2019-07-01 21:29:12.5533939</td><td>1</td><td>SL  </td><td>1</td><td>0x00000000000000000000000000000001</td><td>1</td><td>51</td><td>259</td><td>1</td><td>0</td><td>0</td><td>485576768</td><td>U </td><td>CMACT\\pcuadros_dev</td><td>CMACT\\pcuadros_dev</td><td>0x010500000000000515000000BC6001619635B771C9F611C1713E0000</td><td>dbo</td><td></td><td>NULL</td><td></td><td>TID-01NS3-107</td><td>salesapp1</td><td>Sales</td><td>Customers</td><td>SELECT TOP 10 * FROM Sales.Customers</td><td></td><td>F:\\Data\\File_Audit_F2D34EF6-0C81-45E8-B683-983064C683A7_0_132064900726860000.sqlaudit</td><td>6656</td><td>0</td><td></td><td>1</td><td>0x0BB719ACE1A30E4CBEC1773B431A9946</td><td>141251</td><td>local machine</td><td>azdata-Query</td><td>0</td><td>0</td><td>0</td></tr></table>"
                    }
                }
            ],
            "execution_count": 60
        },
        {
            "cell_type": "markdown",
            "source": "Step 6 - examin the Windows application log audit output. Use Event Viewer",
            "metadata": {}
        },
        {
            "cell_type": "markdown",
            "source": "Step 7 - drop audits and audit specifications, notice that the audits and specifications must be disabled before they can be dropped",
            "metadata": {}
        },
        {
            "cell_type": "code",
            "source": "ALTER DATABASE AUDIT SPECIFICATION sales_select_spec_applog WITH (STATE = OFF);\r\nDROP DATABASE AUDIT SPECIFICATION sales_select_spec_applog;\r\nALTER DATABASE AUDIT SPECIFICATION sales_select_spec_file WITH (STATE = OFF);\r\nDROP DATABASE AUDIT SPECIFICATION sales_select_spec_file;\r\nGO\r\nUSE master;\r\nALTER SERVER AUDIT AppLog_Audit WITH (STATE = OFF);\r\nDROP SERVER AUDIT AppLog_Audit;\r\nALTER SERVER AUDIT File_Audit WITH (STATE = OFF);\r\nDROP SERVER AUDIT File_Audit;\r\nGO",
            "metadata": {},
            "outputs": [
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Commands completed successfully."
                    },
                    "metadata": {}
                },
                {
                    "output_type": "display_data",
                    "data": {
                        "text/html": "Total execution time: 00:00:00.050"
                    },
                    "metadata": {}
                }
            ],
            "execution_count": 0
        }
    ]
}
