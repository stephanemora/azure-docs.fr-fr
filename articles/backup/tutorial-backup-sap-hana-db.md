---
title: Tutoriel - Sauvegarder des bases de données SAP HANA dans des machines virtuelles Azure
description: Dans ce tutoriel, découvrez comment sauvegarder des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure dans un coffre Recovery Services de Sauvegarde Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 31958a4d4e3af4f747ab2f9de7b1bc67560e87d7
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248241"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutoriel : Sauvegarder des bases de données SAP HANA dans une machine virtuelle Azure

Ce tutoriel vous explique comment sauvegarder des bases de données SAP HANA s’exécutant sur des machines virtuelles Azure dans un coffre Recovery Services de Sauvegarde Azure. Cet article porte sur les points suivants :

> [!div class="checklist"]
>
> * Créer et configurer un coffre
> * Découvrir des bases de données
> * Configurer des sauvegardes

[Voici](sap-hana-backup-support-matrix.md#scenario-support) tous les scénarios actuellement pris en charge.

>[!NOTE]
>[Démarrez](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) avec la sauvegarde SAP HANA en préversion pour RHEL (7.4, 7.6, 7.7 ou 8.1). Pour d’autres questions, écrivez-nous à l’adresse [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

## <a name="prerequisites"></a>Prérequis

Avant de configurer les sauvegardes, prenez soin d’effectuer les opérations suivantes :

* Autorisez la connectivité de la machine virtuelle à Internet pour lui permettre d’atteindre Azure comme décrit dans la procédure [Configurer la connectivité réseau](#set-up-network-connectivity) ci-dessous.
* Le **hdbuserstore** doit inclure une clé qui respecte les critères suivants :
  * Elle doit être présente dans le **hdbuserstore** par défaut.
  * Pour MDC, la clé doit pointer vers le port SQL de **NAMESERVER**. Pour SDC, elle doit pointer vers le port SQL de **INDEXSERVER**.
  * Elle doit disposer des informations d’identification nécessaires pour ajouter et supprimer des utilisateurs.
* Exécutez le script de configuration de sauvegarde SAP HANA (script de préinscription) dans la machine virtuelle où HANA est installé en tant qu’utilisateur racine. [Ce script](https://aka.ms/scriptforpermsonhana) prépare le système HANA pour la sauvegarde. Pour en savoir plus sur le script de préinscription, reportez-vous à la section [Ce que fait le script de préinscription](#what-the-pre-registration-script-does).

>[!NOTE]
>Le script de préinscription installe **compat-unixODBC234** pour les charges de travail SAP HANA s’exécutant sur RHEL (7.4, 7.6 et 7.7) et **unixODBC** pour RHEL 8.1. [Ce package se trouve dans le dépôt des services de mise à jour de RHEL for SAP HANA (pour RHEL 7 Server) pour les solutions SAP (RPM)](https://access.redhat.com/solutions/5094721).  Pour l’image RHEL de la Place de marché Azure, le dépôt est **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>Configurer la connectivité réseau

Pour toutes les opérations, la machine virtuelle SAP HANA nécessite une connectivité aux adresses IP publiques Azure. Les opérations de machine virtuelle (détection de bases de données, configuration de sauvegardes, sauvegardes planifiées, restauration des points de récupération, etc.) échouent en cas d’absence de connexion aux adresses IP publiques Azure.

Établissez la connectivité en utilisant l’une des options suivantes :

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Autoriser les plages d’adresses IP du centre de données Azure

Cette option autorise les [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) dans le fichier téléchargé. Pour accéder à un groupe de sécurité réseau, utilisez l’applet de commande Set-AzureNetworkSecurityRule. Si votre liste de destinataires sûrs ne comporte que des adresses IP spécifiques à une région, vous devez également mettre à jour la liste des destinataires sûrs avec la balise du service Azure Active Directory (Azure AD) pour activer l’authentification.

### <a name="allow-access-using-nsg-tags"></a>Autoriser l’accès à l’aide de balises de groupe de sécurité réseau

Si vous utilisez NSG pour limiter la connectivité, vous devez utiliser la balise de service Sauvegarde Azure pour autoriser l’accès sortant à la Sauvegarde Azure. Vous devez également utiliser des [règles](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pour Azure AD et Stockage Azure afin de permettre la connectivité pour l’authentification et le transfert de données. Cette opération peut être effectuée à partir du portail Azure ou via PowerShell.

Pour créer une règle avec le portail :

  1. Dans **Tous les services**, accédez à**Groupes de sécurité réseau** et sélectionnez le groupe de sécurité réseau.
  2. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic sortant**.
  3. Sélectionnez **Ajouter**. Entrez toutes les informations nécessaires à la création d’une nouvelle règle, comme décrit dans [paramètres de règle de sécurité](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Vérifiez que l'option **Destination** est définie sur **Balise de service** et **Balise de service de destination** sur **AzureBackup**.
  4. Cliquez sur **Ajouter**  pour enregistrer la règle de sécurité de trafic sortant que vous venez de créer.

Pour créer une règle à l’aide de Powershell :

 1. Ajoutez les identifiants de compte Azure et mettez à jour les clouds nationaux<br/>
      `Add-AzureRmAccount`<br/>

 2. Sélectionnez l’abonnement au groupe de sécurité réseau<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Sélectionnez le groupe de sécurité réseau<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Ajoutez la règle de trafic sortant autorisée pour la balise du service Sauvegarde Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Ajoutez la règle de trafic sortant autorisée pour la balise du service Stockage<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Ajoutez la règle de trafic sortant autorisée pour la balise du service AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Enregistrez le groupe de sécurité réseau<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Autorisez l’accès à l’aide de balises de Pare-feu Azure**. Si vous utilisez Pare-feu Azure, créez une règle d’application en utilisant la balise [FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags) d’AzureBackup. Cela autorise l’accès sortant à Sauvegarde Azure.

**Déployez un serveur proxy HTTP pour le routage du trafic**. Lorsque vous sauvegardez une base de données SAP HANA sur une machine virtuelle Azure, l’extension de sauvegarde sur la machine virtuelle utilise les API HTTPS pour envoyer des commandes de gestion à Sauvegarde Azure, et des données à Stockage Azure. L’extension de sauvegarde utilise également Azure AD pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. Les extensions sont le seul composant configuré pour l’accès à l’internet public.

Les options de connectivité présentent les avantages et inconvénients suivants :

**Option** | **Avantages** | **Inconvénients**
--- | --- | ---
Autoriser les plages d’adresses IP | Aucun coût supplémentaire | Difficile à gérer, car les plages d’adresses IP changent au fil du temps <br/><br/> Fournit un accès à l’ensemble d’Azure et pas seulement à Stockage Azure
Utiliser les balises du service NSG | Plus faciles à gérer car les modifications apportées à la plage sont fusionnées automatiquement <br/><br/> Aucun coût supplémentaire <br/><br/> | Peut être utilisé uniquement avec les groupes de sécurité réseau <br/><br/> Fournit l’accès à l’ensemble du service
Utiliser les balises FQDN du Pare-feu Azure | Plus faciles à gérer car les noms de domaine complet sont managés automatiquement | Utilisabes avec Pare-feu Azure uniquement
Utiliser un proxy HTTP | Le contrôle granulaire dans le proxy sur les URL de stockage est autorisé <br/><br/> Un seul point d’accès Internet aux machines virtuelles <br/><br/> Non soumis aux modifications d’adresse IP Azure | Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy

## <a name="what-the-pre-registration-script-does"></a>Ce que fait le script de préinscription

Le script de préinscription assure les fonctions suivantes :

* Il installe ou met à jour tous les packages nécessaires à l’agent de Sauvegarde Azure sur votre distribution.
* Il effectue les vérifications de connectivité réseau sortante avec les serveurs de Sauvegarde Azure et les services dépendants comme Azure Active Directory et Stockage Azure.
* Il se connecte à votre système HANA à l’aide de la clé utilisateur figurant dans les [prérequis](#prerequisites). La clé utilisateur permet de créer un utilisateur de sauvegarde (AZUREWLBACKUPHANAUSER) dans le système HANA et peut être supprimée dès lors que le script de préinscription a été correctement exécuté.
* AZUREWLBACKUPHANAUSER reçoit les rôles et autorisations nécessaires suivants :
  * DATABASE ADMIN (dans le cas de MDC) et BACKUP ADMIN (dans le cas de SDC) : pour créer des bases de données lors de la restauration.
  * CATALOG READ : permet de lire le catalogue de sauvegarde.
  * SAP_INTERNAL_HANA_SUPPORT : permet d’accéder à certaines tables privées.
* Le script ajoute une clé à **hdbuserstore** pour AZUREWLBACKUPHANAUSER afin que le plug-in de sauvegarde HANA gère toutes les opérations (requêtes de base de données, opérations de restauration, configuration et exécution de la sauvegarde).

>[!NOTE]
> Vous pouvez transmettre explicitement la clé utilisateur indiquée dans le cadre des [prérequis](#prerequisites) en tant que paramètre au script de pré-inscription : `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Pour connaître les autres paramètres acceptés par le script, utilisez la commande `bash msawb-plugin-config-com-sap-hana.sh --help`

Pour confirmer la création de la clé, exécutez la commande HDBSQL sur la machine HANA avec les informations d’identification SIDADM :

```hdbsql
hdbuserstore list
```

La sortie de commande doit afficher la clé {SID} {DBNAME} avec l’utilisateur en tant que AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Vérifiez que vous disposez d’un ensemble unique de fichiers SSFS sous `/usr/sap/{SID}/home/.hdb/`. Vous ne devez trouver qu’un seul dossier dans ce chemin d’accès.

## <a name="create-a-recovery-service-vault"></a>Créer un coffre Recovery Services

Un coffre Recovery Services est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre Recovery Services contient également les stratégies de sauvegarde associées aux machines virtuelles protégées.

Pour créer un archivage de Recovery Services :

1. Connectez-vous à votre abonnement sur le [portail Azure](https://portal.azure.com/).

2. Dans le menu de gauche, sélectionnez **Tous les services**

   ![Sélectionner Tous les services](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Dans la boîte de dialogue **Tous les services**, entrez **Recovery Services**. Liste des filtres de ressources variant en fonction de votre entrée. Dans la liste des ressources, sélectionnez **Coffres Recovery Services**.

   ![Sélectionner Coffres Recovery Services](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Dans le tableau de bord **Coffres Recovery Services**, sélectionnez **Ajouter**.

   ![Ajouter un coffre Recovery Services](./media/tutorial-backup-sap-hana-db/add-vault.png)

   La boîte de dialogue **Coffre Recovery Services** s’ouvre. Attribuez des valeurs aux champs **Nom, Abonnement, Groupe de ressources** et **Emplacement**

   ![Créer un coffre Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name** : Le nom est utilisé pour identifier le coffre Recovery Services et doit être propre à l’abonnement Azure. Spécifiez un nom composé d’au moins deux caractères, mais sans dépasser 50 caractères. Il doit commencer par une lettre et ne peut être constitué que de lettres, chiffres et traits d’union. Pour ce tutoriel, nous avons utilisé le nom **SAPHanaVault**.
   * **Abonnement**: choisissez l’abonnement à utiliser. Si vous êtes membre d’un seul abonnement, son nom s’affiche. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure. Ici, nous avons utilisé l’abonnement de **laboratoire de solution SAP HANA**.
   * **Groupe de ressources** : Utilisez un groupe de ressources existant ou créez-en un. Ici, nous avons utilisé **SAPHANADemo**.<br>
   Pour afficher la liste des groupes de ressources disponibles dans votre abonnement, sélectionnez **Utiliser existant**, puis sélectionnez une ressource dans la zone de liste déroulante. Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom. Pour obtenir des informations complètes sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Emplacement** : sélectionnez la région géographique du coffre. Le coffre doit se trouver dans la même région que la machine virtuelle exécutant SAP HANA. Nous avons utilisé **USA Est 2**.

5. Sélectionnez **Vérifier + créer**.

   ![Sélectionner Vérifier + créer](./media/tutorial-backup-sap-hana-db/review-create.png)

Le coffre Recovery Services est maintenant créé.

## <a name="discover-the-databases"></a>Détecter les bases de données

1. Dans le coffre, à la section **Prise en main**, cliquez sur **Sauvegarde**. Dans la zone **Où s’exécute votre charge de travail ?** , sélectionnez **SAP HANA dans les machines virtuelles Azure**.
2. Cliquez sur **Démarrer la découverte**. Cette opération lance la détection des machines virtuelles Linux non protégées dans la région du coffre. Vous verrez la machine virtuelle Azure que vous souhaitez protéger.
3. Dans **Sélectionner les machines virtuelles**, cliquez sur le lien pour télécharger le script qui autorise le service Sauvegarde Azure à accéder aux machines virtuelles SAP HANA pour la découverte des bases de données.
4. Exécutez le script sur la machine virtuelle hébergeant les bases de données SAP HANA que vous souhaitez sauvegarder.
5. Après avoir exécuté le script sur la machine virtuelle, dans **Sélectionner les machines virtuelles**, sélectionnez la machine virtuelle. Ensuite, cliquez sur **Découvrir les bases de données**.
6. Le service Sauvegarde Azure détecte toutes les bases de données SAP HANA résidant sur la machine virtuelle. Lors de la détection, le service Sauvegarde Azure inscrit la machine virtuelle auprès du coffre et y installe une extension. Aucun agent n’est installé sur la base de données.

   ![Détecter les bases de données](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurer une sauvegarde

Maintenant que les bases de données à sauvegarder sont découvertes, nous allons activer la sauvegarde.

1. Cliquez sur **Configurer la sauvegarde**.

   ![Configurer une sauvegarde](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Dans **Sélectionner les éléments à sauvegarder**, sélectionnez une ou plusieurs bases de données à protéger, puis cliquez sur **OK**.

   ![Sélectionner les éléments à sauvegarder](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Dans **Stratégie de sauvegarde > Choisir une stratégie de sauvegarde**, créez une stratégie de sauvegarde pour les bases de données en suivant les instructions de la section suivante.

   ![Choisir une stratégie de sauvegarde](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Après avoir créé la stratégie, dans le menu **Sauvegarde**, cliquez sur **Activer la sauvegarde**.

   ![Activer la sauvegarde](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Vous pouvez suivre la progression de la configuration de la sauvegarde dans la zone **Notifications** du portail.

## <a name="creating-a-backup-policy"></a>Création d’une stratégie de sauvegarde

Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées, ainsi que leur durée de rétention.

* Une stratégie est créée au niveau du coffre.
* Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.

Spécifiez les paramètres de stratégie comme suit :

1. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie. Dans le cas présent, entrez **SAPHANA**.

   ![Entrer le nom de la nouvelle stratégie](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Dans **Stratégie de sauvegarde complète**, sélectionnez une **Fréquence de sauvegarde**. Vous pouvez choisir **Quotidienne** ou **Hebdomadaire**. Pour ce tutoriel, nous avons choisi la sauvegarde **Quotidienne**.

   ![Sélectionner une fréquence de sauvegarde](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Dans **Durée de rétention**, configurez les paramètres de rétention pour la sauvegarde complète.
   * Par défaut, toutes les options sont sélectionnées. Désactivez les limites de durée de rétention que vous ne souhaitez pas utiliser et définissez celles qui vous intéressent.
   * La période de rétention minimale est de sept jours pour tous les types de sauvegardes (complète/différentielle/fichier journal).
   * Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
   * La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée de rétention hebdomadaire et aux paramètres.
   * Les durées de rétention mensuelle et annuelle ont le même comportement.
4. Dans le menu de stratégie **Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
5. Sélectionnez ensuite **Sauvegarde différentielle** pour ajouter une stratégie différentielle.
6. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention. Nous avons activé une sauvegarde différentielle chaque **dimanche** à **2:00**, qui est conservée pendant **30 jours**.

   ![Stratégie de sauvegarde différentielle](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Pour l’instant, les sauvegardes incrémentielles ne sont pas prises en charge.
   >

7. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu **Stratégie de sauvegarde** principal.
8. Sélectionnez **Sauvegarde de fichier journal** pour ajouter une stratégie de sauvegarde de fichier journal.
   * L’option **Sauvegarde de fichier journal** est définie par défaut sur **Activer**. Cette option ne peut pas être désactivée, car SAP HANA gère toutes les sauvegardes de fichiers journaux.
   * Nous avons défini **2 heures** comme planification de sauvegarde et **15 jours** de période de rétention.

    ![Stratégie de sauvegarde de fichier journal](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Les sauvegardes de fichiers journaux ne commencent à se produire qu’en cas de réussite d’une sauvegarde complète.
   >

9. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu **Stratégie de sauvegarde** principal.
10. Après avoir défini la stratégie de sauvegarde, cliquez sur **OK**.

Vous avez maintenant configuré les sauvegardes de vos bases de données SAP HANA.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [exécuter des sauvegardes à la demande sur des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Découvrez comment [restaurer des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](sap-hana-db-restore.md)
* Découvrez comment [gérer des bases de données SAP HANA sauvegardées à l’aide de Sauvegarde Azure](sap-hana-db-manage.md)
* Découvrez comment [résoudre les problèmes courants lors de la sauvegarde de bases de données SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
