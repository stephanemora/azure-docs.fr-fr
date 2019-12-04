---
title: Sauvegarder des bases de données SQL Server sur Azure
description: Cet article explique comment sauvegarder SQL Server avec Azure, ainsi que la récupération de SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 39f2348a95be95a03dada45d48952dce99ec4ec7
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462592"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>À propos de la sauvegarde SQL Server sur des machines virtuelles Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. Vous pouvez sauvegarder les bases de données SQL Server qui s’exécutent sur les machines virtuelles Azure en utilisant la [Sauvegarde Azure](backup-overview.md).

## <a name="backup-process"></a>Processus de sauvegarde

Cette solution exploite les API natives de SQL pour effectuer des sauvegardes de vos bases de données SQL.

* Une fois que vous avez spécifié la machine virtuelle SQL Server que vous voulez protéger et dont vous voulez interroger des bases de données, le service Sauvegarde Azure installe une extension de sauvegarde de charge de travail sur la machine virtuelle nommée `AzureBackupWindowsWorkload`.
* Cette extension se compose d’un coordinateur et d’un plug-in SQL. Alors que le coordinateur est responsable du déclenchement des flux de travail pour diverses opérations, comme le configuration de la sauvegarde, la sauvegarde et la restauration, le plug-in est responsable du flux de données réel.
* Pour pouvoir découvrir les bases de données sur cette machine virtuelle, Sauvegarde Azure crée le compte `NT SERVICE\AzureWLBackupPluginSvc`. Ce compte est utilisé pour la sauvegarde et la restauration. Il doit disposer d’autorisations d’administrateur système SQL. Le compte `NT SERVICE\AzureWLBackupPluginSvc` est un [compte de service virtuel](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts) et ne nécessite donc pas de gestion des mots de passe. Sauvegarde Azure utilise le compte `NT AUTHORITY\SYSTEM` pour la découverte et l’interrogation des bases de données. Ce compte doit donc être une connexion publique sur SQL. Si vous n’avez pas créé la machine virtuelle SQL Server à partir de la Place de marché Azure, vous pouvez recevoir une erreur **UserErrorSQLNoSysadminMembership**. Si cela se produit, [suivez ces instructions](#set-vm-permissions).
* Lorsque vous déclenchez la configuration de la protection sur les bases de données sélectionnées, le service de sauvegarde configure le coordinateur avec les planifications de sauvegarde et d’autres détails de stratégie, ce que l’extension met en cache localement sur la machine virtuelle.
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
**Zones géographiques prises en charge** | Australie Sud-Est (ASE), Australie Est (AE), Australie Centre (AC), Australie Centre 2 (AC) <br> Brésil Sud (BRS)<br> Canada Centre (CNC), Canada Est (CE)<br> Asie Sud-Est (SEA), Asie Est (EA) <br> USA Est (EUS), USA Est 2 (EUS2), West Central US (WCUS), USA Ouest (WUS), USA Ouest 2 (WUS 2), USA Centre Nord (NCUS), USA Centre (CUS), USA Centre Sud (SCUS) <br> Inde Centre (INC), Inde Sud (INS), Inde Ouest <br> Japon Est (JPE), Japon Ouest (JPW) <br> Corée Centre (KRC), Corée Sud (KRS) <br> Europe Nord (NE), Europe Ouest (WE) <br> Royaume-Uni Sud (UKS), Royaume-Uni Ouest (UKW) <br> US Gov Arizona, US Gov Virginie, US Gov Texas, US DoD Centre, US DoD Est <br> Allemagne Nord, Allemagne Centre-Ouest <br> Suisse Nord, Suisse Ouest <br> France Centre <br> Chine Est, Chine Est 2, Chine Nord, Chine Nord 2
**Systèmes d’exploitation pris en charge** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux n’est pas actuellement pris en charge.
**Versions de SQL Server prises en charge** | SQL Server 2019, SQL Server 2017 comme indiqué dans la [page Rechercher le cycle de vie des produits](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 et différents SP comme indiqué dans la [page Rechercher le cycle de vie des produits](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versions .NET prises en charge** | .NET Framework 4.5.2 ou ultérieur installé sur la machine virtuelle

## <a name="feature-consideration-and-limitations"></a>Considérations et limitations relatives aux fonctionnalités

* La sauvegarde SQL Server peut être configurée dans le portail Azure ou **PowerShell**. Nous ne prenons pas en charge l’interface CLI.
* La solution est prise en charge pour les deux types de [déploiements](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) : machines virtuelles Azure Resource Manager et machines virtuelles classiques.
* La machine virtuelle exécutant SQL Server nécessite une connexion Internet pour accéder aux adresses IP publiques Azure.
* L’**instance de cluster de basculement** SQL Server n’est pas prise en charge.
* Les opérations de sauvegarde et de restauration des bases de données miroir et des instantanés de base de données ne sont pas prises en charge.
* L’utilisation de plusieurs solutions de sauvegarde pour sauvegarder votre instance SQL Server autonome ou votre groupe de disponibilité SQL AlwaysOn peut entraîner l’échec de la sauvegarde. Une telle utilisation est donc à éviter.
* La sauvegarde de deux nœuds d’un groupe de disponibilité individuellement avec les mêmes solutions ou des solutions différentes peut également entraîner l’échec de la sauvegarde.
* La Sauvegarde Azure prend uniquement en charge les types de sauvegarde Complète et Copie complète uniquement pour les bases de données **en lecture seule**.
* Les bases de données comprenant un grand nombre de fichiers ne peuvent pas être protégées. Le nombre maximal de fichiers pris en charge est d’**environ 1 000**.  
* Vous pouvez sauvegarder jusqu’à **environ 2 000** bases de données SQL Server dans un coffre. Vous pouvez créer plusieurs coffres au cas où vous auriez un plus grand nombre de bases de données.
* Vous pouvez configurer la sauvegarde pour au maximum **50** bases de données en une seule fois. Cette restriction permet d’optimiser les charges de sauvegardes.
* Nous prenons en charge les bases de données d’une taille maximale de **2 To**. Pour les tailles supérieures, des problèmes de performance peuvent survenir.
* Pour avoir une idée du nombre de bases de données qui peuvent être protégées par serveur, nous devons tenir compte de facteurs tels que la bande passante, la taille de la machine virtuelle, la fréquence des sauvegardes, la taille des bases de données, etc. [Téléchargez](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) le planificateur de ressources qui donne le nombre approximatif de bases de données que vous pouvez avoir par serveur en fonction des ressources de la machine virtuelle et de la stratégie de sauvegarde.
* Dans le cas des groupes de disponibilité, les sauvegardes sont effectuées à partir des différents nœuds en fonction de plusieurs facteurs. Le comportement des sauvegardes pour un groupe de disponibilité est récapitulé ci-dessous.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Comportement des sauvegardes dans le cas des groupes de disponibilité AlwaysOn

Dans un groupe de disponibilité, il est recommandé de ne configurer une sauvegarde que sur un seul nœud. Les sauvegardes doivent toujours être configurées dans la même région que le nœud principal. En d’autres termes, le nœud principal doit toujours se trouver dans la région où vous configurez la sauvegarde. Si tous les nœuds du groupe de disponibilité se trouvent dans la même région où la sauvegarde est configurée, cela ne pose pas de problème.

#### <a name="for-cross-region-ag"></a>Groupe de disponibilité interrégional

* Quelle que soit la préférence de sauvegarde, les sauvegardes ne peuvent pas être effectuées sur les nœuds qui ne se trouvent pas dans la région où la sauvegarde est configurée. Ceci est dû au fait que les sauvegardes interrégionales ne sont pas prises en charge. Si vous ne disposez que de deux nœuds et que le nœud secondaire se trouve dans l’autre région, les sauvegardes continueront alors à s’effectuer sur le nœud principal (sauf si votre préférence de sauvegarde est « Secondaire uniquement »).
* Si un basculement se produit vers une région différente de celle dans laquelle la sauvegarde est configurée, les sauvegardes échouent sur les nœuds de la région ayant basculé.

En fonction de la préférence de sauvegarde et des types de sauvegardes (complète/différentielle/de fichier journal/copie complète uniquement), les sauvegardes sont effectuées à partir d’un nœud particulier (principal/secondaire).

* **Préférence de sauvegarde : Primaire**

**Type de sauvegarde** | **Node**
    --- | ---
    Complet | Primaire
    Différentielle | Primaire
    Journal |  Primaire
    Copie complète uniquement |  Primaire

* **Préférence de sauvegarde : Secondaire uniquement**

**Type de sauvegarde** | **Node**
--- | ---
Complet | Primaire
Différentielle | Primaire
Journal |  Secondaire
Copie complète uniquement |  Secondaire

* **Préférence de sauvegarde : Secondaire**

**Type de sauvegarde** | **Node**
--- | ---
Complet | Primaire
Différentielle | Primaire
Journal |  Secondaire
Copie complète uniquement |  Secondaire

* **Pas de préférence de sauvegarde**

**Type de sauvegarde** | **Node**
--- | ---
Complet | Primaire
Différentielle | Primaire
Journal |  Secondaire
Copie complète uniquement |  Secondaire

## <a name="set-vm-permissions"></a>Définir des autorisations de machine virtuelle

  Lorsque vous exécutez la découverte sur un serveur SQL Server, Sauvegarde Azure effectue les opérations suivantes :

* Ajoute l’extension AzureBackupWindowsWorkload.
* Crée un compte NT SERVICE\AzureWLBackupPluginSvc pour découvrir les bases de données sur la machine virtuelle. Ce compte est utilisé pour la sauvegarde et la restauration, et doit disposer d’autorisations d’administrateur système SQL.
* Découvre les bases de données en cours d’exécution sur une machine virtuelle ; Sauvegarde Azure utilise le compte NT AUTHORITY\SYSTEM. Ce compte doit être une connexion publique sur SQL.

Si vous n’avez pas créé la machine virtuelle SQL Server dans la Place de marché Azure, ou si vous êtes sur SQL 2008 et 2008 R2, vous pouvez recevoir une erreur **UserErrorSQLNoSysadminMembership**.

Pour accorder des autorisations dans le cas de **SQL 2008** et **2008 R2** s’exécutant sur Windows 2008 R2, voir [ici](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Pour toutes les autres versions, corrigez les autorisations en procédant comme suit :

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

> [!NOTE]
> Si plusieurs instances de SQL Server sont installées sur votre serveur SQL Server, vous devez ajouter l’autorisation sysadmin du compte **NT Service\AzureWLBackupPluginSvc** à toutes les instances SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Accorder des autorisations d’administrateur système SQL pour SQL 2008 et SQL 2008 R2

Ajouter des connexions **NT AUTHORITY\SYSTEM** et **NT Service\AzureWLBackupPluginSvc** à l’Instance SQL Server :

1. Accédez à l’Instance SQL Server dans l’Explorateur d’objets.
2. Accédez à Sécurité -> Connexions
3. Cliquez avec le bouton droit sur Connexions, puis sélectionnez *Nouvelle connexion*.

    ![Nouvelle connexion à l’aide de SSML](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Accédez à l’onglet Général et entrez **NT AUTHORITY\SYSTEM** en tant que nom de connexion.

    ![nom de connexion pour SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Accédez à *Rôles de serveur*, puis choisissez les rôles *public* et *sysadmin*.

    ![choix de rôles dans SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Cliquez sur *État*. *Accordez* l’autorisation de se connecter au moteur de base de données et de se connecter en tant que *Activé*.

    ![Accorder des autorisations dans SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Cliquez sur OK.
8. Répétez la même séquence d’étapes (1 à 7 ci-dessus) pour ajouter la connexion NT Service\AzureWLBackupPluginSvc à l’instance SQL Server. Si la connexion existe, assurez-vous qu’elle a le rôle serveur sysadmin et que, sous État, l’autorisation lui est accordée de se connecter au moteur de base de données et de se connecter en tant que Activé.
9. Une fois l’autorisation octroyée, **redécouvrez les bases de données** dans le portail : Coffre **->** Infrastructure de sauvegarde **->** Charge de travail dans machine virtuelle Azure :

    ![Redécouvrir les bases de données dans le portail Azure](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Vous pouvez aussi automatiser l’octroi d’autorisations en exécutant les commandes PowerShell suivantes en mode Administrateur. Le nom d’instance a la valeur MSSQLSERVER par défaut. Modifiez l’argument de nom d’instance dans le script si nécessaire :

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur](backup-sql-server-database-azure-vms.md) la sauvegarde de bases de données SQL Server.
* [En savoir plus sur](restore-sql-database-azure-vm.md) la restauration des bases de données SQL Server sauvegardées.
* [En savoir plus sur](manage-monitor-sql-database-backup.md) la gestion des bases de données SQL Server sauvegardées.
