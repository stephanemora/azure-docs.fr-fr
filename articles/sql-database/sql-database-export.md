---
title: Exporter une base de données Azure SQL unique ou mise en pool dans un fichier BACPAC | Microsoft Docs
description: Exporter une base de données Azure SQL dans un fichier BACPAC à l’aide du portail Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: 27a65a871264fa13a42acfb5be2d4b5f99d31adc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483831"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exporter une base de données Azure SQL dans un fichier BACPAC

Si vous avez besoin d’exporter une base de données afin de l’archiver ou de la déplacer vers une autre plateforme, vous pouvez exporter le schéma et les données de cette base de données vers un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un fichier BACPAC est un fichier ZIP avec une extension de fichier BACPAC contenant les métadonnées et les données à partir d’une base de données SQL Server. Il peut être stocké dans le Stockage Blob Azure ou un stockage local dans un emplacement local, puis importé dans Azure SQL Database ou une installation locale SQL Server.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considérations à prendre en compte lors de l’exportation d’une base de données Azure SQL

- Pour qu’un export soit cohérent au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données Azure SQL.
- Si vous exportez dans le stockage Blob, la taille maximale d’un fichier BACPAC est de 200 Go. Pour archiver un fichier BACPAC plus volumineux, exportez dans le stockage local.
- L’exportation d’un fichier BACPAC vers le stockage Premium Azure en utilisant les méthodes décrites dans cet article n’est pas prise en charge.
- Stockage derrière un pare-feu n’est actuellement pas pris en charge.
- Si l’opération d’exportation depuis Azure SQL Database dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :

  - Augmentez temporairement votre taille de calcul.
  - Interrompez toutes les activités de lecture et d’écriture lors de l’exportation.
  - Utilisez un [index cluster](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non nulles sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier. Une bonne méthode pour déterminer si vos tables sont optimisées pour l’exportation consiste à exécuter **DBCC SHOW_STATISTICS** et à vérifier que *RANGE_HI_KEY* n’est pas nul et que sa valeur a une bonne distribution. Pour plus d’informations, consultez [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. Azure SQL Database crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d’ensemble de la continuité de l’activité avec la base de données SQL Azure](sql-database-business-continuity.md) et [Découvrir les sauvegardes SQL Database](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exporter vers un fichier BACPAC à l’aide du portail Azure

> [!NOTE]
> [Une instance gérée](sql-database-managed-instance.md) ne prend actuellement pas en charge l'exportation d’une base de données vers un fichier BACPAC à l’aide du portail Azure. Pour exporter une instance gérée vers un fichier BACPAC, utilisez SQL Server Management Studio ou SQLPackage.

1. Pour exporter une base de données à l’aide du [portail Azure](https://portal.azure.com), ouvrez la page de votre base de données, puis cliquez sur **Exporter** dans la barre d’outils.

   ![Exportation de base de données](./media/sql-database-export/database-export1.png)

2. Spécifiez le nom du fichier BACPAC, sélectionnez un compte de stockage Azure existant ainsi que le conteneur pour l’exportation, puis fournissez les informations d’identification qui conviennent à des fins de connexion à la base de données source.

    ![Exportation de base de données](./media/sql-database-export/database-export2.png)

3. Cliquez sur **OK**.

4. Pour surveiller la progression de l’opération d’exportation, ouvrez la page du serveur SQL Database qui contient la base de données en cours d’exportation. Sous **Paramètres**, cliquez sur **Historique d'importation/exportation**.

   ![historique d'exportation](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exporter vers un fichier BACPAC à l’aide de l’utilitaire SQLPackage

Pour exporter une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consultez [Paramètres et propriétés d’exportation](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). L’utilitaire SQLPackage est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

Nous recommandons l’utilisation de l’utilitaire SQLPackage afin de bénéficier de l’évolutivité et des performances dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Cet exemple montre comment exporter une base de données à l’aide de SqlPackage.exe avec l’authentification Active Directory universelle :

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exporter vers un fichier BACPAC à l’aide de SQL Server Management Studio (SSMS)

Les dernières versions de SQL Server Management Studio incluent un assistant permettant d’exporter une base de données Azure SQL vers un fichier BACPAC. Consultez [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exporter vers un fichier BACPAC à l’aide de PowerShell

> [!NOTE]
> [Une instance gérée](sql-database-managed-instance.md) ne prend actuellement pas en charge l'exportation d’une base de données vers un fichier BACPAC à l’aide d'Azure PowerShell. Pour exporter une instance gérée vers un fichier BACPAC, utilisez SQL Server Management Studio ou SQLPackage.

Utilisez le [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) applet de commande pour soumettre une demande de base de données d’exportation pour le service de base de données SQL Azure. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Pour vérifier l’état de la demande d’exportation, utilisez le [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) applet de commande. L’exécution immédiatement après la requête retourne généralement **État : en cours**. Lorsque **État : réussite** s’affiche, l’exportation est terminée.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la conservation des sauvegardes à long terme des bases de données uniques et des bases de données mises en pool comme alternative à l’exportation d’une base de données à des fins d’archivage, consultez [Conservation à long terme](sql-database-long-term-retention.md). Vous pouvez utiliser des travaux SQL Agent pour planifier des [sauvegardes de base de données en copie seule](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) comme alternative à la rétention des sauvegardes à long terme.
- Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPAC dans une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Pour en savoir plus sur l’exportation d’un fichier BACPAC à partir d’une base de données SQL Server, consultez [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Pour en savoir plus sur l’utilisation du service de migration de données afin de migrer une base de données, consultez [Migrer SQL Server vers Azure SQL Database hors connexion à l’aide de DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Si vous exportez depuis SQL Server avant de procéder à la migration vers Azure SQL Database, consultez [Migrer une base de données SQL Server vers Azure SQL Database](sql-database-single-database-migrate.md).
- Pour savoir comment gérer et partager de façon sécurisée les clés de stockage et les signatures d’accès partagé, consultez le [Guide de sécurité de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
