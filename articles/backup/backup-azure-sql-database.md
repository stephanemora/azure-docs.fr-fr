---
title: Sauvegarder des bases de données SQL Server dans Azure | Microsoft Docs
description: Ce tutoriel explique comment sauvegarder SQL Server avec Azure, ainsi que la récupération de SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847297"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>À propos de la sauvegarde SQL Server sur des machines virtuelles Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. Vous pouvez sauvegarder les bases de données SQL Server qui s’exécutent sur les machines virtuelles Azure par l’intermédiaire de la [sauvegarde Azure](backup-overview.md).

## <a name="backup-process"></a>Processus de sauvegarde

Cette solution exploite les API natives de SQL pour effectuer des sauvegardes de vos bases de données SQL.

* Une fois que vous spécifiez la machine virtuelle SQL Server que vous voulez protéger et dont vous voulez interroger des bases de données, le service de Sauvegarde Azure installe une extension de sauvegarde de charge de travail sur la machine virtuelle par le nom d’extension `AzureBackupWindowsWorkload` .
* Cette extension se compose d’un coordinateur et d’un plug-in SQL. Alors que le coordinateur est responsable du déclenchement des flux de travail pour diverses opérations, comme le configuration de la sauvegarde, la sauvegarde et la restauration, le plug-in est responsable du flux de données réel.
* Pour pouvoir découvrir les bases de données sur cette machine virtuelle, la Sauvegarde Azure crée le compte `NT SERVICE\AzureWLBackupPluginSvc`. Ce compte est utilisé pour la sauvegarde et la restauration. Il doit disposer d’autorisations d’administrateur système SQL. La Sauvegarde Azure utilise le compte `NT AUTHORITY\SYSTEM`  pour la découverte et l’interrogation des bases de données. Ce compte doit donc être une connexion publique sur SQL. Si vous n’avez pas créé la machine virtuelle SQL Server à partir de la Place de marché Azure, vous pouvez recevoir une erreur  **UserErrorSQLNoSysadminMembership**. Si cela se produit, [suivez ces instructions](backup-azure-sql-database.md).
* Une fois que vous déclenchez la configuration de la protection sur les bases de données sélectionnées, le service de sauvegarde configure le coordinateur avec les planifications de sauvegarde et d’autres détails de stratégie, que l’extension met en cache localement sur la machine virtuelle. 
* À l’heure planifiée, le coordinateur communique avec le plug-in et démarre le streaming des données de sauvegarde à partir du serveur SQL avec l’infrastructure VDI.  
* Le plug-in envoie les données directement au coffre Recovery Services, ce qui élimine la nécessité d’un emplacement intermédiaire. Les données sont chiffrées et stockées par le service de Sauvegarde Azure dans des comptes de stockage.
* Une fois le transfert de données terminé, le coordinateur confirme la validation avec le service de sauvegarde.

  ![Architecture de sauvegarde SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, contrôlez les points suivants :

1. Vérifier qu’une instance SQL Server s’exécute dans Azure. Vous pouvez [rapidement créer une instance SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) dans la Place de marché.
2. Consultez les sections [Considérations relatives aux fonctionnalités](#feature-consideration-and-limitations) et [Prise en charge de scénarios](#scenario-support).
3. [Consulter les questions les plus fréquentes](faq-backup-sql-server.md) sur ce scénario.

## <a name="scenario-support"></a>Prise en charge du scénario

**Support** | **Détails**
--- | ---
**Déploiements pris en charge** | Les machines virtuelles Azure de la Place de marché SQL et les machines virtuelles autres que celles de la Place de marché (sur lesquelles SQL Server est installé manuellement) sont prises en charge.
**Zones géographiques prises en charge** | Australie Sud-Est (ASE), Australie Est (AE) <br> Brésil Sud (BRS)<br> Canada Centre (CNC), Canada Est (CE)<br> Asie Sud-Est (SEA), Asie Est (EA) <br> USA Est (EUS), USA Est 2 (EUS2), West Central US (WCUS), USA Ouest (WUS), USA Ouest 2 (WUS 2), USA Centre Nord (NCUS), USA Centre (CUS), USA Centre Sud (SCUS) <br> Inde Centre (INC), Inde Sud (INS) <br> Japon Est (JPE), Japon Ouest (JPW) <br> Corée Centre (KRC), Corée Sud (KRS) <br> Europe Nord (NE), Europe Ouest (WE) <br> Royaume-Uni Sud (UKS), Royaume-Uni Ouest (UKW)
**Systèmes d’exploitation pris en charge** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux n’est pas actuellement pris en charge.
**Versions de SQL Server prises en charge** | SQL Server 2017 ; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versions .NET prises en charge** | .NET Framework 4.5.2 et versions ultérieures installées sur la machine virtuelle

## <a name="feature-consideration-and-limitations"></a>Considérations et limitations relatives aux fonctionnalités

- La sauvegarde SQL Server peut être configurée dans le portail Azure ou **PowerShell**. Nous ne prenons pas en charge l’interface CLI.
- La machine virtuelle exécutant SQL Server nécessite une connexion Internet pour accéder aux adresses IP publiques Azure.
- L’**instance de cluster de basculement (FCI)** SQL Server et l’instance de cluster de basculement SQL Server Always On ne sont pas prises en charge.
- Les opérations de sauvegarde et de restauration des bases de données miroirs et des instantanés de base de données ne sont pas prises en charge.
- L’utilisation de plusieurs solutions de sauvegarde pour sauvegarder votre instance SQL Server autonome ou votre groupe de disponibilité SQL AlwaysOn peut entraîner l’échec de la sauvegarde. Évitez de le faire.
- La sauvegarde de deux nœuds d’un groupe de disponibilité individuellement avec les mêmes solutions ou des solutions différentes peut également entraîner l’échec de la sauvegarde. La Sauvegarde Azure peut détecter et protéger tous les nœuds se trouvant dans la même région que le coffre. Si votre groupe de disponibilité SQL Server AlwaysOn s’étend sur plusieurs régions Azure, configurez la sauvegarde à partir de la région où se trouve le nœud principal. La Sauvegarde Azure peut découvrir et protéger toutes les bases de données du groupe de disponibilité en fonction de votre préférence de sauvegarde.  
- La Sauvegarde Azure prend uniquement en charge les types de sauvegarde Complète et Copie complète uniquement pour les bases de données **en lecture seule**.
- Les bases de données comprenant un grand nombre de fichiers ne peuvent pas être protégées. Le nombre maximal de fichiers pris en charge est d’**environ 1 000**.  
- Vous pouvez sauvegarder jusqu’à **environ 2 000** bases de données SQL Server dans un coffre. Vous pouvez créer plusieurs coffres au cas où vous auriez un plus grand nombre de bases de données.
- Vous pouvez configurer la sauvegarde pour au maximum **50** bases de données en une seule fois. Cette restriction permet d’optimiser les charges de sauvegardes.
- Nous prenons en charge une taille maximale de **2 To**. Pour les tailles supérieures, des problèmes de performance peuvent survenir.
- Pour avoir une idée du nombre de bases de données qui peuvent être protégées par serveur, nous devons tenir compte de facteurs tels que la bande passante, la taille de la machine virtuelle, la fréquence des sauvegardes, la taille des bases de données, etc. Nous travaillons sur un planificateur qui vous aiderait à calculer vous-mêmes ces valeurs. Nous le publierons prochainement.
- Dans le cas des groupes de disponibilité, les sauvegardes sont effectuées à partir des différents nœuds en fonction de plusieurs facteurs. Le comportement des sauvegardes pour un groupe de disponibilité est récapitulé ci-dessous.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Comportement des sauvegardes dans le cas des groupes de disponibilité AlwaysOn

En fonction de la préférence de sauvegarde et des types de sauvegardes (complète/différentielle/de fichier journal/copie complète uniquement), les sauvegardes sont effectuées à partir d’un nœud particulier (principal/secondaire).

- **Préférence de sauvegarde : Primaire**

**Type de sauvegarde** | **Node**
    --- | ---
    Complet | Primaire
    Différentielle | Primaire
    Journal |  Primaire
    Copie complète uniquement |  Primaire

- **Préférence de sauvegarde : Secondaire uniquement**

**Type de sauvegarde** | **Node**
--- | ---
Complet | Primaire
Différentielle | Primaire
Journal |  Secondaire
Copie complète uniquement |  Secondaire

- **Préférence de sauvegarde : Secondaire**

**Type de sauvegarde** | **Node**
--- | ---
Complet | Primaire
Différentielle | Primaire
Journal |  Secondaire
Copie complète uniquement |  Secondaire

- **Pas de préférence de sauvegarde**

**Type de sauvegarde** | **Node**
--- | ---
Complet | Primaire
Différentielle | Primaire
Journal |  Secondaire
Copie complète uniquement |  Secondaire

## <a name="fix-sql-sysadmin-permissions"></a>Correction des autorisations d’administrateur système SQL

  Si vous devez corriger des autorisations en raison d’une erreur **UserErrorSQLNoSysadminMembership**, procédez comme suit :

  1. Utilisez un compte disposant d’autorisations d’administrateur système SQL Server pour vous connecter à SQL Server Management Studio (SSMS). À moins que vous ayez besoin d’autorisations spéciales, l’authentification Windows doit fonctionner.
  2. Sur le serveur SQL Server, ouvrez le dossier **Security/Logins**.

      ![Ouvrir le dossier Security/Logins pour afficher les comptes](./media/backup-azure-sql-database/security-login-list.png)

  3. Cliquez avec le bouton droit sur le dossier **Logins** et sélectionnez **Nouvelle connexion**. Dans **Nouvelle connexion**, sélectionnez **Rechercher**.

      ![Dans la boîte de dialogue Connexion - Nouveau, sélectionnez Rechercher](./media/backup-azure-sql-database/new-login-search.png)

  4. Le compte de service virtuel Windows **NT SERVICE\AzureWLBackupPluginSvc** a été créé pendant la phase d’inscription de la machine virtuelle et de découverte SQL. Entrez le nom du compte, comme indiqué dans **Entrez le nom de l’objet à sélectionner**. Sélectionnez **Vérifier les noms** pour résoudre le nom. Cliquez sur **OK**.

      ![Cliquer sur Vérifier les noms pour résoudre le nom de service inconnu](./media/backup-azure-sql-database/check-name.png)

  5. Dans **Rôles du serveur**, assurez-vous que le rôle **sysadmin** (administrateur système) est sélectionné. Cliquez sur **OK**. Les autorisations requises doivent désormais exister.

      ![S’assurer que le rôle serveur administrateur système est sélectionné](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. À présent, associez la base de données avec le coffre Recovery Services. Dans la liste **Serveurs protégés** du portail Azure, cliquez avec le bouton droit sur le serveur en erreur > **Redécouvrir les bases de données**.

      ![Vérifier que le serveur dispose des autorisations appropriées](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Vérifiez la progression dans la zone **Notifications**. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

      ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur](backup-sql-server-database-azure-vms.md) la sauvegarde de bases de données SQL Server.
- [En savoir plus sur](restore-sql-database-azure-vm.md) la restauration des bases de données SQL Server sauvegardées.
- [En savoir plus sur](manage-monitor-sql-database-backup.md) la gestion des bases de données SQL Server sauvegardées.
