---
title: Serveurs d’hébergement SQL sur Azure Stack | Microsoft Docs
description: Comment ajouter des instances SQL pour l’approvisionnement via le fournisseur de ressources de l’adaptateur SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265226"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Ajouter des serveurs d’hébergement pour le fournisseur de ressources SQL

Vous pouvez héberger une instance SQL sur une machine virtuelle dans [Azure Stack](azure-stack-poc.md), ou sur une machine virtuelle à l’extérieur de votre environnement Azure Stack, à condition que le fournisseur de ressources SQL puisse se connecter à l’instance.

## <a name="overview"></a>Vue d'ensemble

Les spécifications générales pour les serveurs d’hébergement SQL sont les suivantes :

* L’instance SQL doit être dédiée à une utilisation par le fournisseur de ressources et les charges de travail utilisateur. Vous ne pouvez pas utiliser d’instance SQL utilisée par un autre consommateur. Cette restriction s’applique également à App Services.
* La machine virtuelle du fournisseur de ressources SQL n’est pas jointe au domaine et ne peut se connecter que par le biais de l’authentification SQL.
* Il est nécessaire de configurer un compte doté des privilèges appropriés pour que le fournisseur de ressources puisse l’utiliser.
* Le fournisseur de ressources et les utilisateurs, notamment Web Apps, utilisent le réseau des utilisateurs ; c’est pourquoi une connexion à l’instance SQL sur ce réseau est requise. Cette exigence signifie généralement que l’adresse IP de vos instances SQL doit se trouver sur un réseau public.
* La gestion des instances SQL et de leurs hôtes vous revient. Par exemple, le fournisseur de ressources n’applique pas les mises à jour, ne gère pas les sauvegardes et ne gère pas non plus la rotation des informations d’identification.
* Vous pouvez utiliser des références SKU qui prennent en charge différentes classes de capacités SQL, telles que les performances et la haute disponibilité à l’aide d’AlwaysOn.

### <a name="sql-server-virtual-machine-images"></a>Images de machines virtuelles SQL Server

Des images de machines virtuelles IaaS SQL sont disponibles via la fonctionnalité Gestion de la Place de Marché. Ces images sont les mêmes que les machines virtuelles SQL disponibles dans Azure.

Veillez à toujours télécharger la dernière version de **l’extension IaaS SQL** avant de déployer une machine virtuelle à l’aide d’un élément de la Place de marché.  L’extension IaaS et les améliorations apportées au portail correspondantes fournissent des fonctionnalités supplémentaires de mise à jour corrective et de sauvegarde automatique.

Il existe d’autres options pour le déploiement de machines virtuelles SQL, y compris des modèles dans la [Galerie de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Les serveurs d’hébergement installés sur un Azure Stack à plusieurs nœuds doivent être créés à partir d’un abonnement utilisateur. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail de l’utilisateur ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent disposer des licences SQL appropriées. L’administrateur de service _peut_ être le propriétaire de cet abonnement.

### <a name="required-privileges"></a>Privilèges requis

Vous pouvez créer un utilisateur administratif avec des privilèges plus faibles qu’un administrateur système SQL. L’utilisateur a uniquement besoin des autorisations pour les opérations suivantes :

- Base de données : Créer, Modifier, Avec imbrication (pour Always On uniquement), Supprimer, Sauvegarder
- Groupe de disponibilité : Modifier, Joindre, Ajouter/Supprimer une base de données
- Connexion : Créer, Sélectionner, Modifier, Supprimer, Révoquer
- Sélection d’opérations : \[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[sys\].\[dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[sys\].\[availability_groups\] (AlwaysOn), sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Fournir une capacité par le biais d’une connexion à un serveur SQL d’hébergement

Vous pouvez utiliser des serveurs SQL (non-HA) autonomes à l’aide de n’importe quelle édition de SQL Server 2014 ou SQL Server 2016. Assurez-vous de détenir les informations d’identification d’un compte disposant de privilèges d’administrateur système.

Pour ajouter un serveur d’hébergement autonome déjà configuré, procédez comme suit :

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.

2. Sélectionnez **Parcourir** &gt; **RESSOURCES ADMINISTRATIVES** &gt; **Serveurs d’hébergement SQL**.

   ![Serveurs d’hébergement SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Dans le panneau **Serveurs d’hébergement SQL**, vous pouvez connecter le fournisseur de ressources SQL à des instances de SQL Server qui font office de serveur principal du fournisseur de ressources.

   ![Tableau de bord de l’adaptateur SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Remplissez le formulaire avec les détails de connexion de votre instance de SQL Server.

   ![Ajouter un serveur d’hébergement SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Vous pouvez, si vous le souhaitez, ajouter un nom d’instance et spécifier un numéro de port si l’instance n’est pas affectée au port par défaut, 1433.

   > [!NOTE]
   > Tant que l’instance SQL est accessible par l’utilisateur et l’administrateur Azure Resource Manager, elle peut être placée sous contrôle du fournisseur de ressources. L’instance de SQL __doit__ être allouée exclusivement au fournisseur de ressources.

4. À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour différencier les offres de service. Par exemple, vous pouvez avoir une instance de SQL Enterprise fournissant :
  
   - Une capacité de base de données
   - La sauvegarde automatique
   - La réservation de serveurs hautes performances pour des services individuels

   Tous les serveurs d’hébergement d’une référence SKU doivent avoir les mêmes capacités. Le **nom** doit refléter les propriétés de la référence SKU pour que les utilisateurs puissent déployer leurs bases de données sur la référence SKU appropriée.

   > [!IMPORTANT]
   > Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** et **Niveau** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

   Par exemple : 

   ![Créer une référence SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > Une heure entière peut être nécessaire avant que les références n’apparaissent dans le portail. Les utilisateurs ne peuvent pas créer de base de données tant que la référence SKU n’a pas été complètement créée.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fournir une haute disponibilité à l’aide de groupes de disponibilité Always On SQL

La configuration d’instances Always On SQL nécessite des étapes supplémentaires et requiert au moins trois machines virtuelles (ou machines physiques). Cet article suppose que vous disposiez déjà d’une connaissance approfondie des groupes de disponibilité Always On. Pour plus d'informations, consultez les pages suivantes :

* [Présentation des groupes de disponibilité SQL Server Always On sur des machines virtuelles Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Groupes de disponibilité SQL Server Always On (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Le fournisseur de ressources de l’adaptateur SQL prend en charge _uniquement_ SQL 2016 SP1 Enterprise ou les instances ultérieures pour Always On. Cette configuration de l’adaptateur requiert des nouvelles fonctionnalités SQL telles que l’amorçage automatique.

Outre la précédente liste d’exigences, vous devez activer l’[amorçage automatique](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) sur chaque groupe de disponibilité pour chaque instance de SQL Server.

Pour activer l’amorçage automatique sur toutes les instances, modifiez, puis exécutez la commande SQL suivante pour chaque instance :

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Sur les instances secondaires, modifiez, puis exécutez la commande SQL suivante pour chaque instance :

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Pour ajouter des serveurs d’hébergement Always On SQL

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.

2. Sélectionnez **Parcourir** &gt; **RESSOURCES ADMINISTRATIVES** &gt; **Serveurs d’hébergement SQL** &gt; **+Ajouter**.

   Sous **Serveurs d’hébergement SQL**, vous pouvez connecter le fournisseur de ressources SQL Server à des instances réelles de SQL Server qui font office de serveur principal du fournisseur de ressources.

3. Remplissez le formulaire avec les détails de connexion de votre instance de SQL Server. Assurez-vous que vous utilisez l’adresse de nom de domaine complet de l’écouteur Always On (et le numéro de port facultatif.) Fournissez les informations du compte que vous avez configuré avec des privilèges d’administrateur système.

4. Cochez la case Groupe de disponibilité Always On pour activer la prise en charge des instances de groupe de disponibilité Always On SQL.

   ![Activer Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Ajouter l’instance Always On SQL à une référence SKU.

   > [!IMPORTANT]
   > Vous ne pouvez pas mélanger des serveurs autonomes avec des instances Always On dans la même référence SKU. Toute tentative de mélange de types après l’ajout du premier serveur d’hébergement entraîne une erreur.

## <a name="make-the-sql-databases-available-to-users"></a>Mettre à disposition des bases de données SQL pour les utilisateurs

Créez des plans et des offres pour mettre des bases de données SQL à la disposition d’utilisateurs. Ajoutez le service **Microsoft.SqlAdapter** au plan, puis ajoutez le Quota par défaut ou créez un nouveau Quota.

![Créer des plans et des offres pour inclure des bases de données](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Étapes suivantes

[Ajouter des bases de données](azure-stack-sql-resource-provider-databases.md)
