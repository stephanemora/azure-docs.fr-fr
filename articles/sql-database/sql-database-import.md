---
title: Importer un fichier BACPAC pour créer une base de données SQL Azure | Microsoft Docs
description: Créez une base de données SQL Azure en important un fichier BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c1b6c55475c1600c89c1ac1cae9dee0068b92070
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478217"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Démarrage rapide : Importer un fichier BACPAC dans une nouvelle base de données SQL Azure

Vous pouvez migrer une base de données SQL Server vers une base de données SQL Azure en utilisant un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (fichier zip avec une extension `.bacpac` qui a les métadonnées et les données d’une base de données). Vous pouvez importer un fichier BACPAC à partir de Stockage Blob Azure (stockage standard uniquement) ou à partir d’un stockage local à un emplacement local. Pour optimiser la vitesse d’importation, vous pouvez spécifier un niveau de service plus élevé et calculer la taille (par exemple, P6). Vous pouvez ensuite effectuer un scale-down après l’importation. Le niveau de compatibilité de la base de données importée est basé sur celui de la base de données source.

> [!IMPORTANT]
> Après avoir importé votre base de données, vous pouvez choisir de l’utiliser à son niveau de compatibilité actuel (niveau 100 pour la base de données AdventureWorks2008R2) ou à un niveau supérieur. Pour plus d’informations sur les implications et les options du fonctionnement d’une base de données à un niveau de compatibilité spécifique, consultez [Niveau de compatibilité ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consultez également [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) pour plus d’informations sur des paramètres supplémentaires au niveau de la base de données relatifs aux niveaux de compatibilité.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importer à partir d’un fichier BACPAC sur le portail Azure

Cette section montre comment créer sur le [portail Azure](https://portal.azure.com) une base de données Azure SQL Database à partir d’un fichier BACPAC stocké dans Stockage Blob Azure. Le portail prend *uniquement* en charge l’importation d’un fichier BACPAC à partir de Stockage Blob Azure.

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) prend en charge l’importation à partir d’un fichier BACPAC en employant les autres méthodes de cet article, mais ne prend actuellement pas en charge la migration sur le portail Azure.

Pour importer une base de données sur le portail Azure, ouvrez la page du serveur SQL Database destiné à héberger l’importation puis, dans la barre d’outils, sélectionnez **Importer une base de données**.  

   ![Importation de base de données](./media/sql-database-import/import.png)

Sélectionnez le compte de stockage, le conteneur et le fichier BACPAC que vous voulez importer. Spécifiez la taille de la nouvelle base de données (généralement la même que celle d’origine) et indiquez les informations d’identification du serveur SQL Server de destination. 

### <a name="monitor-imports-progress"></a>Superviser la progression de l’importation

Pour superviser la progression d’une importation, ouvrez la page du serveur de la base de données importée et, sous **Paramètres**, sélectionnez **Historique d’importation/exportation**. Une fois achevée, l’importation présente l’état **Terminé**.

Pour vérifier que la base de données est active sur le serveur, sélectionnez **Bases de données SQL** et assurez-vous que la nouvelle base de données est **En ligne**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importer à partir d’un fichier BACPAC à l’aide de SqlPackage

Pour importer une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consultez [Paramètres et propriétés d’importation](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage a les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez aussi télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) sur le Centre de téléchargement Microsoft.

Pour bénéficier d’une mise à l’échelle et de performances optimales, nous vous recommandons d’utiliser SqlPackage dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

La commande SqlPackage ci-dessous importe la base de données **AdventureWorks2008R2** du stockage local vers un serveur Azure SQL Database appelé **mynewserver20170403**. Elle crée une base de données appelée **myMigratedDatabase** avec un niveau de service **Premium** et un objectif de service **P6**. Changez ces valeurs en fonction de votre environnement.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Un serveur SQL Database écoute sur le port 1433. Pour vous connecter à un serveur SQL Database derrière un pare-feu d’entreprise, le port de ce dernier doit être ouvert.
>

Cet exemple montre comment importer une base de données à l’aide de SqlPackage avec l’authentification universelle Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importer à partir d’un fichier BACPAC à l’aide de PowerShell

Utilisez l’applet de commande [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) pour soumettre une demande d’importation de base de données au service de base de données SQL Azure. Selon la taille de la base de données, l’importation peut prendre un certain temps avant d’aboutir.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Vous pouvez utiliser l’applet de commande [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) pour vérifier la progression de l’importation. Quand vous exécutez l’applet de commande de suite après la demande, l’état généralement retourné est **État : En cours**. L’importation est terminée dès que vous voyez **État : Réussite**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Pour un autre exemple de script, consultez [Importation d’une base de données à partir d’un fichier BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limites

L’importation dans une base de données d’un pool élastique n’est pas prise en charge. Vous pouvez importer les données dans une base de données unique, puis déplacer cette dernière vers le pool élastique.

## <a name="import-using-wizards"></a>Importer à l’aide d’Assistants

Vous pouvez aussi utiliser ces Assistants.

- [Assistant Importation de l’application de la couche Données dans SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Assistant Importation et Exportation de SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment vous connecter à une base de données SQL Database importée et comment l’interroger, consultez [Démarrage rapide : Azure SQL Database : Utiliser SQL Server Management Studio pour se connecter et interroger des données](sql-database-connect-query-ssms.md).
- Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Pour obtenir une description de l’ensemble du processus de migration de base de données SQL Server ainsi que des recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers Azure SQL Database](sql-database-cloud-migrate.md).
- Pour savoir comment gérer et partager de façon sécurisée les clés de stockage et les signatures d’accès partagé, consultez le [Guide de sécurité de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
