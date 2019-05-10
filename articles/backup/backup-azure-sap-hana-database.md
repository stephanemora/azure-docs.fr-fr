---
title: Sauvegarder une base de données SAP HANA sur Azure avec sauvegarde Azure | Microsoft Docs
description: Ce didacticiel explique comment sauvegarder une base de données SAP HANA sur Azure avec le service de sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 5ed41013535e4591d88bff5c017c1fcf4c4053cc
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237806"
---
# <a name="back-up-an-sap-hana-database"></a>Sauvegarder une base de données SAP HANA

[Sauvegarde Azure](backup-overview.md) prend en charge la sauvegarde des bases de données SAP HANA sur Azure.

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique. Il n’est pas prêt pour la production et n’a pas un contrat SLA garanti. 


## <a name="scenario-support"></a>Prise en charge du scénario

**Support** | **Détails**
--- | ---
**Zones géographiques prises en charge** | Australie du Sud, Australie <br> Brésil Sud <br> Canada Central, est du Canada <br> Asie du Sud-est, Asie de l’est <br> Est des États-Unis, est des États-Unis 2, centre ouest des États-Unis, ouest des États-Unis, ouest des États-Unis 2, États-Unis centre nord, centre des États-Unis, Sud-Centre des États-Unis<br> Inde centre, Inde-sud <br> Japon Est, Japon Ouest<br> Centre de la Corée, Corée du Sud <br> Europe Nord, Europe Ouest <br> Royaume-Uni Sud, Royaume-Uni ouest
**Systèmes d’exploitation de machine virtuelle** | SLES 12 avec SP2 ou SP3.
**Versions prises en charge de HANA** | SSDC sur HANA 1.x, MDC sur HANA 2.x < = SPS03


### <a name="current-limitations"></a>Limitations actuelles

- Vous pouvez uniquement sauvegarder les bases de données SAP HANA en cours d’exécution sur des machines virtuelles Azure.
- Vous pouvez uniquement configurer la sauvegarde SAP HANA dans le portail Azure. La fonctionnalité ne peut pas être configurée avec PowerShell, CLI ou l’API REST.
- Vous pouvez uniquement sauvegarder les bases de données en mode de monter en puissance.
- Vous pouvez sauvegarder les journaux de base de données toutes les 15 minutes. Sauvegardes de journaux à peine flux issue d’une sauvegarde complète réussie pour la base de données.
- Vous pouvez effectuer des sauvegardes complètes et différentielles. Sauvegarde incrémentielle n’est pas actuellement pris en charge.
- Vous ne pouvez pas modifier la stratégie de sauvegarde une fois que vous l’appliquez aux sauvegardes SAP HANA. Si vous souhaitez sauvegarder avec des paramètres différents, créez une stratégie ou affecter une autre stratégie. 
    - Pour créer une nouvelle stratégie, dans le coffre, cliquez sur **stratégies** > **stratégies de sauvegarde** > **+ ajouter** > **SAP HANA dans Machine virtuelle Azure**et spécifiez les paramètres de stratégie.
    - Pour affecter une stratégie différente, dans les propriétés de la machine virtuelle en cours d’exécution la base de données, cliquez sur le nom de la stratégie actuelle. Ensuite, sous la **stratégie de sauvegarde** page, vous pouvez sélectionner une autre stratégie à utiliser pour la sauvegarde.




## <a name="prerequisites"></a>Conditions préalables

Assurez-vous que vous effectuez ce qui suit avant de configurer des sauvegardes :

1. Sur la machine virtuelle exécutant la base de données SAP HANA, installez Microsoft officielle [le Runtime .NET Core 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) package. Notez les points suivants :
    - Vous devez uniquement le **dotnet-runtime-2.1** package. Vous n’avez pas besoin **aspnetcore-runtime-2.1**.
    - Si la machine virtuelle n’a pas internet accès, de mettre en miroir ou de fournir un cache hors connexion pour dotnet-runtime-2.1 (et RPM dépendants toutes les) à partir du package Microsoft flux spécifié dans la page.
    - Pendant l’installation de package, vous devrez peut-être spécifier une option. Dans ce cas, spécifiez **Solution 2**.

        ![Option d’installation de package](./media/backup-azure-sap-hana-database/hana-package.png)

2.  Sur la machine virtuelle, installer et activer les packages de pilotes ODBC à partir des officiel SLES package ou des éléments multimédias à l’aide de zypper, comme suit :

    ``` 
    sudo zypper update
    sudo zypper install unixODBC
    ```
4.  Autoriser la connectivité à partir de la machine virtuelle à internet, afin qu’il peut atteindre Azure, comme décrit dans la procédure ci-dessous.


### <a name="set-up-network-connectivity"></a>Configurer le connectivité réseau

Pour toutes les opérations, la machine virtuelle SAP HANA nécessite une connectivité vers les adresses IP publiques Azure. Opérations de machine virtuelle (détection de la base de données, configurer des sauvegardes, planifier des sauvegardes, restaurer des points de récupération et ainsi de suite) ne peut pas fonctionner sans connectivité. Établir la connectivité en autorisant l’accès aux plages d’IP de centre de données Azure : 

- Vous pouvez télécharger le [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) pour les centres de données Azure, puis autoriser l’accès à ces adresses IP.
- Si vous utilisez des groupes de sécurité réseau (NSG), vous pouvez utiliser la AzureCloud [balise de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pour autoriser l’adresse IP publique Azure toutes les adresses. Vous pouvez utiliser la [applet de commande Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) pour modifier des règles de groupe de sécurité réseau.

## <a name="onboard-to-the-public-preview"></a>Intégrer à la version préliminaire publique

Intégrer à la version préliminaire publique comme suit :

- Dans le portail, inscrivez votre ID d’abonnement au fournisseur de services de Recovery Services par [suivant cet article](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Pour PowerShell, exécutez cette applet de commande. Il doit s’exécuter en tant que « Enregistré ».

    ```
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Découvrir les bases de données


1. Dans le coffre, dans **mise en route**, cliquez sur **sauvegarde**. Dans **où s’exécute votre charge de travail ?**, sélectionnez **SAP HANA dans Azure VM**.
2. Cliquez sur **démarrer la découverte**. Cela lance la découverte des machines virtuelles Linux non protégées dans la région du coffre.

   - Après la découverte, machines virtuelles non protégées s’affichent dans le portail, répertoriés par nom et groupe de ressources.
   - Si une machine virtuelle n’est pas répertoriée comme prévu, vérifiez si elle est déjà sauvegardé dans un coffre.
   - Plusieurs machines virtuelles peuvent avoir le même nom, mais ils appartiennent à différents groupes de ressources.

3. Dans **sélectionner les Machines virtuelles**, cliquez sur le lien pour télécharger le script qui accorde des autorisations pour le service de sauvegarde Azure accéder aux machines virtuelles SAP HANA pour la découverte de la base de données
4. Exécutez le script sur chaque machine virtuelle qui héberge les bases de données SAP HANA que vous souhaitez sauvegarder.
5. Après avoir exécuté le script sur les machines virtuelles, en **sélectionner les Machines virtuelles**, sélectionnez les machines virtuelles. Puis cliquez sur **découvrir des bases de données**.
6. Sauvegarde Azure permet de découvrir toutes les bases de données SAP HANA sur la machine virtuelle. Pendant la découverte, sauvegarde Azure inscrit la machine virtuelle auprès du coffre et installe une extension sur la machine virtuelle. Aucun agent n’est installé sur la base de données.

    ![Découvrir les bases de données SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)
    
## <a name="configure-backup"></a>Configurer une sauvegarde  

Activer la sauvegarde maintenant.

1. À l’étape 2, cliquez sur **configurer la sauvegarde**.
2. Dans **sélectionner les éléments à sauvegarder**, sélectionnez toutes les bases de données que vous souhaitez protéger > **OK**.
3. Dans **stratégie de sauvegarde** > **choisir une stratégie de sauvegarde**, créer une stratégie de sauvegarde pour les bases de données, conformément aux instructions ci-dessous.
4. Après avoir créé la stratégie, sur le **sauvegarde** menu, cliquez sur **activer la sauvegarde**.
5. Suivre la progression de la configuration de la sauvegarde dans le **Notifications** zone du portail.

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde
Une stratégie de sauvegarde définit quand les sauvegardes sont effectuées, et la durée pendant laquelle ils sont conservés.

- Une stratégie est créée au niveau du coffre.
- Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.

Spécifiez les paramètres de stratégie comme suit :

1. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.
2. Dans **Stratégie de sauvegarde complète**, comme **Fréquence de sauvegarde**, sélectionnez **Tous les jours** ou **Toutes les semaines**.
   - **Daily (Quotidienne)** : Sélectionnez l’heure et le fuseau horaire dans lequel la tâche de sauvegarde commence.
   
       - Vous devez exécuter une sauvegarde complète. Vous ne pouvez pas désactiver cette option.
       - Cliquez sur **Sauvegarde complète** pour afficher la stratégie.
       - Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.
       
   - **Hebdomadaire**: Sélectionnez le jour de la semaine, heure et fuseau horaire dans lequel s’exécute le travail de sauvegarde.
3. Dans **durée de rétention**, configurer les paramètres de rétention pour la sauvegarde complète.
    - Par défaut, toutes les options sont sélectionnées. Désactivez les limites de plage de rétention que vous ne souhaitez pas utiliser, puis définissez celles que vous effectuez.
    - La période de rétention minimale pour n’importe quel type de sauvegarde (complète/différentielle/de journal) est de sept jours.
    - Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    - La sauvegarde pour un jour spécifique est marqué et conservé selon le paramètre et la durée de rétention hebdomadaire.
    - Les durées de rétention mensuelle et annuelle ont le même comportement.

4. Dans le **stratégie de sauvegarde complète** menu, cliquez sur **OK** pour accepter les paramètres.
5. Sélectionnez **sauvegarde différentielle** pour ajouter une stratégie différentielle.
6. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.
    - Vous pouvez déclencher au plus une sauvegarde différentielle par jour.
    - Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Si vous avez besoin d’une durée de rétention supérieure, vous devez utiliser des sauvegardes complètes.

    > [!NOTE]
    > Les sauvegardes incrémentielles ne sont pas actuellement pris en charge. 

7. Cliquez sur **OK** pour enregistrer la stratégie et revenir à la main **stratégie de sauvegarde** menu.
8. Sélectionnez **sauvegarde de journal** pour ajouter une stratégie de sauvegarde de journal des transactions
    - Dans **sauvegarde du journal**, sélectionnez **activer**.
    - Définir les contrôles de fréquence et de rétention.

    > [!NOTE]
    > Sauvegardes de journaux à peine flux après qu’une sauvegarde complète est terminée.

9. Cliquez sur **OK** pour enregistrer la stratégie et revenir à la main **stratégie de sauvegarde** menu.
10. Une fois que vous avez terminé la définition de la stratégie de sauvegarde, cliquez sur **OK**.


## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Les sauvegardes exécutées conformément à la planification de la stratégie. Vous pouvez exécuter une sauvegarde à la demande comme suit :


1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Dans **éléments de sauvegarde**, sélectionnez la machine virtuelle en cours d’exécution la base de données SAP HANA, puis cliquez sur **sauvegarder maintenant**.
3. Dans **sauvegarder maintenant**, utilisez le contrôle calendrier pour sélectionner le dernier jour où le point de récupération doit être conservé. Cliquez ensuite sur **OK**.
4. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **travaux de sauvegarde** > en cours d’exécution. Selon la taille de votre base de données, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Exécuter la sauvegarde de SAP HANA Studio sur une base de données avec la sauvegarde Azure activée

Si vous souhaitez effectuer une sauvegarde locale (à l’aide de HANA Studio) d’une base de données est en cours de sauvegarde avec sauvegarde Azure, procédez comme suit :

1. Attendre pour n’importe quel intégral ou les sauvegardes de journal pour la base de données à la fin. Vérifiez l’état dans SAP HANA Studio.
2. Désactiver les sauvegardes de journal et définir le catalogue de sauvegarde pour le système de fichiers pour la base de données appropriée.
3. Pour ce faire, double-cliquez sur **systemdb** > **Configuration** > **sélectionner la base de données** > **filtre (journal)** .
4. Définissez **enable_auto_log_backup** à **non**.
5. Définissez **log_backup_using_backint** à **False**.
6. Effectuez un ad hoc sauvegarde complète de la base de données.
7. Attendez que la sauvegarde complète et de la sauvegarde de catalogue se termine.
8. Rétablir les paramètres précédents à celles pour Azure :
    - Définissez **enable_auto_log_backup** à **Oui**.
    - Définissez **log_backup_using_backint** à **True**.



## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur](backup-azure-arm-vms-prepare.md) sauvegarde des machines virtuelles Azure.


