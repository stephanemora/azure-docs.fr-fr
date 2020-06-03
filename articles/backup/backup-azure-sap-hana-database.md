---
title: Sauvegarder une base de données SAP HANA sur Azure avec Sauvegarde Azure
description: Dans cet article, découvrez comment sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 4183c1eca6b1149c5c61ed77c0ca1101c86f8f4f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745426"
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

>[!NOTE]
>**La suppression réversible pour SQL Server dans la machine virtuelle Azure et la suppression réversible pour SAP HANA dans les charges de travail de machine virtuelle Azure** est maintenant disponible en préversion.<br>
>Pour vous inscrire à la version préliminaire, écrivez-nous à l’adresse suivante : AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prérequis

Reportez-vous aux sections [Conditions préalables](tutorial-backup-sap-hana-db.md#prerequisites) et [Ce que fait le script de préinscription](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) pour configurer la base de données pour la sauvegarde.

### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toutes les opérations, une base de données SAP HANA s’exécutant sur une machine virtuelle Azure nécessite une connectivité avec le service Sauvegarde Azure, Stockage Azure et Azure Active Directory. Pour ce faire, vous pouvez utiliser des points de terminaison privés ou autoriser l’accès aux IP publiques ou aux noms de domaine complets (FQDN) requis. Le fait de ne pas permettre une connectivité appropriée aux services Azure requis peut entraîner l’échec d’opérations telles que la détection de base de données, la configuration de la sauvegarde, l’exécution de sauvegardes et la restauration de données.

Le tableau suivant répertorie les différentes alternatives que vous pouvez utiliser pour établir la connectivité :

| **Option**                        | **Avantages**                                               | **Inconvénients**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Instances Private Endpoint                 | Autorisent les sauvegardes sur des adresses IP privées au sein du réseau virtuel  <br><br>   Fournissent un contrôle approfondi du côté du réseau et du coffre | Engendre des [coûts](https://azure.microsoft.com/pricing/details/private-link/) de point de terminaison privé standard |
| Balises de service NSG                  | Plus faciles à gérer car les modifications apportées à la plage sont fusionnées automatiquement   <br><br>   Aucun coût supplémentaire | Peut être utilisé uniquement avec les groupes de sécurité réseau  <br><br>    Fournit l’accès à l’ensemble du service |
| Balises FQDN de Pare-feu Azure          | Plus faciles à gérer, car les FQDN requis sont gérés automatiquement | Utilisabes avec Pare-feu Azure uniquement                         |
| Autoriser l’accès aux FQDN/adresses IP du service | Aucun coût supplémentaire   <br><br>  Fonctionne avec toutes les appliances de sécurité réseau et tous les pare-feu | Il peut être nécessaire d’accéder à un large éventail d’adresses IP ou de FQDN   |
| Utiliser un proxy HTTP                 | Un seul point d’accès Internet aux machines virtuelles                       | Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy         |

De plus amples informations sur l’utilisation de ces options sont disponibles ci-dessous :

#### <a name="private-endpoints"></a>Instances Private Endpoint

Les points de terminaison privés vous permettent de vous connecter en toute sécurité à votre coffre Recovery Services à partir de serveurs situés dans un réseau virtuel. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre coffre. Le trafic réseau entre vos ressources dans le réseau virtuel et le coffre transite via votre réseau virtuel et une liaison privée sur le réseau principal de Microsoft. Cela élimine l’exposition de l’Internet public. Pour en savoir plus sur les points de terminaison privés pour Sauvegarde Azure, cliquez [ici](https://docs.microsoft.com/azure/backup/private-endpoints).

#### <a name="nsg-tags"></a>Balises NSG

Si vous utilisez des groupes de sécurité réseau (NSG), utilisez la balise de service *AzureBackup* pour autoriser l’accès sortant vers Sauvegarde Azure. En plus de la balise pour Sauvegarde Azure, vous devez également autoriser la connectivité pour l’authentification et le transfert de données en créant des [règles NSG](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) similaires pour *Azure AD* et *Stockage Azure*.  Les étapes suivantes décrivent le processus de création d’une règle pour la balise de Sauvegarde Azure :

1. Dans **Tous les services**, accédez à**Groupes de sécurité réseau** et sélectionnez le groupe de sécurité réseau.

1. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic sortant**.

1. Sélectionnez **Ajouter**. Entrez toutes les informations nécessaires à la création d’une nouvelle règle, comme décrit dans [paramètres de règle de sécurité](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Vérifiez que l’option **Destination** est définie sur *Balise de service* et l’option **Balise de service de destination** sur *AzureBackup*.

1. Cliquez sur **Ajouter**  pour enregistrer la règle de sécurité de trafic sortant que vous venez de créer.

De même, vous pouvez créer des règles de sécurité de trafic sortant NSG pour Stockage Azure et Azure AD.

#### <a name="azure-firewall-tags"></a>Balises Pare-feu Azure

Si vous utilisez Pare-feu Azure, créez une règle d’application en utilisant la [balise FQDN de Pare-feu Azure](https://docs.microsoft.com/azure/firewall/fqdn-tags) *AzureBackup*. Cela autorise tout accès sortant vers Sauvegarde Azure.

#### <a name="allow-access-to-service-ip-ranges"></a>Autoriser l’accès aux plages d’adresses IP du service

Si vous choisissez d’autoriser l’accès aux adresses IP du service, reportez-vous aux plages d’adresses IP répertoriées dans le fichier JSON accessible [ici](https://www.microsoft.com/download/confirmation.aspx?id=56519). Vous devez autoriser l’accès aux adresses IP correspondant à Sauvegarde Azure, Stockage Azure et Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Autoriser l’accès aux FQDN du service

Vous pouvez également utiliser les FQDN suivants pour autoriser l’accès aux services requis à partir de vos serveurs :

| Service    | Noms de domaine auxquels accéder                             |
| -------------- | ------------------------------------------------------------ |
| Sauvegarde Azure  | `*.backup.windowsazure.com`                             |
| Stockage Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Autoriser l’accès aux FQDN en vertu des sections 56 et 59 conformément à [cet article](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Utiliser un serveur proxy HTTP pour acheminer le trafic

Lorsque vous sauvegardez une base de données SAP HANA qui s’exécute sur une machine virtuelle Azure, l’extension de sauvegarde sur la machine virtuelle utilise les API HTTPS pour envoyer des commandes de gestion à Sauvegarde Azure et des données à Stockage Azure. L’extension de sauvegarde utilise également Azure AD pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. Utilisez la liste des adresses IP et des FQDN ci-dessus pour autoriser l’accès aux services requis. Les serveurs proxy authentifiés ne sont pas pris en charge.

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

>[!NOTE]
>Sauvegarde Azure ne s’ajuste pas automatiquement au changement d’heure lorsque vous sauvegardez une base de données SAP HANA s’exécutant sur une machine virtuelle Azure.
>
>Modifiez la stratégie manuellement en fonction des besoins.

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
> Chaque sauvegarde de fichier journal est chaînée à la sauvegarde complète précédente pour former une chaîne de récupération. Cette sauvegarde complète est conservée jusqu’à la fin de la durée de conservation de la dernière sauvegarde de fichier journal. Il est donc possible que la sauvegarde complète soit conservée pour une durée supplémentaire afin que tous les journaux puissent être récupérés. Supposons que l’utilisateur effectue une sauvegarde complète hebdomadaire, une sauvegarde différentielle par jour et des journaux d’activité toutes les 2 heures. Tous sont conservés 30 jours. Cependant, la sauvegarde complète hebdomadaire ne peut être réellement nettoyée/supprimée que lorsque la sauvegarde complète suivante est disponible, à savoir après 30 + 7 jours. Par exemple, la sauvegarde complète hebdomadaire a lieu le 16 novembre. Conformément à la stratégie de rétention, elle doit être conservée jusqu’au 16 décembre. La dernière sauvegarde de fichier journal de cette sauvegarde complète a lieu avant la prochaine sauvegarde complète planifiée, le 22 novembre. Tant que ce journal n’est pas disponible, jusqu’au 22 décembre, la sauvegarde complète du 16 novembre ne peut pas être supprimée. La sauvegarde complète du 16 novembre est donc conservée jusqu’au 22 décembre.

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
