---
title: Se connecter à Azure SQL Data Warehouse
description: Connectez-vous à Azure SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 72825f588ff92383858020cdbcd92c7de3078ed5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350660"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Se connecter à Azure SQL Data Warehouse
Connectez-vous à Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Recherche du nom de serveur
Le nom du serveur dans l’exemple suivant est samplesvr.database.windows.net. Pour rechercher le nom complet du serveur :

1. Accédez au [portail Azure][Azure portal].
2. Cliquez sur **Entrepôts de données SQL**.
3. Cliquez sur l’entrepôt de données auquel vous souhaitez vous connecter.
4. Recherchez le nom complet du serveur.
   
    ![Nom complet du serveur](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Chaînes de connexion et pilotes pris en charge
Azure SQL Data Warehouse prend en charge [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] et [JDBC][JDBC]. Pour rechercher la dernière version et accéder à la documentation connexe, cliquez sur l’un des pilotes ci-dessus. Pour générer automatiquement la chaîne de connexion pour le pilote que vous utilisez à partir du portail Azure, cliquez sur l’option **Afficher les chaînes de connexion de la base de données** dans l’exemple précédent. Voici également quelques exemples montrant à quoi ressemble une chaîne de connexion pour chaque pilote.

> [!NOTE]
> Vous pouvez définir le délai de connexion à 300 secondes pour permettre à votre connexion de résister à des courtes périodes d’indisponibilité.
> 
> 

### <a name="adonet-connection-string-example"></a>Exemple de chaîne de connexion ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemple de chaîne de connexion ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemple de chaîne de connexion PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemple de chaîne de connexion JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Paramètres de connexion
SQL Data Warehouse standardise certains paramètres pendant la connexion et la création d’objets. Ces paramètres, qui ne peuvent pas être remplacés, comprennent notamment :

| Paramètre de base de données | Valeur |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ACTIVÉ |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ACTIVÉ |
| [DATEFORMAT][DATEFORMAT] |mja |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Étapes suivantes
Pour vous connecter et interroger avec Visual Studio, consultez la page [Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)][Query with Visual Studio]. Pour en savoir plus sur les options d’authentification, consultez la page [Authentification sur Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]:sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]:sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


