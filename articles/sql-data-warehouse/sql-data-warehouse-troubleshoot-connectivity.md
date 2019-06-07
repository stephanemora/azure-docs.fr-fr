---
title: Résolution des problèmes d’Azure SQL Data Warehouse | Microsoft Docs
description: Résolution des problèmes d’Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 3f25d2ab5b2b988725d8f4fdf942854746f404d1
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754849"
---
# <a name="troubleshooting-connectivity-issues"></a>Résolution des problèmes de connectivité

Cet article répertorie les techniques de dépannage courantes autour de la connexion à votre SQL Data Warehouse.
- [Vérifier la disponibilité du service](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Vérification pour l’opération de mise à l’échelle ou en pause](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Vérifiez vos paramètres de pare-feu](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Vérifiez vos paramètres de point de terminaison de Service/réseau virtuel](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Recherchez les derniers pilotes](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Vérifiez votre chaîne de connexion](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problèmes de connexion intermittents](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Messages d’erreur courants](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Vérifier la disponibilité du service

Vérifiez si le service est disponible. Dans le portail Azure, accédez à l’entrepôt de données SQL que vous tentez de vous connecter. Dans le volet gauche de la table des matières, cliquez sur **diagnostiquer et résoudre les problèmes**.

![Sélectionnez l’intégrité des ressources](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

L’état de votre entrepôt de données SQL s’affiche ici. Si le service n’est pas visible en tant que **disponible**, vérifier d’autres étapes.

![Disponibilité du service](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Si l’intégrité de votre ressource indique que votre entrepôt de données est interrompue ou mise à l’échelle, suivez les instructions pour reprendre votre entrepôt de données.

![Service suspendu](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) trouverez ici des informations supplémentaires sur l’intégrité des ressources.

## <a name="check-for-paused-or-scaling-operation"></a>Vérification pour l’opération de mise à l’échelle ou en pause

Vérifiez le portail pour voir si votre entrepôt de données SQL est en pause ou la mise à l’échelle.

![Service suspendu](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Si vous constatez que votre service est en pause ou la mise à l’échelle, vérifiez qu’il n’est pas au cours de votre planification de maintenance. Sur le portail de votre entrepôt de données SQL *vue d’ensemble*, vous verrez la planification de maintenance sélectionnés.

![Planification de Maintenance de vue d’ensemble](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Sinon, contactez votre administrateur informatique pour vérifier que cette opération de maintenance n’est pas un événement planifié. Pour reprendre l’entrepôt de données SQL, suivez les étapes décrites [ici](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Vérifiez vos paramètres de pare-feu

SQL Data Warehouse communique sur le port 1433.   Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne peut pas se connecter à votre serveur de base de données SQL Azure, sauf si votre service informatique ouvre le port 1433. Vous pouvez trouver plus d’informations sur les configurations de pare-feu [ici](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Vérifiez vos paramètres de point de terminaison de Service/réseau virtuel

Si vous recevez des erreurs 40914 et 40615, consultez [description d’erreur et de résolution ici](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Recherchez les derniers pilotes

### <a name="software"></a>Logiciel

Vérifiez que vous utilisez les derniers outils pour vous connecter à votre SQL data warehouse :

* SSMS
* Studio de données Azure
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Pilotes

Vérifiez que vous utilisez les dernières versions de pilote.  À l’aide d’une version antérieure des pilotes peut entraîner des comportements inattendus car les anciens pilotes ne peuvent pas en charge des nouvelles fonctionnalités.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Vérifiez votre chaîne de connexion

Vérifiez que vos chaînes de connexion sont correctement définies.  Voici quelques exemples.  Vous trouverez des informations supplémentaires au sujet [ici des chaînes de connexion](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Chaîne de connexion ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Chaîne de connexion ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Chaîne de connexion de PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Chaîne de connexion JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problèmes de connexion intermittents

Vérifiez si vous êtes confronté à une charge importante sur le serveur avec un grand nombre de demandes en file d’attente. Vous devrez peut-être mettre à l’échelle de votre entrepôt de données pour des ressources supplémentaires.

## <a name="common-error-messages"></a>Messages d’erreur courants

Erreurs 40914 et 40615, consultez le [description d’erreur et de résolution ici](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Problèmes de connectivité persistent ?
Créer un [ticket de support](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) afin de vous assister par l’équipe d’ingénierie.
