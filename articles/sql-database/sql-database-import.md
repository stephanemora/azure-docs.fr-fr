---
title: Importer un fichier BACPAC pour créer une base de données SQL Azure | Microsoft Docs
description: Créez une base de données SQL Azure en important un fichier BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3eaa12b5ba735d1e2aa0e074054328942a3041eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338612"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Démarrage rapide : Importer un fichier BACPAC dans Azure SQL Database

Vous pouvez importer une base de données SQL Server dans Azure SQL Database à l’aide d’un fichier [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). Vous pouvez importer les données d’un fichier `BACPAC` stocké dans le Stockage Blob Azure (stockage standard uniquement) ou à partir d’un stockage local à un emplacement local. Pour optimiser la vitesse d’importation en fournissant des ressources plus nombreuses et plus rapides, mettez à l’échelle votre base de données à un niveau de service plus élevé et calculez la taille pendant le processus d’importation. Vous pouvez ensuite effectuer un scale-down après l’importation.

> [!NOTE]
> Le niveau de compatibilité de la base de données importée est basé sur celui de la base de données source.
> [!IMPORTANT]
> Après avoir importé votre base de données, vous pouvez choisir de l’utiliser à son niveau de compatibilité actuel (niveau 100 pour la base de données AdventureWorks2008R2) ou à un niveau supérieur. Pour plus d’informations sur les implications et les options du fonctionnement d’une base de données à un niveau de compatibilité spécifique, consultez [Niveau de compatibilité ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consultez également [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) pour plus d’informations sur des paramètres supplémentaires au niveau de la base de données relatifs aux niveaux de compatibilité.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importer à partir d’un fichier BACPAC sur le portail Azure

Le [Portail Microsoft Azure](https://portal.azure.com) prend *uniquement* en charge la création d’une base de données unique dans Azure SQL Database et *uniquement* à partir d’un fichier BACPAC stocké dans le stockage Blob Azure.

> [!NOTE]
> [Une instance gérée](sql-database-managed-instance.md) ne prend actuellement pas en charge la migration d’une base de données vers une base de données d’instance à partir d’un fichier BACPAC à l’aide du Portail Microsoft Azure. Pour importer dans une instance managée, utilisez SQL Server Management Studio ou SQLPackage.

1. Pour importer à partir d’un fichier BACPAC dans une base de données unique à l’aide du Portail Microsoft Azure, ouvrez la page appropriée du serveur de base de données, puis, dans la barre d’outils, sélectionnez **Importer la base de données**.  

   ![Importation1 de base de données](./media/sql-database-import/import1.png)

2. Sélectionnez le compte de stockage et le conteneur pour le fichier BACPAC, puis sélectionnez le fichier BACPAC à partir duquel importer.
3. Spécifiez la taille de la nouvelle base de données (généralement la même que celle d’origine) et indiquez les informations d’identification du serveur SQL Server de destination. Pour obtenir la liste des valeurs possibles pour Azure SQL Database, consultez [Créer une base de données](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Importation2 de base de données](./media/sql-database-import/import2.png)

4. Cliquez sur **OK**.

5. Pour superviser la progression d’une importation, ouvrez la page du serveur de la base de données et, sous **Paramètres**, sélectionnez **Historique d’importation/exportation**. Une fois achevée, l’importation présente l’état **Terminé**.

   ![Statut d’importation de la base de données](./media/sql-database-import/import-status.png)

6. Pour vérifier que la base de données est active sur le serveur de base de données, sélectionnez **Bases de données SQL** et assurez-vous que la nouvelle base de données est **En ligne**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importer à partir d’un fichier BACPAC à l’aide de SqlPackage

Pour importer une base de données SQL Server à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consultez les [paramètres et propriétés d’importation](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage a les dernières versions de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez aussi télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) sur le Centre de téléchargement Microsoft.

Pour bénéficier d’une mise à l’échelle et de performances optimales, nous vous recommandons d’utiliser SqlPackage dans la plupart des environnements de production au lieu du portail Azure. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers `BACPAC` (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Pour bénéficier d’une mise à l’échelle et de performances optimales, nous vous recommandons d’utiliser SqlPackage dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

La commande SqlPackage ci-dessous importe la base de données **AdventureWorks2008R2** du stockage local vers un serveur Azure SQL Database appelé **mynewserver20170403**. Elle crée une base de données appelée **myMigratedDatabase** avec un niveau de service **Premium** et un objectif de service **P6**. Changez ces valeurs en fonction de votre environnement.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pour vous connecter à un serveur SQL Database qui gère une base de données unique derrière un pare-feu d’entreprise, le port de ce dernier doit être 1433 ouvert. Pour vous connecter à une instance gérée, vous devez avoir une [connexion point à site](sql-database-managed-instance-configure-p2s.md) ou une connexion Express Route.
>

Cet exemple montre comment importer une base de données à l’aide de SqlPackage avec l’authentification universelle Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importer dans une base de données unique à partir d’un fichier BACPAC à l’aide de PowerShell

> [!NOTE]
> [Une instance gérée](sql-database-managed-instance.md) ne prend pas en charge la migration d’une base de données dans une base de données d’instance à partir d’un fichier BACPAC à l’aide d’Azure PowerShell. Pour importer dans une instance managée, utilisez SQL Server Management Studio ou SQLPackage.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

Utilisez le [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) applet de commande pour soumettre une demande de base de données d’importation pour le service de base de données SQL Azure. Selon la taille de la base de données, l’importation peut prendre un certain temps avant d’aboutir.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Vous pouvez utiliser la [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) applet de commande pour vérifier l’état d’avancement de l’importation. Quand vous exécutez l’applet de commande de suite après la demande, l’état généralement retourné est **État : En cours**. L’importation est terminée dès que vous voyez **État : Réussite**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Pour un autre exemple de script, consultez [Importation d’une base de données à partir d’un fichier BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limites

L’importation dans une base de données d’un pool élastique n’est pas prise en charge. Vous pouvez importer les données dans une base de données unique, puis déplacer cette dernière vers le pool élastique.

## <a name="import-using-wizards"></a>Importer à l’aide d’Assistants

Vous pouvez aussi utiliser ces Assistants.

- [Assistant Importation de l’application de la couche Données dans SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Assistant Importation et Exportation de SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment vous connecter à une base de données SQL Database importée et comment l’interroger, consultez [Démarrage rapide : Azure SQL Database : Utiliser SQL Server Management Studio pour se connecter et interroger des données](sql-database-connect-query-ssms.md).
- Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Pour obtenir une description de l’ensemble du processus de migration de base de données SQL Server ainsi que des recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers Azure SQL Database](sql-database-single-database-migrate.md).
- Pour savoir comment gérer et partager de façon sécurisée les clés de stockage et les signatures d’accès partagé, consultez le [Guide de sécurité de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
