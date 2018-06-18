---
title: Restaurer une sauvegarde dans Azure SQL Database Managed Instance | Microsoft Docs
description: Restaurez une sauvegarde de base de données dans Azure SQL Database Managed Instance à l’aide de SSMS.
keywords: didacticiel sql database, sql database managed instance, restaurer une sauvegarde
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 06/07/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: c6f397c2cfcfd9fbd4a5cc8f70a99f4abc86ba21
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "34850676"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Restaurer une sauvegarde de base de données dans Azure SQL Database Managed Instance

Ce didacticiel montre comment restaurer une sauvegarde d’une base de données stockée dans le stockage blob Azure dans l’instance gérée à l’aide de Wide World Importers, fichier de sauvegarde standard. Cette méthode nécessite un temps d’arrêt. Pour un tutoriel utilisant le service Azure Database Migration Service (DMS) pour la migration, consultez [Migrer SQL vers Azure SQL Database Managed Instance](../dms/tutorial-sql-server-to-managed-instance.md). Pour une discussion des diverses méthodes migration, consultez [Migration d’une instance SQL Server vers Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Télécharger le fichier de sauvegarde Wide World Importers - Standard
> * Créer le compte de stockage Azure et charger le fichier de sauvegarde
> * Restaurer la base de données Wide World Importers à partir d’un fichier de sauvegarde

## <a name="prerequisites"></a>Prérequis

Ce didacticiel utilise comme point de départ les ressources créées dans ce didacticiel : [Créer une Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Télécharger le fichier de sauvegarde Wide World Importers - Standard

Suivez la procédure ci-après pour télécharger le fichier de sauvegarde Wide World Importers - Standard.

À l’aide d’Internet Explorer, entrez https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak dans la zone d’adresse d’URL et, lorsque vous y êtes invité, cliquez sur **Enregistrer** pour enregistrer ce fichier dans le dossier **Téléchargements**.

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Créer le compte de stockage Azure et charger le fichier de sauvegarde

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Recherchez **Stockage**, puis cliquez sur **Compte de stockage** pour ouvrir le formulaire de compte de stockage.

   ![compte de stockage](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Remplissez le formulaire de compte de stockage avec les informations demandées, en utilisant les données du tableau suivant :

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   |**Name**|Nom valide|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Modèle de déploiement**|Modèle de ressource||
   |**Type de compte**|Stockage d'objets blob||
   |**Performances**|Standard ou premium|Lecteurs magnétiques ou disques SSD|
   |**Réplication**|Stockage localement redondant||
   |\*\*Niveau d’accès (par défaut)|Froid ou chaud||
   |**Transfert sécurisé requis**|Désactivé||
   |**Abonnement**|Votre abonnement|Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions).|
   |**Groupe de ressources**|Le groupe de ressources que vous avez créé précédemment|| 
   |**Lieu**|L’emplacement que vous avez précédemment sélectionné||
   |**Réseaux virtuels**|Désactivé||

4. Cliquez sur **Créer**.

   ![détails du compte de stockage](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Une fois le déploiement du compte de stockage terminé, ouvrez votre nouveau compte de stockage.
6. Sous **Paramètres**, cliquez sur **Signature d’accès partagé** pour ouvrir le formulaire de signature d’accès partagé (SAP).

   ![formulaire de SAP](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Dans le formulaire SAP, modifiez les valeurs par défaut comme vous le souhaitez. Notez que la date/l’heure d’expiration est, par défaut, uniquement de 8 heures.
8. Cliquez sur **Générer une signature d’accès partagé**.

   ![formulaire de SAP complété](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Copiez et enregistrez le **jeton SAP** et **Blob server SAS URL** (URL de SAP du server blob).
10. Sous **Paramètres**, cliquez sur **Conteneurs**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Cliquez sur **+Conteneur** pour créer un conteneur qui hébergera votre fichier de sauvegarde.
12. Remplissez le formulaire de conteneur avec les informations demandées, en utilisant les données du tableau suivant :

    | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   |**Name**|Nom valide|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Niveau d’accès public**|Privé (aucun accès anonyme)||

    ![détails du conteneur](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Cliquez sur **OK**.
14. Une fois le conteneur créé, ouvrez-le.

    ![conteneur](./media/sql-database-managed-instance-tutorial/container.png)

15. Cliquez sur **Propriétés du conteneur**, puis copiez l’URL du conteneur.

    ![ID du conteneur](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Cliquez sur **Charger** pour ouvrir le formulaire **Charger l’objet blob**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Recherchez le dossier de téléchargement et sélectionnez le fichier **WideWorldIimporters-Standard.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Cliquez sur **Télécharger**.
19. Ne continuez pas tant que le chargement n’est pas terminé.

    ![chargement terminé](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Restaurer la base de données Wide World Importers à partir d’un fichier de sauvegarde

Avec SSMS, procédez comme suit pour restaurer la base de données Wide World Importers à partir du fichier de sauvegarde dans votre option Managed Instance.

1. Dans SSMS, ouvrez une nouvelle fenêtre de requête.
2. Utilisez le script suivant pour créer des informations d’identification SAP, en fournissant l’URL du conteneur de compte de stockage et la clé SAP, comme indiqué.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. Utilisez le script suivant pour créer des informations d’identification SAP et la validité de la sauvegarde, en fournissant l’URL du conteneur avec le fichier de sauvegarde :

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![liste de fichiers](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Utilisez le script suivant pour restaurer la base de données Adventure Works 2012 à partir d’un fichier de sauvegarde, en fournissant l’URL du conteneur avec le fichier de sauvegarde :

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![restauration en cours d’exécution](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Pour suivre l’état de votre restauration, exécutez la requête suivante dans une nouvelle session de requête :

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![pourcentage d’achèvement de la restauration](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Une fois la restauration terminée, affichez-la dans l’Explorateur d’objets. 

    ![restauration terminée](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à restaurer une sauvegarde d’une base de données stockée dans le stockage blob Azure dans l’instance gérée à l’aide de Wide World Importers, fichier de sauvegarde standard. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Télécharger le fichier de sauvegarde Wide World Importers - Standard
> * Créer le compte de stockage Azure et charger le fichier de sauvegarde
> * Restaurer la base de données Wide World Importers à partir d’un fichier de sauvegarde

Passez au didacticiel suivant pour apprendre à migrer SQL Server vers Azure SQL Database Managed Instance à l’aide de DMS.

> [!div class="nextstepaction"]
>[Migrer SQL Server vers Azure SQL Database Managed Instance à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md)