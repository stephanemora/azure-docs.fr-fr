---
title: Sauvegarder une base de données SAP HANA sur Azure avec Sauvegarde Azure | Microsoft Docs
description: Ce tutoriel explique comment sauvegarder une base de données SAP HANA sur Azure à l’aide du service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 5ed41013535e4591d88bff5c017c1fcf4c4053cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65237806"
---
# <a name="back-up-an-sap-hana-database"></a>Sauvegarder une base de données SAP HANA

Le service [Sauvegarde Azure](backup-overview.md) prend en charge la sauvegarde des bases de données SAP HANA sur Azure.

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique. Pour l’instant, elle n’est pas prête pour la production et elle n’offre pas de Contrat de niveau de service (SLA) garanti. 


## <a name="scenario-support"></a>Prise en charge du scénario

**Support** | **Détails**
--- | ---
**Zones géographiques prises en charge** | Australie Sud-Est, Australie Est <br> Brésil Sud <br> Canada Centre, Canada Est <br> Asie Sud-Est, Asie Est <br> USA Est, USA Est 2, USA Centre-Ouest, USA Ouest, USA Ouest 2, USA Centre Nord, USA Centre, USA Centre Sud<br> Inde Centre, Inde Sud <br> Japon Est, Japon Ouest<br> Centre de la Corée, Corée du Sud <br> Europe Nord, Europe Ouest <br> Royaume-Uni Sud, Royaume-Uni Ouest
**Systèmes d’exploitation de machine virtuelle pris en charge** | SLES 12 avec SP2 ou SP3
**Versions HANA prises en charge** | SSDC sur HANA 1.x, MDC sur HANA 2.x <= SPS03


### <a name="current-limitations"></a>Limitations actuelles

- Vous ne pouvez sauvegarder que les bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure.
- Vous pouvez uniquement configurer la sauvegarde SAP HANA dans le Portail Azure. Cette fonctionnalité n’est pas configurable avec PowerShell, CLI ou l’API REST.
- Vous ne pouvez sauvegarder les bases de données qu’en mode Montée en puissance.
- Vous pouvez sauvegarder les journaux de base de données toutes les 15 minutes. Les sauvegardes de fichiers journaux ne commencent à s’effectuer qu’en cas de réussite d’une sauvegarde complète de la base de données.
- Vous pouvez exécuter des sauvegardes complètes ou différentielles. Pour l’instant, les sauvegardes incrémentielles ne sont pas prises en charge.
- Après avoir appliqué une stratégie de sauvegarde aux sauvegardes SAP HANA, vous ne pouvez plus la modifier. Si vous souhaitez modifier les paramètres de sauvegarde, créez ou attribuez une autre stratégie. 
    - Pour créer une autre stratégie, dans le coffre, cliquez sur **Stratégies** > **Stratégies de sauvegarde** >  **+Ajouter** > **SAP HANA dans les machines virtuelles Azure**, puis spécifiez les paramètres de stratégie.
    - Pour attribuer une autre stratégie, dans les propriétés de la machine virtuelle qui exécute la base de données, cliquez sur le nom de la stratégie actuelle. Ensuite, dans la page **Stratégie de sauvegarde**, sélectionnez une autre stratégie à utiliser pour la sauvegarde.




## <a name="prerequisites"></a>Prérequis

Avant de configurer les sauvegardes, prenez soin d’effectuer les opérations suivantes :

1. Sur la machine virtuelle exécutant la base de données SAP HANA, installez le package Microsoft [.NET Core Runtime 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) officiel. Notez les points suivants :
    - Seul le package **dotnet-runtime-2.1** vous est nécessaire. Vous n’avez pas besoin du package **aspnetcore-runtime-2.1**.
    - Si la machine virtuelle ne dispose pas d’un accès Internet, procédez à une mise en miroir ou fournissez un cache hors connexion pour dotnet-runtime-2.1 (et tous les RPM dépendants) à partir du flux de package Microsoft spécifié sur la page.
    - Lors de l’installation du package, vous pouvez être invité à spécifier une option. Si tel est le cas, spécifiez **Solution 2**.

        ![Options d’installation du package](./media/backup-azure-sap-hana-database/hana-package.png)

2.  Sur la machine virtuelle, installez et activez les packages de pilotes ODBC à partir du package/média SUSE Linux Enterprise Server (SLES) officiels à l’aide de zypper, en procédant comme suit :

    ``` 
    sudo zypper update
    sudo zypper install unixODBC
    ```
4.  Autorisez la connectivité à Internet de la machine virtuelle pour permettre à cette dernière d’atteindre Azure, comme décrit dans la procédure ci-dessous.


### <a name="set-up-network-connectivity"></a>Configurer la connectivité réseau

Pour toutes les opérations, la machine virtuelle SAP HANA nécessite une connectivité aux adresses IP publiques Azure. En l’absence d’une connectivité, les opérations de machine virtuelle (détection de bases de données, configuration de sauvegardes, planification de sauvegardes, restauration de points de récupération, etc.) échouent. Établissez la connectivité en autorisant l’accès aux plages d’adresses IP des centres de données Azure : 

- Vous pouvez télécharger les [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) des centres de données Azure, puis autoriser l’accès à ces adresses.
- Si vous utilisez des Groupes de sécurité réseau (NSG), vous pouvez utiliser la [balise de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud pour autoriser toutes les adresses IP publiques Azure. Vous pouvez modifier les règles NSG à l’aide de la [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0).

## <a name="onboard-to-the-public-preview"></a>Intégration à la préversion publique

Exécutez la procédure d’intégration à la préversion publique ci-après :

- Dans le portail, inscrivez votre ID d’abonnement auprès du fournisseur de services Recovery Services en [suivant les instructions de cet article](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Pour PowerShell, exécutez cette cmdlet. Ce processus doit s’achever en présentant la mention « Inscrit ».

    ```
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Détecter les bases de données


1. Dans le coffre, à la section **Prise en main**, cliquez sur **Sauvegarde**. Dans la zone **Où s’exécute votre charge de travail ?** , sélectionnez **SAP HANA dans les machines virtuelles Azure**.
2. Cliquez sur **Démarrer la découverte**. Cette opération lance la détection des machines virtuelles Linux non protégées dans la région du coffre.

   - Après la détection, les machines virtuelles non protégées apparaissent dans le portail en étant répertoriées par nom et groupe de ressources.
   - Si une machine virtuelle n’est pas répertoriée alors qu’elle devrait l’être, vérifiez si elle a déjà été sauvegardée dans un coffre.
   - Plusieurs machines virtuelles peuvent avoir le même nom, mais appartenir à différents groupes de ressources.

3. Dans **Sélectionner les machines virtuelles**, cliquez sur le lien pour télécharger le script qui autorise le service Sauvegarde Azure à accéder aux machines virtuelles SAP HANA pour la détection des bases de données.
4. Exécutez le script sur chacune des machines virtuelles hébergeant les bases de données SAP HANA que vous souhaitez sauvegarder.
5. Après avoir exécuté le script sur les machines virtuelles, dans **Sélectionner les machines virtuelles**, sélectionnez les machines souhaitées. Ensuite, cliquez sur **Découvrir les bases de données**.
6. Le service Sauvegarde Azure détecte toutes les bases de données SAP HANA résidant sur la machine virtuelle. Lors de la détection, le service Sauvegarde Azure inscrit la machine virtuelle auprès du coffre et y installe une extension. Aucun agent n’est installé sur la base de données.

    ![Détection des bases de données SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)
    
## <a name="configure-backup"></a>Configurer une sauvegarde  

Maintenant, activez la sauvegarde.

1. À l’étape 2, cliquez sur **Configurer la sauvegarde**.
2. Dans **Sélectionner les éléments à sauvegarder**, sélectionnez toutes les bases de données que vous souhaitez protéger, puis choisissez **OK**.
3. Dans **Stratégie de sauvegarde** > **Choisir une stratégie de sauvegarde**, créez une stratégie de sauvegarde pour les bases de données en suivant les instructions ci-dessous.
4. Après avoir créé la stratégie, dans le menu **Sauvegarde**, cliquez sur **Activer la sauvegarde**.
5. Suivez la progression de la configuration de la sauvegarde dans la zone  **Notifications**  du portail.

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde
Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées, ainsi que leur durée de rétention.

- Une stratégie est créée au niveau du coffre.
- Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.

Spécifiez les paramètres de stratégie comme suit :

1. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.
2. Dans **Stratégie de sauvegarde complète**, comme **Fréquence de sauvegarde**, sélectionnez **Tous les jours** ou **Toutes les semaines**.
   - **Daily (Quotidienne)** : sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde.
   
       - Vous devez exécuter une sauvegarde complète. Vous ne pouvez pas désactiver cette option.
       - Cliquez sur **Sauvegarde complète** pour afficher la stratégie.
       - Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.
       
   - **Hebdomadaire** : sélectionnez le jour de la semaine, l’heure et le fuseau horaire de début du travail de sauvegarde.
3. Dans **Durée de rétention**, configurez les paramètres de rétention pour la sauvegarde complète.
    - Par défaut, toutes les options sont sélectionnées. Désactivez les limites de durée de rétention que vous ne souhaitez pas utiliser et définissez celles qui vous intéressent.
    - La période de rétention minimale est de sept jours pour tous les types de sauvegardes (complète/différentielle/fichier journal).
    - Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    - La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée de rétention hebdomadaire et aux paramètres.
    - Les durées de rétention mensuelle et annuelle ont le même comportement.

4. Dans le menu de stratégie **Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
5. Sélectionnez **Sauvegarde différentielle** pour ajouter une stratégie différentielle.
6. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.
    - Vous pouvez déclencher au plus une sauvegarde différentielle par jour.
    - Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Si vous avez besoin d’une durée de rétention supérieure, vous devez utiliser des sauvegardes complètes.

    > [!NOTE]
    > Pour l’instant, les sauvegardes incrémentielles ne sont pas prises en charge. 

7. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu **Stratégie de sauvegarde** principal.
8. Sélectionnez **Sauvegarde de fichier journal** pour ajouter une stratégie de sauvegarde de fichier journal.
    - Dans **Sauvegarde de fichier journal**, sélectionnez **Activer**.
    - Définissez les contrôles de fréquence et de rétention.

    > [!NOTE]
    > Les sauvegardes de fichiers journaux ne commencent à s’effectuer qu’en cas de réussite d’une sauvegarde complète.

9. Cliquez sur **OK** pour enregistrer la stratégie et revenir au menu **Stratégie de sauvegarde** principal.
10. Après avoir défini la stratégie de sauvegarde, cliquez sur **OK**.


## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Les sauvegardes s’exécutent conformément à la planification de la stratégie. Vous pouvez exécuter une sauvegarde à la demande en procédant comme suit :


1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Dans **Éléments de sauvegarde**, sélectionnez la machine virtuelle exécutant la base de données SAP HANA, puis cliquez sur **Sauvegarder maintenant**.
3. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner le dernier jour de rétention du point de récupération. Cliquez ensuite sur **OK**.
4. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre en sélectionnant **Travaux de sauvegarde** > En cours d’exécution. Selon la taille de votre base de données, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Exécuter une sauvegarde SAP HANA Studio d’une base de données pour laquelle le service Sauvegarde Azure est activé

Si vous souhaitez effectuer une sauvegarde locale (à l’aide de HANA Studio) d’une base de données en cours de sauvegarde par le service Sauvegarde Azure, procédez comme suit :

1. Attendez la fin de toute sauvegarde complète ou de fichier journal de la base de données. Vérifiez l’état dans SAP HANA Studio.
2. Désactivez les sauvegardes de fichiers journaux, puis définissez le catalogue de sauvegarde sur le système de fichiers pour la base de données appropriée.
3. Pour ce faire, double-cliquez sur **systemdb** > **Configuration (Configuration)**  > **Select Database (Sélectionner la base de données)**  > **Filter (Log) (Filtrer [journal])** .
4. Définissez **enable_auto_log_backup** sur **No (Non)** .
5. Définissez **log_backup_using_backint** sur **False (Faux)** .
6. Effectuez une sauvegarde complète ad hoc de la base de données.
7. Attendez la fin de la sauvegarde complète et de la sauvegarde du catalogue.
8. Rétablissez les paramètres précédents sur les valeurs pour Azure :
    - Définissez **enable_auto_log_backup** sur **Yes (Oui)** .
    - Définissez **log_backup_using_backint** sur **True (Vrai)** .



## <a name="next-steps"></a>Étapes suivantes

[Apprenez-en davantage](backup-azure-arm-vms-prepare.md) sur la sauvegarde des machines virtuelles Azure.


