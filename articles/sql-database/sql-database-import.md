---
title: Importer un fichier BACPAC pour créer une base de données
description: Créez une base de données SQL Azure en important un fichier BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228625"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Démarrage rapide : Importer un fichier BACPAC dans Azure SQL Database

Vous pouvez importer une base de données SQL Server dans Azure SQL Database à l’aide d’un fichier [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). Vous pouvez importer les données d’un fichier `BACPAC` stocké dans le Stockage Blob Azure (stockage standard uniquement) ou à partir d’un stockage local à un emplacement local. Pour optimiser la vitesse d’importation en fournissant des ressources plus nombreuses et plus rapides, mettez à l’échelle votre base de données un niveau de service et une taille de calcul supérieurs pendant le processus d’importation. Vous pouvez ensuite effectuer un scale-down après l’importation.

> [!NOTE]
> Le niveau de compatibilité de la base de données importée est basé sur celui de la base de données source.

> [!IMPORTANT]
> Après avoir importé votre base de données, vous pouvez choisir de l’utiliser à son niveau de compatibilité actuel (niveau 100 pour la base de données AdventureWorks2008R2) ou à un niveau supérieur. Pour plus d’informations sur les implications et les options du fonctionnement d’une base de données à un niveau de compatibilité spécifique, consultez [Niveau de compatibilité ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consultez également [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) pour plus d’informations sur des paramètres supplémentaires au niveau de la base de données relatifs aux niveaux de compatibilité.

## <a name="using-azure-portal"></a>En passant par le portail Azure

Regardez cette vidéo pour savoir comment importer à partir d’un fichier BACPAC sur le portail Azure ou poursuivez la lecture ci-dessous :

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Le [portail Microsoft Azure](https://portal.azure.com) prend *uniquement* en charge la création d’une base de données unique dans Azure SQL Database et *uniquement* à partir d’un fichier BACPAC stocké dans le stockage Blob Azure.

La migration d’une base de données vers une [instance managée](sql-database-managed-instance.md) provenant d’un fichier BACPAC à l’aide d’Azure PowerShell n’est pas prise en charge pour le moment. Veuillez plutôt utiliser SQL Server Management Studio ou SQLPackage.

> [!NOTE]
> Les machines qui traitent les requêtes d’importation/exportation soumises par le biais du portail Microsoft Azure ou de PowerShell doivent stocker le fichier BACPAC et les fichiers temporaires générés par Data-Tier Application Framework (DacFX). L’espace disque requis varie considérablement entre bases de données de même taille, et peut nécessiter jusqu’à 3 fois la taille de la base de données. Les ordinateurs exécutant les requêtes d’importation/exportation ne disposent que de 450 Go d’espace disque local. Par conséquent, certaines demandes peuvent échouer avec l’erreur `There is not enough space on the disk`. Dans ce cas, la solution de contournement consiste à exécuter sqlpackage.exe sur un ordinateur ayant suffisamment d’espace disque local. Pour éviter ce problème, nous vous conseillons d’utiliser SqlPackage pour importer/exporter des bases de données de plus de 150 Go.

1. Pour importer à partir d’un fichier BACPAC dans une base de données unique à l’aide du Portail Microsoft Azure, ouvrez la page appropriée du serveur de base de données, puis, dans la barre d’outils, sélectionnez **Importer la base de données**.  

   ![Importation1 de base de données](./media/sql-database-import/sql-server-import-database.png)

1. Sélectionnez le compte de stockage et le conteneur pour le fichier BACPAC, puis sélectionnez le fichier BACPAC à partir duquel importer.

1. Spécifiez la taille de la nouvelle base de données (généralement la même que celle d’origine) et indiquez les informations d’identification du serveur SQL Server de destination. Pour obtenir la liste des valeurs possibles pour Azure SQL Database, consultez [Créer une base de données](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Importation2 de base de données](./media/sql-database-import/sql-server-import-database-settings.png)

1. Cliquez sur **OK**.

1. Pour superviser la progression d’une importation, ouvrez la page du serveur de la base de données et, sous **Paramètres**, sélectionnez **Historique d’importation/exportation**. Une fois achevée, l’importation présente l’état **Terminé**.

   ![Statut d’importation de la base de données](./media/sql-database-import/sql-server-import-database-history.png)

1. Pour vérifier que la base de données est active sur le serveur de base de données, sélectionnez **Bases de données SQL** et assurez-vous que la nouvelle base de données est **En ligne**.

## <a name="using-sqlpackage"></a>Utilisation de SqlPackage

Pour importer une base de données SQL Server à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consultez les [paramètres et propriétés d’importation](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage a les dernières versions de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez aussi télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) sur le Centre de téléchargement Microsoft.

Pour bénéficier d’une mise à l’échelle et de performances optimales, nous vous recommandons d’utiliser SqlPackage dans la plupart des environnements de production au lieu du portail Azure. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers `BACPAC` (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

La commande SqlPackage ci-dessous importe la base de données **AdventureWorks2008R2** du stockage local vers un serveur Azure SQL Database appelé **mynewserver20170403**. Elle crée une base de données appelée **myMigratedDatabase** avec un niveau de service **Premium** et un objectif de service **P6**. Changez ces valeurs en fonction de votre environnement.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pour vous connecter à un serveur SQL Database qui gère une base de données unique derrière un pare-feu d’entreprise, le port de ce dernier doit être 1433 ouvert. Pour vous connecter à une instance gérée, vous devez avoir une [connexion point à site](sql-database-managed-instance-configure-p2s.md) ou une connexion Express Route.

Cet exemple montre comment importer une base de données à l’aide de SqlPackage avec l’authentification universelle Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Utilisation de PowerShell

> [!NOTE]
> [Une instance managée](sql-database-managed-instance.md) ne prend actuellement pas en charge la migration d’une base de données vers une base de données d’instance à partir d’un fichier BACPAC à l’aide d’Azure PowerShell. Pour importer dans une instance managée, utilisez SQL Server Management Studio ou SQLPackage.

> [!NOTE]
> Les machines qui traitent les requêtes d’importation/exportation soumises par le biais du portail ou de Powershell doivent stocker le fichier bacpac et les fichiers temporaires générés par Data-Tier Application Framework (DacFX). L’espace disque requis varie considérablement parmi les bases de données de même taille, et peut prendre jusqu’à trois fois la taille de la base de données. Les ordinateurs exécutant les requêtes d’importation/exportation ne disposent que de 450 Go d’espace disque local. Par conséquent, certaines requêtes peuvent échouer avec l’erreur « Espace insuffisant sur le disque ». Dans ce cas, la solution de contournement consiste à exécuter sqlpackage.exe sur un ordinateur ayant suffisamment d’espace disque local. Pour importer/exporter des bases de données de plus de 150 Go, utilisez SqlPackage pour éviter ce problème.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager (RM) est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Le module AzureRM continue à recevoir des résolutions de bogues jusqu’à au moins décembre 2020.  Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Pour en savoir plus sur leur compatibilité, consultez [Présentation du nouveau module Az Azure PowerShell](/powershell/azure/new-azureps-module-az).

Utilisez la cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) pour soumettre une demande d’importation de base de données au service Azure SQL Database. Selon la taille de la base de données, l’importation peut prendre un certain temps avant d’aboutir.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Vous pouvez utiliser la cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) pour vérifier la progression de l’importation. L’exécution de l’applet de commande de suite après la demande retourne généralement `Status: InProgress`. L’importation est terminée dès que vous voyez `Status: Succeeded`.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) pour soumettre une demande d’importation de base de données au service Azure SQL Database. Selon la taille de la base de données, l’importation peut prendre un certain temps avant d’aboutir.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

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
