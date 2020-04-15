---
title: Restaurer une sauvegarde vers une instance gérée
description: Restaurez une sauvegarde de base de données dans Azure SQL Database Managed Instance à l’aide de SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 3b12aa860bee189f706bd98df63fc194a3a0cc71
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874702"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Démarrage rapide : Restaurer une base de données sur une instance managée

Dans ce guide de démarrage rapide, vous utilisez SQL Server Management Studio (SSMS) pour restaurer une base de données (le fichier de sauvegarde standard Wide World Importers) à partir du stockage Blob Azure dans une [instance managée](sql-database-managed-instance.md) Azure SQL Database.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Pour plus d’informations sur la migration à l’aide d’Azure Database Migration Service (DMS), consultez [Migration d’instances managées à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).
> Pour plus d’informations sur les diverses méthodes de migration, consultez [Migration d’une instance SQL Server vers Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Prérequis

Dans ce guide de démarrage rapide :

- Utilise des ressources du guide de démarrage rapide [Créer une instance managée](sql-database-managed-instance-get-started.md).
- Votre ordinateur doit avoir la dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) installée.
- Nécessite l’utilisation de SSMS pour se connecter à votre instance managée. Consultez ces guides de démarrage rapide sur la façon de se connecter :
  - [Activer le point de terminaison public](sql-database-managed-instance-public-endpoint-configure.md) sur Managed Instance (il s’agit de l’approche recommandée pour ce didacticiel).
  - [Se connecter à une instance Azure SQL Database Managed Instance à partir d’une machine virtuelle Azure](sql-database-managed-instance-configure-vm.md)
  - [Configurer une connexion point à site à une instance managée Azure SQL Database à partir d’un emplacement local](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Pour plus d’informations sur la sauvegarde et la restauration d’une base de données SQL Server à l’aide du stockage Blob Azure et d’une [clé de signature d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), consultez [Sauvegarde SQL Server vers une URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Restaurer la base de données à partir d’un fichier de sauvegarde

Avec SSMS, suivez ces étapes pour restaurer la base de données Wide World Importers vers votre instance managée. Le fichier de sauvegarde de base de données est stocké dans un compte de stockage Blob Azure préconfiguré.

1. Ouvrez SSMS et connectez-vous à votre instance managée.
2. Dans le menu de gauche, cliquez avec le bouton droit sur votre instance managée et sélectionnez **Nouvelle requête** pour ouvrir une nouvelle fenêtre de requête.
3. Exécutez le script SQL suivant, qui crée un compte de stockage préconfiguré et une clé SAS pour [créer des informations d’identification](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) dans votre instance managée.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![créer des informations d’identification](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Pour vérifier vos informations d’identification, exécutez le script suivant, qui utilise une URL de [conteneur](https://azure.microsoft.com/services/container-instances/) permettant d’obtenir une liste de fichiers de sauvegarde.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![liste de fichiers](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Exécutez le script suivant pour restaurer la base de données Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![restauration](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Exécutez le script suivant pour suivre l’état de la restauration.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Une fois la restauration terminée, affichez la base de données dans l’Explorateur d’objets. Vous pouvez vérifier que la restauration de la base de données est terminée dans la vue [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).

> [!NOTE]
> L’opération de restauration de base de données est asynchrone et peut être retentée. SQL Server Management Studio peut générer une erreur en cas d’interruption de la connexion ou d’expiration d’un délai d’attente. Azure SQL Database continue d’essayer de restaurer la base de données en arrière-plan et vous pouvez suivre l’avancement de la restauration dans les vues [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) et [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).
> Dans certaines phases du processus de restauration, un identificateur unique s’affiche au lieu du nom réel de la base de données dans les vues système. Découvrez les `RESTORE`différences de comportement d’instruction [ici](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes liés à la sauvegarde vers une URL, consultez [Bonnes pratiques et résolution des problèmes liés à la sauvegarde SQL Server vers une URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Pour obtenir une vue d’ensemble des options de connexion pour les applications, consultez [Connecter vos applications à Managed Instance](sql-database-managed-instance-connect-app.md).
- Pour lancer des requêtes à l’aide d’un de vos outils ou langages préférés, consultez [Démarrage rapide : Se connecter à une base de données Azure SQL et l’interroger](sql-database-connect-query.md).
