---
title: Configurer l’analyse des dépendances sans agent dans l’outil d’évaluation de serveur Azure Migrate
description: Configurez l’analyse des dépendances sans agent dans l’outil d’évaluation de serveur Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045851"
---
# <a name="analyze-server-dependencies-agentless"></a>Analyser les dépendances de serveur (sans agent)

Cet article explique comment configurer une analyse des dépendances sans agent à l’aide de l’outil d’évaluation de serveur d’Azure Migrate. L’[analyse des dépendances](concepts-dependency-visualization.md) vous aide à identifier et à comprendre les dépendances des serveurs à des fins l’évaluation et de migration vers Azure.

> [!IMPORTANT]
> L’analyse des dépendances sans agent est actuellement en préversion pour les serveurs s’exécutant dans votre environnement VMware, découverts avec l’outil d’évaluation de serveur d’Azure Migrate.
> Cette préversion est couverte par le support client et peut être utilisée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limites actuelles

- L’affichage de l’analyse des dépendances ne vous permet pas actuellement d’ajouter ou de supprimer un serveur dans un groupe.
- Aucune carte des dépendances pour un groupe de serveurs n’est disponible actuellement.
- Dans un projet Azure Migrate, vous pouvez activer la collecte de données de dépendances simultanément pour 1 000 serveurs. Vous pouvez analyser un plus grand nombre de serveurs grâce à un séquencement de blocs de 1 000 serveurs.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé un projet Azure Migrate](./create-manage-projects.md) auquel vous avez ajouté l’outil d’évaluation de serveur Azure Migrate.
- Examinez la [configuration requise de VMware](migrate-support-matrix-vmware.md#vmware-requirements) pour une analyse des dépendances.
- Examinez la [configuration requise de l’appliance](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) avant de configurer celle-ci.
- [Examinez la configuration requise de l’analyse des dépendances](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) avant d’activer celle-ci sur les serveurs.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Déployer et configurer l’appliance Azure Migrate

1. [Examinez](migrate-appliance.md#appliance---vmware) la configuration requise pour le déploiement de l’appliance Azure Migrate.
2. Passez en revue les URL Azure auxquelles l’appliance devra accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et les [clouds Government](migrate-appliance.md#government-cloud-urls).
3. [Passez en revue les données](migrate-appliance.md#collected-data---vmware) que l’appliance collecte pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-vmware.md#port-access-requirements) les conditions d’accès aux ports pour l’appliance.
5. [Déployez l’appliance Azure Migrate](how-to-set-up-appliance-vmware.md) pour démarrer la découverte. Pour déployer l’appliance, vous téléchargez et importez un modèle OVA dans VMware afin de créer un serveur s’exécutant dans votre vCenter Server. Après avoir déployé l’appliance, vous devez l’inscrire auprès du projet Azure Migrate et la configurer pour lancer la découverte.
6. Quand vous configurez l’appliance, vous devez spécifier les éléments suivants dans le gestionnaire de configuration de l’appliance :
    - détails du vCenter Server auquel vous souhaitez vous connecter ;
    - informations d’identification de vCenter Server étendues pour découvrir les serveurs dans votre environnement VMware ;
    - informations d’identification du serveur, qui peuvent être des informations d’identification de domaine/Windows (hors domaine)/Linux (hors domaine). [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.

## <a name="verify-permissions"></a>Vérification des autorisations

- Vous devez [créer un compte vCenter Server en lecture seule](./tutorial-discover-vmware.md#prepare-vmware) pour la découverte et l’évaluation. Le compte en lecture seule nécessite des privilèges pour **Machines virtuelles** > **Opérations d’invité** afin de pouvoir interagir avec les serveurs pour la collecte des données de dépendance.
- Vous avez besoin d’un compte d’utilisateur pour permettre à l’outil l’évaluation du serveur d’accéder au serveur pour la collecte des données de dépendance. [Découvrez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) la configuration requise de compte pour les serveurs Windows et Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Ajouter les informations d’identification et lancer la découverte

1. Ouvrez le gestionnaire de configuration d’appliance, puis procédez aux vérifications préalable et à l’inscription de l’appliance.
2. Accédez au panneau **Gérer les informations d’identification et les sources de découverte**.
1.  À l’**Étape 1 : Fournir les informations d’identification de vCenter Server**, cliquez sur **Ajouter des informations d’identification** afin de fournir les informations d’identification du compte vCenter Server que l’appliance utilisera pour découvrir les serveurs s’exécutant sur le vCenter Server.
1. À l’**Étape 2 : Fournir les détails de vCenter Server**, cliquez sur **Ajouter une source de découverte** afin de sélectionner le nom convivial des informations d’identification dans la liste déroulante, puis spécifiez l’**adresse IP/nom de domaine complet** de l’instance vCenter Server. :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panneau 3 du gestionnaire de configuration de l’appliance pour les détails de vCenter Server":::
1. À l’**Étape 3 : Fournir les informations d’identification de serveur pour effectuer l’inventaire logiciel, l’analyse des dépendances sans agent et la découverte des instances et bases de données SQL Server**, cliquez sur **Ajouter les informations d’identification** pour fournir plusieurs informations d’identification de serveur afin de lancer un inventaire logiciel.
1. Cliquez sur **Démarrer la découverte** pour lancer la découverte de vCenter Server.

 Une fois la découverte de vCenter Server terminée, l’appliance lance la détection des applications, rôles et fonctionnalités installés (inventaire logiciel). Au cours de l’inventaire logiciel, les informations d’identification des serveurs ajoutées sont comparées aux serveurs et validées pour l’analyse des dépendances sans agent. Vous pouvez activer l’analyse des dépendances sans agent pour les serveurs à partir du portail. Seuls les serveurs dont la validation réussit peuvent être sélectionnés pour activer l’analyse des dépendances sans agent.

## <a name="start-dependency-discovery"></a>Démarrer la découverte de dépendance

Choisissez les serveurs sur lesquels vous voulez activer la découverte de dépendances.

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Choisissez le **Nom de l’appliance** dont vous voulez examiner la découverte.
1. Vous pouvez voir l’état de validation des serveurs dans la colonne **Dépendances (sans agent)** .
1. Cliquez sur la liste déroulante **Analyse des dépendances**.
1. Cliquez sur **Ajouter des serveurs**.
1. Dans la page **Ajouter des serveurs**, sélectionnez les serveurs sur lesquels vous souhaitez activer l’analyse des dépendances. Vous ne pouvez activer le mappage des dépendances que sur les serveurs dont la validation a réussi. Le cycle de validation suivant s’exécutera 24 heures après le dernier horodatage de validation.
1. Après avoir sélectionné les serveurs, cliquez sur **Ajouter des serveurs**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Démarrer l’analyse des dépendances":::

Vous pouvez visualiser les dépendances environ six heures après avoir activé l’analyse des dépendances sur les serveurs. Si vous souhaitez activer simultanément plusieurs serveurs pour l’analyse des dépendances, vous pouvez utiliser [PowerShell](#start-or-stop-dependency-analysis-using-powershell).

## <a name="visualize-dependencies"></a>Visualiser les dépendances

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
1. Choisissez le **Nom de l’appliance** dont vous voulez examiner la découverte.
1. Recherchez le serveur dont vous souhaitez examiner les dépendances.
1. Dans la colonne **Dépendances (sans agent)** , cliquez sur **Afficher les dépendances**.
1. Modifiez la période pendant laquelle vous souhaitez afficher la carte à l’aide de la liste déroulante **Durée**.
1. Développez le groupe **Client** pour afficher la liste des serveurs dépendant du serveur sélectionné.
1. Développez le groupe **Port** pour afficher la liste des serveurs dépendant du serveur sélectionné.
1. Pour accéder à l’affichage cartographique d’un serveur dépendant, cliquez sur le nom de celui-ci > **Charger la carte des serveurs**
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Développer un groupe de ports du serveur et charger une carte des serveurs":::.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Développer un groupe de clients":::

8. Développez le serveur sélectionné pour afficher les détails au niveau processus pour chaque dépendance.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Développer un serveur pour afficher les processus":::

> [!NOTE]
> Les informations de processus pour une dépendance ne sont pas toujours disponibles. Si elles ne sont pas disponible, la dépendance est représentée par le processus marqué comme « Processus inconnu ».

## <a name="export-dependency-data"></a>Exporter les données de dépendance

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Cliquez sur la liste déroulante **Analyse des dépendances**.
3. Cliquez sur **Exporter les dépendances d’application**.
4. Dans la page **Exporter les dépendances d’application**, choisissez le nom de l’appliance qui découvre les serveurs souhaités.
5. Sélectionnez l’heure de début et l’heure de fin. Notez que vous pouvez télécharger les données uniquement pour les 30 derniers jours.
6. Cliquez sur **Exporter la dépendance**.

Les données de dépendances sont exportées et téléchargées dans un format CSV. Le fichier téléchargé contient les données de dépendances de tous les serveurs activés pour l’analyse des dépendances. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Exporter les dépendances":::

### <a name="dependency-information"></a>Informations sur les dépendances

Chaque ligne du fichier CSV exporté correspond à une dépendance observée dans le créneau spécifié.

Le tableau suivant récapitule les champs du fichier CSV exporté. Notez que les champs de nom de serveur, d’application et de processus sont uniquement renseignés pour les serveurs sur lesquels l’analyse des dépendances sans agent est activée.

**Nom du champ** | **Détails**
--- | --- 
Créneau | Créneau pendant lequel la dépendance a été observée. <br/> Les données de dépendances sont actuellement capturées sur des créneaux de six heures.
Nom du serveur source | Nom du serveur source 
Application source | Nom de l’application sur le serveur source  
Processus source | Nom du processus sur le serveur source  
Nom du serveur de destination | Nom du serveur de destination
IP de destination | Adresse IP du serveur de destination
Application de destination | Nom de l’application sur le serveur de destination
Processus de destination | Nom du processus sur le serveur de destination  
Port de destination | Numéro de port sur le serveur de destination

## <a name="stop-dependency-discovery"></a>Arrêter la découverte de dépendance

Sélectionnez les serveurs sur lesquels vous voulez arrêter la découverte des dépendances.

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
1. Choisissez le **Nom de l’appliance** dont vous voulez examiner la découverte.
1. Cliquez sur la liste déroulante **Analyse des dépendances**.
1. Cliquez sur **Supprimer des serveurs**.
1. Dans la page **Supprimer des serveurs**, sélectionnez le serveur que vous souhaitez arrêter pour l’analyse des dépendances.
1. Après avoir sélectionné les serveurs, cliquez sur **Supprimer les serveurs**.

Si vous souhaitez arrêter une dépendance simultanément sur plusieurs serveurs, vous pouvez utiliser [PowerShell](#start-or-stop-dependency-analysis-using-powershell).

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Démarrer ou arrêter une analyse des dépendances à l’aide de PowerShell

Téléchargez le module PowerShell à partir du dépôt [Exemples Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) sur GitHub.

### <a name="log-in-to-azure"></a>Connexion à Azure

1. Connectez-vous à votre abonnement Azure à l’aide de l’applet de commande Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Si vous utilisez Azure Government, utilisez la commande suivante.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Sélectionnez l’abonnement dans lequel vous avez créé le projet Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importer le module PowerShell AzMig_Dependencies téléchargé

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Activer ou désactiver la collecte de données de dépendance

1. Récupérez la liste des serveurs découverts dans votre projet Azure Migrate à l’aide des commandes suivantes. Dans l’exemple ci-dessous, le nom du projet est FabrikamDemoProject et le groupe de ressources auquel il appartient est FabrikamDemoRG. La liste des serveurs est enregistrée dans FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    Dans le fichier, vous pouvez voir le nom d’affichage du serveur, l’état actuel de la collecte des dépendances et l’ID ARM de tous les serveurs découverts. 

2. Pour activer ou désactiver les dépendances, créez un fichier CSV d’entrée. Le fichier doit contenir une colonne avec l’en-tête « ID ARM ». Tous les en-têtes supplémentaires du fichier CSV seront ignorés. Vous pouvez créer le CSV à l’aide du fichier généré à l’étape précédente. Créez une copie du fichier contenant les serveurs sur lesquels vous souhaitez activer ou désactiver les dépendances. 

    Dans l’exemple suivant, l’analyse des dépendances est activée sur la liste de serveurs figurant dans le fichier d’entrée FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    Dans l’exemple suivant, l’analyse des dépendances est désactivée sur la liste de serveurs figurant dans le fichier d’entrée FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualiser les connexions réseau dans Power BI

Azure Migrate propose un modèle de Power BI que vous pouvez utiliser pour visualiser simultanément les connexions réseau de nombreux serveurs, et filtrer par processus et par serveur. Pour visualiser, chargez le Power BI avec des données de dépendance, comme indiqué dans les instructions ci-dessous.

1. Téléchargez le module PowerShell et le modèle Power Bi à partir du dépôt [Exemples Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) sur GitHub.

2. Connectez-vous à Azure à l’aide des instructions ci-dessous : 
    - Connectez-vous à votre abonnement Azure à l’aide de l’applet de commande Connect-AzAccount.

        ```PowerShell
        Connect-AzAccount
        ```

    - Si vous utilisez Azure Government, utilisez la commande suivante.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Sélectionnez l’abonnement dans lequel vous avez créé le projet Azure Migrate

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importer le module PowerShell AzMig_Dependencies téléchargé

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Exécutez la commande suivante : Cette commande télécharge les données de dépendances dans un CSV et les traite pour générer une liste de dépendances uniques qui peuvent être utilisées pour la visualisation dans Power BI. Dans l’exemple ci-dessous, le nom du projet est FabrikamDemoProject et le groupe de ressources auquel il appartient est FabrikamDemoRG. Les dépendances sont téléchargées pour les serveurs découverts par FabrikamAppliance. Les dépendances uniques seront enregistrées dans FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Ouvrir le modèle Power BI téléchargé

6. Chargez les données de dépendance téléchargées dans Power BI.
    - Ouvrez le modèle dans Power BI.
    - Cliquez sur **Obtenir les données** dans la barre d’outils. 
    - Choisissez **Texte/CSV** à partir de sources de données communes.
    - Choisissez le fichier de dépendances téléchargé.
    - Cliquez sur **Charger**.
    - Vous verrez qu’une table est importée avec le nom du fichier CSV. Vous pouvez voir la table dans la barre des champs située à droite. Renommez-la AzMig_Dependencies
    - Cliquez sur Actualiser dans la barre d’outils.

    Le graphique Connexions réseau et le nom du serveur source, le nom du serveur de destination, le nom du processus source, les segments de nom du processus de destination doivent s’allumer avec les données importées.

7. Visualisez la carte du filtrage des connexions réseau par les serveurs et les processus. Enregistrez votre fichier.

## <a name="next-steps"></a>Étapes suivantes

[Grouper des serveurs](how-to-create-a-group.md) pour l’évaluation.