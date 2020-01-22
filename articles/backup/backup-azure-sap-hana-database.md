---
title: Sauvegarder une base de données SAP HANA sur Azure avec Sauvegarde Azure
description: Dans cet article, découvrez comment sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: c5df198d009f0d4a9f37a68d6b21386f06842722
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753974"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Sauvegarder des bases de données SAP HANA dans des machines virtuelles Azure

Les bases de données SAP HANA sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. Vous pouvez sauvegarder les bases de données SAP HANA qui s’exécutent sur les machines virtuelles Azure en utilisant [Sauvegarde Azure](backup-overview.md).

Cet article explique comment sauvegarder des bases de données SAP HANA s’exécutant sur des machines virtuelles Azure pour un coffre Recovery Services de la Sauvegarde Azure.

Dans cet article, vous allez apprendre à :
> [!div class="checklist"]
>
> * Créer et configurer un coffre
> * Découvrir des bases de données
> * Configurer des sauvegardes
> * Exécuter un travail de sauvegarde à la demande

## <a name="prerequisites"></a>Conditions préalables requises

Reportez-vous aux sections [conditions préalables](tutorial-backup-sap-hana-db.md#prerequisites) et [configuration des autorisations](tutorial-backup-sap-hana-db.md#setting-up-permissions) pour configurer la base de données pour la sauvegarde.

### <a name="set-up-network-connectivity"></a>Configurer la connectivité réseau

Pour toutes les opérations, la machine virtuelle SAP HANA nécessite une connectivité aux adresses IP publiques Azure. Les opérations de machine virtuelle (détection de bases de données, configuration de sauvegardes, sauvegardes planifiées, restauration des points de récupération, etc.) échouent en cas d’absence de connexion aux adresses IP publiques Azure.

Établissez la connectivité en utilisant l’une des options suivantes :

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Autoriser les plages d’adresses IP du centre de données Azure

Cette option autorise les [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) dans le fichier téléchargé. Pour accéder à un groupe de sécurité réseau, utilisez l’applet de commande Set-AzureNetworkSecurityRule. Si votre liste de destinataires sûrs ne comporte que des adresses IP spécifiques à une région, vous devez également mettre à jour la liste des destinataires sûrs avec la balise du service Azure Active Directory (Azure AD) pour activer l’authentification.

#### <a name="allow-access-using-nsg-tags"></a>Autoriser l’accès à l’aide de balises de groupe de sécurité réseau

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

## <a name="onboard-to-the-public-preview"></a>Intégration à la préversion publique

Exécutez la procédure d’intégration à la préversion publique ci-après :

* Dans le portail, inscrivez votre ID d’abonnement auprès du fournisseur de services Recovery Services en [suivant les instructions de cet article](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Pour le module « Az » dans PowerShell, exécutez cette cmdlet. Ce processus doit s’achever en présentant la mention « Inscrit ».

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* Si vous utilisez le module « AzureRM » dans PowerShell, exécutez cette cmdlet. Ce processus doit s’achever en présentant la mention « Inscrit ».

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Détecter les bases de données

1. Dans le coffre, à la section **Prise en main**, cliquez sur **Sauvegarde**. Dans la zone **Où s’exécute votre charge de travail ?** , sélectionnez **SAP HANA dans les machines virtuelles Azure**.
2. Cliquez sur **Démarrer la découverte**. Cette opération lance la détection des machines virtuelles Linux non protégées dans la région du coffre.

   * Après la détection, les machines virtuelles non protégées apparaissent dans le portail en étant répertoriées par nom et groupe de ressources.
   * Si une machine virtuelle n’est pas répertoriée alors qu’elle devrait l’être, vérifiez si elle a déjà été sauvegardée dans un coffre.
   * Plusieurs machines virtuelles peuvent avoir le même nom, mais appartenir à différents groupes de ressources.

3. Dans **Sélectionner les machines virtuelles**, cliquez sur le lien pour télécharger le script qui autorise le service Sauvegarde Azure à accéder aux machines virtuelles SAP HANA pour la découverte des bases de données.
4. Exécutez le script sur chacune des machines virtuelles hébergeant les bases de données SAP HANA que vous souhaitez sauvegarder.
5. Après avoir exécuté le script sur les machines virtuelles, dans **Sélectionner les machines virtuelles**, sélectionnez les machines souhaitées. Ensuite, cliquez sur **Découvrir les bases de données**.
6. Le service Sauvegarde Azure détecte toutes les bases de données SAP HANA résidant sur la machine virtuelle. Lors de la détection, le service Sauvegarde Azure inscrit la machine virtuelle auprès du coffre et y installe une extension. Aucun agent n’est installé sur la base de données.

    ![Détection des bases de données SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurer une sauvegarde  

Maintenant, activez la sauvegarde.

1. À l’étape 2, cliquez sur **Configurer la sauvegarde**.

    ![Configurez une sauvegarde](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Dans **Sélectionner les éléments à sauvegarder**, sélectionnez toutes les bases de données que vous souhaitez protéger, puis choisissez **OK**.

    ![Sélectionner les éléments à sauvegarder](./media/backup-azure-sap-hana-database/select-items.png)
3. Dans **Stratégie de sauvegarde** > **Choisir une stratégie de sauvegarde**, créez une stratégie de sauvegarde pour les bases de données en suivant les instructions ci-dessous.

    ![Choisir une stratégie de sauvegarde](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Après avoir créé la stratégie, dans le menu **Sauvegarde**, cliquez sur **Activer la sauvegarde**.

    ![Activer la sauvegarde](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Vous pouvez suivre la progression de la configuration de la sauvegarde dans la zone **Notifications** du portail.

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées, ainsi que leur durée de rétention.

* Une stratégie est créée au niveau du coffre.
* Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.

Spécifiez les paramètres de stratégie comme suit :

1. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.

   ![Saisir un nom de stratégie](./media/backup-azure-sap-hana-database/policy-name.png)
2. Dans **Stratégie de sauvegarde complète**, comme **Fréquence de sauvegarde**, sélectionnez **Tous les jours** ou **Toutes les semaines**.
   * **Daily (Quotidienne)** : sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde.
       * Vous devez exécuter une sauvegarde complète. Vous ne pouvez pas désactiver cette option.
       * Cliquez sur **Sauvegarde complète** pour afficher la stratégie.
       * Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.
   * **Hebdomadaire** : sélectionnez le jour de la semaine, l’heure et le fuseau horaire de début du travail de sauvegarde.

   ![Sélectionner la fréquence de sauvegarde](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Dans **Durée de rétention**, configurez les paramètres de rétention pour la sauvegarde complète.
    * Par défaut, toutes les options sont sélectionnées. Désactivez les limites de durée de rétention que vous ne souhaitez pas utiliser et définissez celles qui vous intéressent.
    * La période de rétention minimale est de sept jours pour tous les types de sauvegardes (complète/différentielle/fichier journal).
    * Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    * La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée de rétention hebdomadaire et aux paramètres.
    * Les durées de rétention mensuelle et annuelle ont le même comportement.

4. Dans le menu de stratégie **Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
5. Sélectionnez **Sauvegarde différentielle** pour ajouter une stratégie différentielle.
6. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.
    * Vous pouvez déclencher au plus une sauvegarde différentielle par jour.
    * Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Si vous avez besoin d’une durée de rétention supérieure, vous devez utiliser des sauvegardes complètes.

    ![Stratégie de sauvegarde différentielle](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Pour l’instant, les sauvegardes incrémentielles ne sont pas prises en charge.

7. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu **Stratégie de sauvegarde** principal.
8. Sélectionnez **Sauvegarde de fichier journal** pour ajouter une stratégie de sauvegarde de fichier journal.
    * Dans **Sauvegarde de fichier journal**, sélectionnez **Activer**.  Cette option ne peut pas être désactivée, car SAP HANA gère toutes les sauvegardes de fichiers journaux.
    * Définissez les contrôles de fréquence et de rétention.

    > [!NOTE]
    > Les sauvegardes de fichiers journaux ne commencent à s’effectuer qu’en cas de réussite d’une sauvegarde complète.

9. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu **Stratégie de sauvegarde** principal.
10. Après avoir défini la stratégie de sauvegarde, cliquez sur **OK**.

> [!NOTE]
> Chaque sauvegarde de fichier journal est chaînée à la sauvegarde complète précédente pour former une chaîne de récupération. Cette sauvegarde complète est conservée jusqu’à la fin de la durée de conservation de la dernière sauvegarde de fichier journal. Il est donc possible que la sauvegarde complète soit conservée pour une durée supplémentaire afin que tous les journaux puissent être récupérés. Supposons que l’utilisateur effectue une sauvegarde complète hebdomadaire, une sauvegarde différentielle par jour et des journaux d’activité toutes les 2 heures. Tous sont conservés 30 jours. Cependant, la sauvegarde complète hebdomadaire ne peut être réellement nettoyée/supprimée que lorsque la sauvegarde complète suivante est disponible, à savoir après 30 + 7 jours. Par exemple, la sauvegarde complète hebdomadaire a lieu le 16 novembre. Conformément à la stratégie de rétention, elle doit être conservée jusqu’au 16 décembre. La dernière sauvegarde de fichier journal de cette sauvegarde complète a lieu avant la prochaine sauvegarde complète planifiée, le 22 novembre. Tant que ce journal n’est pas disponible, jusqu’au 22 décembre, la sauvegarde complète du 16 novembre ne peut pas être supprimée. La sauvegarde complète du 16 novembre est donc conservée jusqu’au 22 décembre.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Les sauvegardes s’exécutent conformément à la planification de la stratégie. Vous pouvez exécuter une sauvegarde à la demande en procédant comme suit :

1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Dans **Éléments de sauvegarde**, sélectionnez la machine virtuelle exécutant la base de données SAP HANA, puis cliquez sur **Sauvegarder maintenant**.
3. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner le dernier jour de rétention du point de récupération. Cliquez ensuite sur **OK**.
4. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre base de données, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Exécuter une sauvegarde SAP HANA Studio d’une base de données pour laquelle le service Sauvegarde Azure est activé

Si vous souhaitez effectuer une sauvegarde locale (à l’aide de HANA Studio) d’une base de données en cours de sauvegarde par le service Sauvegarde Azure, procédez comme suit :

1. Attendez la fin de toute sauvegarde complète ou de fichier journal de la base de données. Vérifiez l’état dans SAP HANA Studio/Cockpit.
2. Désactivez les sauvegardes de fichiers journaux, puis définissez le catalogue de sauvegarde sur le système de fichiers pour la base de données appropriée.
3. Pour ce faire, double-cliquez sur **systemdb** > **Configuration (Configuration)**  > **Select Database (Sélectionner la base de données)**  > **Filter (Log) (Filtrer [journal])** .
4. Définissez **enable_auto_log_backup** sur **No (Non)** .
5. Définissez **log_backup_using_backint** sur **False (Faux)** .
6. Effectuez une sauvegarde complète à la demande de la base de données.
7. Attendez la fin de la sauvegarde complète et de la sauvegarde du catalogue.
8. Rétablissez les paramètres précédents sur les valeurs pour Azure :
    * Définissez **enable_auto_log_backup** sur **Yes (Oui)** .
    * Définissez **log_backup_using_backint** sur **True (Vrai)** .

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [restaurer des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Découvrez comment [gérer les bases de données SAP HANA sauvegardées à l’aide de la Sauvegarde Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
