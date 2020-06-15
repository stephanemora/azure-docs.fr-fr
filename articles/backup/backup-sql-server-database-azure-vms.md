---
title: Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure
description: Dans cet article, découvrez comment sauvegarder des bases de données SQL Server sur des machines virtuelles Azure avec la Sauvegarde Azure.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: faf5ffd65f9b3133c504413201d58aee988af71a
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248105"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. Vous pouvez sauvegarder les bases de données SQL Server qui s’exécutent sur les machines virtuelles Azure en utilisant [Sauvegarde Azure](backup-overview.md).

Cet article explique comment sauvegarder dans un coffre Recovery Services de Sauvegarde Azure une base de données SQL Server s’exécutant sur une machine virtuelle Azure.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créer et configurer un coffre
> * Détecter des bases de données et configurer des sauvegardes
> * Configurer la protection automatique de bases de données

>[!NOTE]
>**La suppression réversible pour SQL Server dans une machine virtuelle Azure et la suppression réversible pour SAP HANA dans les charges de travail de machine virtuelle Azure** sont maintenant disponibles en préversion.<br>
>Pour vous inscrire à la version préliminaire, écrivez-nous à l’adresse suivante : AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prérequis

Pour pouvoir sauvegarder une base de données SQL Server, vérifiez les critères suivants :

1. Identifiez ou créez un [coffre Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) dans la même région et avec le même abonnement que la machine virtuelle qui héberge l’instance SQL Server.
2. Vérifiez que la machine virtuelle dispose d’une [connectivité réseau](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Assurez-vous que les bases de données SQL Server respectent les [instructions de nommage pour Sauvegarde Azure](#database-naming-guidelines-for-azure-backup).
4. Vérifiez que vous n’avez aucune autre solution de sauvegarde activée pour la base de données. Désactivez tous les autres sauvegardes SQL Server avant de sauvegarder la base de données.

> [!NOTE]
> Vous pouvez activer Sauvegarde Azure pour une machine virtuelle Azure et une base de données SQL Server s’exécutant sur la machine virtuelle sans conflit.

### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toutes les opérations, une machine virtuelle SQL Server nécessite une connectivité avec le service Sauvegarde Azure, Stockage Azure et Azure Active Directory. Pour ce faire, vous pouvez utiliser des points de terminaison privés ou autoriser l’accès aux IP publiques ou aux noms de domaine complets (FQDN) requis. Le fait de ne pas permettre une connectivité appropriée aux services Azure requis peut entraîner l’échec d’opérations telles que la détection de base de données, la configuration de la sauvegarde, l’exécution de sauvegardes et la restauration de données.

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

Lorsque vous sauvegardez une base de données SQL Server sur une machine virtuelle Azure, l’extension de sauvegarde sur la machine virtuelle utilise les API HTTPS pour envoyer des commandes de gestion à Sauvegarde Azure, et des données à Stockage Azure. L’extension de sauvegarde utilise également Azure AD pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. Utilisez la liste des adresses IP et des FQDN ci-dessus pour autoriser l’accès aux services requis. Les serveurs proxy authentifiés ne sont pas pris en charge.

### <a name="database-naming-guidelines-for-azure-backup"></a>Instructions de dénomination des bases de données pour Sauvegarde Azure

Évitez d’utiliser les éléments suivants dans les noms de base de données :

* Espaces au début et à la fin
* Points d’exclamation (!) à la fin
* Crochets de fermeture (])
* Point-virgule « ; »
* Barre oblique « / »

L’utilisation d’alias est possible, bien que déconseillée, pour les caractères non pris en charge. Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>L’opération **Configurer la protection** n’est pas prise en charge pour les bases de données dont le nom comporte des caractères spéciaux tels que « + » ou « & ». Vous pouvez modifier le nom de la base de données ou bien activer la **protection automatique**, ce qui permet de protéger correctement ces bases de données.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Détecter les bases de données SQL Server

Comment détecter les bases de données en cours d’exécution sur une machine virtuelle :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le coffre Recovery Services que vous utilisez pour sauvegarder la base de données.

2. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **Sauvegarde**.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur **Azure**.

4. Dans **Que souhaitez-vous sauvegarder ?** , sélectionnez **SQL Server dans une machine virtuelle Azure**.

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Dans **Objectif de sauvegarde** > **Découvrir les bases de données dans les machines virtuelles**, sélectionnez **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. La durée de cette recherche varie selon le nombre de machines virtuelles non protégées de l’abonnement.

   * Après la détection, les machines virtuelles non protégées doivent apparaître dans la liste, répertoriées par nom et groupe de ressources.
   * Si une machine virtuelle n’est pas répertoriée contrairement à ce que vous attendez, vérifiez si elle n’est pas déjà sauvegardée dans un coffre.
   * Plusieurs machines virtuelles peuvent avoir le même nom, mais appartenir à différents groupes de ressources.

     ![La sauvegarde est en attente au cours de la recherche pour des bases de données dans des machines virtuelles](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle exécutant la base de données SQL Server > **Découvrir les bases de données**.

7. Suivez la découverte des bases de données dans **Notifications**. La durée nécessaire à cette action dépend du nombre de bases de données sur la machine virtuelle. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

    ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. La sauvegarde Azure détecte toutes les bases de données SQL Server résidant sur la machine virtuelle. Lors de la découverte, les éléments suivants se produisent en arrière-plan :

    * Sauvegarde Azure inscrit la machine virtuelle auprès du coffre pour la sauvegarde de la charge de travail. Les bases de données présentes sur la machine virtuelle inscrite ne peuvent être sauvegardées que sur ce coffre.
    * Sauvegarde Azure installe l’extension AzureBackupWindowsWorkload sur la machine virtuelle. Aucun agent n’est installé sur une base de données SQL.
    * Sauvegarde Azure crée le compte de service NT Service\AzureWLBackupPluginSvc sur la machine virtuelle.
      * Toutes les opérations de sauvegarde et de restauration utilisent le compte de service.
      * NT Service\AzureWLBackupPluginSvc requiert des autorisations d’administrateur système SQL. SqlIaaSExtension est installé sur toutes les machines virtuelles SQL Server créées dans la Place de marché Azure. L’extension AzureBackupWindowsWorkload utilise l’extension SQLIaaSExtension pour obtenir automatiquement les autorisations requises.
    * Si vous n’avez pas créé la machine virtuelle à partir de la Place de marché ou si vous utilisez SQL 2008 et 2008 R2, SqlIaaSExtension n’est probablement pas installé sur la machine virtuelle, et l’opération de découverte échoue avec le message d’erreur UserErrorSQLNoSysAdminMembership. Pour résoudre ce problème, suivez les instructions de la section [Définir les autorisations de machine virtuelle](backup-azure-sql-database.md#set-vm-permissions).

        ![Sélectionner la machine virtuelle et la base de données](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurer une sauvegarde  

1. Dans **Objectif de sauvegarde** > **Étape 2 : Configurer la sauvegarde**, sélectionnez **Configurer la sauvegarde**.

   ![Sélectionner Configurer la sauvegarde](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **Sélectionner les éléments à sauvegarder** répertorie tous les groupes de disponibilité inscrits et les instances SQL Server autonomes. Sélectionnez la flèche vers la gauche pour développer la liste de toutes les bases de données non protégées dans cette instance ou ce groupe de disponibilité Always On.  

    ![Affichage de toutes les instances SQL Server avec des bases de données autonomes](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Choisissez toutes les bases de données que vous souhaitez protéger, puis sélectionnez **OK**.

   ![Protection de la base de données](./media/backup-azure-sql-database/select-database-to-protect.png)

   Pour optimiser les charges de sauvegarde, la sauvegarde Azure définit le nombre maximal de bases de données à 50 dans une tâche de sauvegarde.

     * Pour protéger plus de 50 bases de données, configurez plusieurs sauvegardes.
     * Pour [activer](#enable-auto-protection) l’ensemble de l’instance ou le groupe de disponibilité Always On dans la liste déroulante **PROTECTION AUTOMATIQUE**, sélectionnez **ACTIVER** puis **OK**.

    > [!NOTE]
    > La fonctionnalité de [protection automatique](#enable-auto-protection) permet non seulement de protéger toutes les bases de données existantes en une seule étape, mais aussi de protéger automatiquement toutes les nouvelles bases de données ajoutées à cette instance ou ce groupe de disponibilité.  

4. Cliquez sur **OK** pour ouvrir **Stratégie de sauvegarde**.

    ![Activer la protection automatique pour le groupe de disponibilité Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Dans **Stratégie de sauvegarde**, choisissez une stratégie, puis sélectionnez **OK**.

   * Sélectionner la stratégie par défaut HourlyLogBackup.
   * Choisir une stratégie de sauvegarde existante créée précédemment pour SQL.
   * Définir une nouvelle stratégie selon votre RPO et la durée de rétention.

     ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-sql-database/select-backup-policy.png)

6. Dans **Sauvegarde**, sélectionnez **Activer la sauvegarde**.

    ![Activer la stratégie de sauvegarde choisie](./media/backup-azure-sql-database/enable-backup-button.png)

7. Vous pouvez suivre la progression de la configuration dans la zone **Notifications** du portail.

    ![Zone Notifications](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées ainsi que leur durée de rétention.

* Une stratégie est créée au niveau du coffre.
* Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.
* Lorsque vous créez une stratégie de sauvegarde, une sauvegarde complète quotidienne est la valeur par défaut.
* Vous pouvez ajouter une sauvegarde différentielle, mais uniquement si vous configurez les sauvegardes complètes pour qu’elles aient lieu toutes les semaines.
* Apprenez-en davantage sur les [différents types de stratégies de sauvegarde](backup-architecture.md#sql-server-backup-types).

Pour créer une stratégie de sauvegarde :

1. Dans le coffre, sélectionnez **Stratégies de sauvegarde** > **Ajouter**.
2. Dans **Ajouter**, sélectionnez **SQL Server dans une machine virtuelle Azure** pour définir le type de stratégie.

   ![Choisissez un type de stratégie pour la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/policy-type-details.png)

3. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.
4. Dans **Stratégie de sauvegarde complète**, sélectionnez une **Fréquence de sauvegarde**. Choisissez **Quotidienne** ou **Hebdomadaire**.

   * Si vous sélectionnez **quotidienne**, sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde.
   * Si vous choisissez la fréquence **hebdomadaire**, sélectionnez le jour de la semaine, l’heure et le fuseau horaire indiquant le début du travail de sauvegarde.
   * Exécutez une sauvegarde complète, car vous ne pouvez pas désactiver l’option **Sauvegarde complète**.
   * Sélectionnez **Sauvegarde complète** pour afficher la stratégie.
   * Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.

     ![Champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Dans **DURÉE DE RÉTENTION**, toutes les options sont sélectionnées par défaut. Désactivez les limites des plages de rétention dont vous ne souhaitez pas, puis définissez les intervalles à utiliser.

    * La période de rétention minimale est de sept jours pour tous les types de sauvegardes (complète, différentielle et fichier journal).
    * Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    * La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée et au paramètre de rétention hebdomadaire.
    * Les durées de rétention mensuelle et annuelle ont le même comportement.

       ![Paramètres d’intervalle de la durée de rétention](./media/backup-azure-sql-database/retention-range-interval.png)

6. Dans le menu de **stratégie Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
7. Pour ajouter une stratégie de sauvegarde différentielle, sélectionnez **Sauvegarde différentielle**.

   ![Paramètres d’intervalle des durées de rétention](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Ouvrir le menu de la stratégie de sauvegarde différentielle](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.

    * Vous pouvez déclencher une sauvegarde différentielle une fois par jour uniquement.
    * Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Pour une durée de rétention supérieure, utilisez des sauvegardes complètes.

9. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

10. Pour ajouter une stratégie de sauvegarde de fichier journal, cliquez sur **Sauvegarde de fichier journal**.
11. Dans **Sauvegarde de fichier journal**, sélectionnez **Activer** et définissez les contrôles de fréquence et de rétention. Les sauvegardes de fichiers journaux peuvent se produire toutes les 15 minutes et être conservées jusqu’à 35 jours.
12. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

    ![Modifier la stratégie de sauvegarde de fichier journal](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Dans le menu **Stratégie de sauvegarde**, choisissez s’il convient d’activer l’option **Compression de la sauvegarde SQL**, qui est désactivée par défaut. Si elle est activée, SQL Server envoie un flux de sauvegarde compressé à VDI.  Notez que Sauvegarde Azure remplace les valeurs par défaut au niveau de l’instance par la clause COMPRESSION / NO_COMPRESSION en fonction de la valeur de ce contrôle.

14. Après avoir terminé les modifications apportées à la stratégie de sauvegarde, sélectionnez **OK**.

> [!NOTE]
> Chaque sauvegarde de fichier journal est chaînée à la sauvegarde complète précédente pour former une chaîne de récupération. Cette sauvegarde complète est conservée jusqu’à la fin de la durée de conservation de la dernière sauvegarde de fichier journal. Il est donc possible que la sauvegarde complète soit conservée pour une durée supplémentaire afin que tous les journaux puissent être récupérés. Supposons que l’utilisateur effectue une sauvegarde complète hebdomadaire, une sauvegarde différentielle par jour et des journaux d’activité toutes les 2 heures. Tous sont conservés 30 jours. Cependant, la sauvegarde complète hebdomadaire ne peut être réellement nettoyée/supprimée que lorsque la sauvegarde complète suivante est disponible, à savoir après 30 + 7 jours. Par exemple, la sauvegarde complète hebdomadaire a lieu le 16 novembre. Conformément à la stratégie de conservation, elle doit être conservée jusqu’au 16 décembre. La dernière sauvegarde de fichier journal de cette sauvegarde complète a lieu avant la prochaine sauvegarde complète planifiée, le 22 novembre. Tant que ce journal n’est pas disponible, jusqu’au 22 décembre, la sauvegarde complète du 16 novembre ne peut pas être supprimée. La sauvegarde complète du 16 novembre est donc conservée jusqu’au 22 décembre.

## <a name="enable-auto-protection"></a>Activer la protection automatique  

Vous pouvez activer la protection automatique pour sauvegarder automatiquement toutes les bases de données existantes et futures sur une instance SQL Server autonome ou à un groupe de disponibilité Always On.

* Il n’existe aucune limite sur le nombre de bases de données que vous pouvez sélectionner en une fois pour la protection automatique.
* Vous ne pouvez pas protéger ou exclure sélectivement des bases de données de la protection dans une instance lorsque vous activez la protection automatique.
* Si votre instance inclut déjà des bases de données protégées, elles restent protégées par leurs stratégies respectives même après que vous avez activé la protection automatique. Toutes les bases de données non protégées que vous ajoutez par la suite ont une seule stratégie que vous définissez au moment de l’activation de la protection automatique et qui est affichée sous **Configurer la sauvegarde**. Toutefois, vous pouvez modifier ultérieurement la stratégie associée à une base de données protégée automatiquement.  

Pour activer la protection automatique :

  1. Dans **Éléments à sauvegarder**, sélectionnez l’instance pour laquelle vous souhaitez activer la protection automatique.
  2. Sélectionnez la liste déroulante sous **AUTOPROTECT** (Protection automatique), choisissez **ON** (Activer), puis sélectionnez **OK**.

      ![Activer la protection automatique sur le groupe de disponibilité](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. La sauvegarde est configurée pour toutes les bases de données et peut être suivie dans **Travaux de sauvegarde**.

Si vous devez désactiver la protection automatique, sélectionnez le nom d’instance sous **Configurer la sauvegarde**, puis sélectionnez **Désactiver la protection automatique** pour cette instance. Toutes les bases de données continueront à être sauvegardées, mais mes futures bases de données, en revanche, ne seront pas automatiquement protégées.

![Désactiver la protection automatique sur cette instance](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

* [Restaurer des bases de données SQL Server sauvegardées](restore-sql-database-azure-vm.md)
* [Gérer les bases de données SQL Server sauvegardées](manage-monitor-sql-database-backup.md)
