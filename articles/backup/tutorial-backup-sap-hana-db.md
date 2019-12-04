---
title: Tutoriel - Sauvegarder des bases de données SAP HANA dans des machines virtuelles Azure
description: Dans ce tutoriel, découvrez comment sauvegarder des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure dans un coffre Recovery Services de Sauvegarde Azure.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: a622370fca3144aeb6a5d7c071c227b3c21cf135
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287186"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Didacticiel : Sauvegarder des bases de données SAP HANA dans une machine virtuelle Azure

Ce tutoriel vous explique comment sauvegarder des bases de données SAP HANA s’exécutant sur des machines virtuelles Azure dans un coffre Recovery Services de Sauvegarde Azure. Cet article porte sur les points suivants :

> [!div class="checklist"]
>
> * Créer et configurer un coffre
> * Découvrir des bases de données
> * Configurer des sauvegardes

[Voici](sap-hana-backup-support-matrix.md#scenario-support) tous les scénarios actuellement pris en charge.

## <a name="onboard-to-the-public-preview"></a>Intégration à la préversion publique

Exécutez la procédure d’intégration à la préversion publique ci-après :

* Dans le portail, inscrivez votre ID d’abonnement auprès du fournisseur de services Recovery Services en [suivant les instructions de cet article](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).

* Pour PowerShell, exécutez cette cmdlet. Ce processus doit s’achever en présentant la mention « Inscrit ».

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Prérequis

Avant de configurer les sauvegardes, prenez soin d’effectuer les opérations suivantes :

1. Sur la machine virtuelle qui exécute la base de données SAP HANA, installez et activez les packages de pilotes ODBC à partir du package/média SUSE Linux Enterprise Server (SLES) officiels à l’aide de zypper, en procédant comme suit :

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> Si vous ne mettez pas à jour les dépôts, vérifiez que la version d’unixODBC est au minimum 2.3.4. Pour connaître la version de uniXODBC, exécutez `odbcinst -j` en tant que racine
>

2. Autorisez la connectivité à Internet de la machine virtuelle pour permettre à cette dernière d’atteindre Azure, comme décrit dans la [procédure ci-dessous](#set-up-network-connectivity).

3. Exécutez le script de préinscription dans la machine virtuelle où HANA est installé en tant qu’utilisateur racine. [Ce script](https://aka.ms/scriptforpermsonhana) définira les [autorisations appropriées](#setting-up-permissions).

## <a name="set-up-network-connectivity"></a>Configurer la connectivité réseau

Pour toutes les opérations, la machine virtuelle SAP HANA nécessite une connectivité aux adresses IP publiques Azure. En l’absence d’une connectivité, les opérations de machine virtuelle (détection de bases de données, configuration de sauvegardes, planification de sauvegardes, restauration de points de récupération, etc.) échouent. Établissez la connectivité en autorisant l’accès aux plages d’adresses IP des centres de données Azure :

* Vous pouvez télécharger les [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) des centres de données Azure, puis autoriser l’accès à ces adresses.
* Si vous utilisez des Groupes de sécurité réseau (NSG), vous pouvez utiliser la [balise de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud pour autoriser toutes les adresses IP publiques Azure. Vous pouvez modifier les règles NSG à l’aide de la [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0).
* Le port 443 doit être ajouté à la liste verte puisque le transport s’effectue via HTTPS.

## <a name="setting-up-permissions"></a>Configurer les autorisations

Le script de préinscription effectue les actions suivantes :

1. Il crée AZUREWLBACKUPHANAUSER dans un système HANA et ajoute les rôles et autorisations requis :
   * DATABASE ADMIN : permet de créer de nouvelles bases de données pendant la restauration.
   * CATALOG READ : permet de lire le catalogue de sauvegarde.
   * SAP_INTERNAL_HANA_SUPPORT : permet d’accéder à certaines tables privées.
2. Ajoute une clé à Hdbuserstore pour le plug-in HANA afin de gérer toutes les opérations (requêtes de base de données, opérations de restauration, configuration et exécution de la sauvegarde).

Pour confirmer la création de la clé, exécutez la commande HDBSQL sur la machine HANA avec les informations d’identification SIDADM :

```hdbsql
hdbuserstore list
```

La sortie de commande doit afficher la clé {SID} {DBNAME} avec l’utilisateur en tant que AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Vérifiez que vous disposez d’un ensemble unique de fichiers SSFS sous /usr/sap/{SID}/home/.hdb/. Vous ne devez trouver qu’un seul dossier dans ce chemin d’accès.
>

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

* **Nom** : Le nom est utilisé pour identifier le coffre Recovery Services et doit être propre à l’abonnement Azure. Spécifiez un nom composé d’au moins deux caractères, mais sans dépasser 50 caractères. Il doit commencer par une lettre et ne peut être constitué que de lettres, chiffres et traits d’union. Pour ce tutoriel, nous avons utilisé le nom **SAPHanaVault**.
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
