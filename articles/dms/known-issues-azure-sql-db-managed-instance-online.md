---
title: Problèmes connus et limitations dans le cadre des migrations en ligne vers Azure SQL Managed Instance
description: Apprenez-en plus sur les problèmes connus/limitations de migration associées dans le cadre des migrations en ligne vers Azure SQL Managed Instance.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98695529"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure SQL Managed Instance

Les problèmes connus et limitations associées aux migrations en ligne de SQL Server vers Azure SQL Managed Instance sont décrits ci-dessous.

> [!IMPORTANT]
> Avec les migrations en ligne de SQL Server pour la base de données SQL Azure, la migration de types de données SQL_variant n’est pas pris en charge.

## <a name="backup-requirements"></a>Exigences de sauvegarde

- **Sauvegardes avec somme de contrôle**

    Azure Database Migration Service utilise la méthode de sauvegarde et de restauration pour migrer vos bases de données locales vers SQL Managed Instance. Azure Database Migration Service prend uniquement en charge les sauvegardes créées à l'aide de la somme de contrôle.

    [Activez ou désactivez les sommes de contrôle de sauvegarde au cours d’opérations de sauvegarde ou de restauration (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Si vous effectuez des sauvegardes de base de données avec compression, la somme de contrôle correspond au comportement par défaut, sauf désactivation explicite.

    Avec les migrations hors connexion, si vous sélectionnez **J'autorise Azure Database Migration Service…** , Azure Database Migration Service sauvegarde la base de données avec la somme de contrôle activée.

- **Support de sauvegarde**

    Veillez à effectuer chaque sauvegarde sur un support de sauvegarde distinct (fichiers de sauvegarde). Azure Database Migration Service ne prend pas en charge les sauvegardes ajoutées à un fichier de sauvegarde unique. Effectuez une sauvegarde complète et des sauvegardes de journaux pour séparer les fichiers de sauvegarde.

## <a name="data-and-log-file-layout"></a>Disposition des fichiers de données et des fichiers journaux

- **Nombre de fichiers journaux**

    Azure Database Migration Service ne prend pas en charge les bases de données avec plusieurs fichiers journaux. Si vous disposez de plusieurs fichiers journaux, réduisez et réorganisez-les au sein d'un même fichier journal de transactions. N'étant pas en mesure de vous connecter à distance à des fichiers journaux non vides, vous devez d’abord sauvegarder le fichier journal.

## <a name="sql-server-features"></a>fonctionnalités SQL Server

- **FileStream/FileTables**

    Actuellement, SQL Managed Instance ne prend pas en charge FileStream et FileTables. Pour les charges de travail dépendant de ces fonctionnalités, nous vous recommandons d’opter pour les instances SQL Server qui s'exécutent sur des machines virtuelles Azure telles que votre cible Azure.

- **Tables en mémoire**

    OLTP en mémoire est disponible aux niveaux Premium et Critique pour l’entreprise pour SQL Managed Instance. Le niveau Usage général ne prend pas en charge OLTP en mémoire.

## <a name="migration-resets"></a>Réinitialisations de migration

- **Déploiements**

    SQL Managed Instance est un service PaaS avec mises à jour correctives et mises à jour de version automatiques. Lors de la migration de votre instance SQL Managed Instance, les mises à jour non critiques sont suspendues jusqu’à 36 heures. Ensuite (et pour les mises à jour critiques), si la migration est interrompue, le processus se réinitialise en état de restauration complète.

    Le basculement de la migration ne peut être appelé qu'au terme de la restauration de la sauvegarde complète, et récupère toutes les sauvegardes de journaux. Si vos basculements de migration de production sont affectés, contactez l'[alias de commentaires Azure DMS](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>Connectivité du partage de fichiers SMB

Les problèmes de connexion au partage de fichiers SMB sont probablement dus à un problème d’autorisation. 

Pour tester la connectivité du partage de fichiers SMB, procédez comme suit : 

1. Enregistrez une sauvegarde dans le partage de fichiers SMB. 
1. Vérifiez la connectivité réseau entre le sous-réseau d’Azure Database Migration Service et le serveur SQL source. Le moyen le plus simple consiste à déployer une machine virtuelle SQL Server sur le sous-réseau DMS et à vous connecter au serveur SQL source à l’aide de SQL Server Management Studio. 
1. Restaurez l’en-tête sur le serveur SQL source à partir de la sauvegarde sur le partage de fichiers : 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Si vous ne parvenez pas à vous connecter au partage de fichiers, configurez les autorisations en procédant comme suit : 

1. Accédez à votre partage de fichiers à l’aide d’Explorateur de fichiers. 
1. Cliquez avec le bouton droit sur le partage de fichiers et sélectionnez Propriétés. 
1. Choisissez l’onglet **Partage** et sélectionnez **Partage avancé**. 
1. Ajoutez le compte Windows utilisé pour la migration et attribuez-lui un contrôle d’accès total. 
1. Ajoutez le compte de service SQL Server et attribuez-lui un contrôle d’accès total. Vérifiez **Gestionnaire de configuration SQL Server** pour le compte de service SQL Server si vous n’êtes pas certain du compte utilisé. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Accordez un contrôle d’accès total aux comptes Windows utilisés pour la migration et pour le compte de service SQL Server.":::

