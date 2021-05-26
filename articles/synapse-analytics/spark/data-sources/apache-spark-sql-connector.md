---
title: Azure SQL et SQL Server
description: Cet article explique comment utiliser le connecteur pour déplacer des données entre Azure MS SQL et des pools Apache Spark serverless.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 05/19/2020
ms.openlocfilehash: 82518e6c8f4901ffe6c5503d902359a79c12e0b0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110081673"
---
# <a name="azure-sql-database-and-sql-server-connector-for-apache-spark"></a>Connecteur Azure SQL Database et SQL Server pour Apache Spark
Le connecteur Apache Spark pour Azure SQL Database et SQL Server permet à ces bases de données de faire office de sources de données d’entrée et de générer des récepteurs de données en sortie pour les travaux Apache Spark. Il vous permet d’utiliser des données transactionnelles en temps réel dans l’analytique du Big Data et de conserver les résultats pour des requêtes ad hoc ou des rapports.

Par rapport au connecteur JDBC intégré, ce connecteur offre la possibilité d’insérer des données en bloc dans des bases de données SQL. Il peut donner de meilleurs résultats que l’insertion ligne par ligne et atteindre des performances entre 10 et 20 fois plus rapides. Le connecteur Spark pour SQL Server et Azure SQL Database prend également en charge l’[authentification](/sql/connect/spark/connector#azure-active-directory-authentication) Azure Active Directory (Azure AD), ce qui vous permet de vous connecter en toute sécurité à vos bases de données Azure SQL à partir d’Azure Synapse Analytics. 

Cet article explique comment utiliser l’API DataFrame pour se connecter à des bases de données SQL à l’aide du connecteur MS SQL. Cet article fournit des exemples détaillés utilisant l’API PySpark. Pour obtenir tous les exemples et arguments pris en charge pour la connexion aux bases de données SQL à l’aide du connecteur MS SQL, consultez [Exemples SQL Azure Data](https://github.com/microsoft/sql-server-samples#azure-data-sql-samples-repository).


  
## <a name="connection-details"></a>Détails de la connexion
Dans cet exemple, nous allons utiliser les utilitaires Microsoft Spark pour faciliter l’acquisition de secrets à partir d’un coffre de clés préconfiguré. Pour en savoir plus sur les utilitaires Microsoft Spark, consultez [Présentation des utilitaires Microsoft Spark](../microsoft-spark-utilities.md).

```python
servername = "<< server name >>"
dbname = "<< database name >>"
url = servername + ";" + "databaseName=" + dbname + ";"
dbtable = "<< table name >> "
user = "<< username >>" 
password = mssparkutils.credentials.getSecret('azure key vault name','secret name')
```

> [!NOTE]
> Actuellement, il n’existe pas de prise en charge du transfert AAD ni de service lié avec le connecteur Azure SQL.

## <a name="use-the-azure-sql-and-sql-server-connector"></a>Utiliser le connecteur Azure SQL et SQL Server

### <a name="read-data"></a>Lire les données
```python
#Read from SQL table using MS SQL Connector
print("read data from SQL server table  ")
jdbcDF = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("user", user) \
        .option("password", password).load()

jdbcDF.show(5)
```

### <a name="write-data"></a>Écrire des données
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("overwrite") \
    .option("url", url) \
    .option("dbtable", dbtable) \
    .option("user", user) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("MSSQL Connector write failed", error)

print("MSSQL Connector write(overwrite) succeeded  ")
```
### <a name="append-data"></a>Ajout de données
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("append") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

## <a name="azure-active-directory-authentication"></a>Authentification Azure Active Directory

### <a name="python-example-with-service-principal"></a>Exemple Python avec un principal de service
```python
import adal

# Located in App Registrations from Azure Portal
tenant_id = "<< tenant id >> "

# Located in App Registrations from Azure Portal
resource_app_id_url = "https://database.windows.net/"

# Authority
authority = "https://login.windows.net/" + tenant_id

context = adal.AuthenticationContext(authority)
token = context.acquire_token_with_client_credentials(resource_app_id_url, service_principal_id, service_principal_secret)
access_token = token["accessToken"]

jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("accessToken", access_token) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

### <a name="python-example-with-active-directory-password"></a>Exemple Python avec mot de passe Active Directory
```python
jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("authentication", "ActiveDirectoryPassword") \
        .option("user", user_name) \
        .option("password", password) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur le connecteur SQL Server et Azure SQL](/sql/connect/spark/connector)
- [Afficher des exemples SQL Azure Data](https://github.com/microsoft/sql-server-samples)
