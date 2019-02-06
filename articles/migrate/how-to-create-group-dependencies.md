---
title: Affiner un groupe d’évaluation avec mappage de dépendances de groupe dans Azure Migrate | Microsoft Docs
description: Explique comment affiner une évaluation à l’aide du mappage de dépendances de groupe dans le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 007f7fe95be77a2b1661cd6c82118eb875401f24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472573"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Affiner un groupe à l’aide du mappage de dépendances de groupe

Cet article décrit comment affiner un groupe en visualisant les dépendances de tous les ordinateurs dans le groupe. On utilise généralement cette méthode pour affiner l’appartenance à un groupe existant, en vérifiant par recoupement les dépendances du groupe avant d’exécuter une évaluation. Le fait d’affiner un groupe à l’aide de la visualisation des dépendances peut vous aider à planifier efficacement votre migration vers Azure. Vous pouvez découvrir tous les systèmes interdépendants devant migrer en même temps. Elle vous permet de ne rien oublier et vous épargne les pannes inopinées pendant la migration vers Azure.


> [!NOTE]
> Les groupes pour lesquels vous souhaitez visualiser les dépendances ne doivent pas contenir plus de 10 machines. Si vous avez plus de 10 machines dans le groupe, nous vous recommandons de le diviser en groupes plus petits pour tirer parti des fonctionnalités de visualisation de dépendance.


## <a name="prepare-for-dependency-visualization"></a>Préparer la visualisation des dépendances
Azure Migrate s’appuie sur la solution Service Map dans Log Analytics pour permettre la visualisation des dépendances des machines.

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

### <a name="associate-a-log-analytics-workspace"></a>Associer un espace de travail Log Analytics
Pour tirer parti de la visualisation des dépendances, vous pouvez associer un espace de travail Log Analytics, nouveau ou existant, à un projet Azure Migrate. Vous ne pouvez créer ou attacher un espace de travail que dans l’abonnement où le projet de migration est créé.

- Pour attacher un espace de travail Log Analytics à un projet, dans **Vue d’ensemble**, accédez à la section **Bases** du projet, puis cliquez sur **Requiert une configuration**

    ![Associer un espace de travail Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Lors de l’association d’un espace de travail, vous aurez la possibilité de créer un nouvel espace de travail ou de joindre un espace existant :
    - Quand vous créez un espace de travail, vous devez spécifier un nom pour celui-ci. L’espace de travail est ensuite créé dans une région appartenant à la même [zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que le projet de migration.
    - Quand vous attachez un espace de travail existant, vous pouvez choisir parmi tous les espaces de travail disponibles dans le même abonnement que le projet de migration. Notez que seuls les espaces de travail qui ont été créés dans une région où [Service Map est pris en charge](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions) sont listés. Pour être en mesure d’attacher un espace de travail, assurez-vous d’avoir accès en lecture à l’espace de travail.

> [!NOTE]
> Vous ne pouvez pas changer l’espace de travail associé à un projet de migration.

### <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles
Pour afficher les dépendances d’un groupe, vous devez télécharger et installer des agents sur chacune des machines locales faisant partie du groupe. En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la [passerelle Log Analytics](../azure-monitor/platform/gateway.md) sur ces machines.

1. Dans **Vue d’ensemble**, cliquez sur **Gérer** > **Groupes**, accédez au groupe requis.
2. Dans la liste des machines, dans la colonne **Agent de dépendances**, cliquez sur **Installation requise** pour afficher les instructions relatives au téléchargement et à l’installation des agents.
3. Sur la page **Dépendances**, téléchargez et installez Microsoft Monitoring Agent (MMA) et l’agent de dépendances sur chacune des machines virtuelles faisant partie du groupe.
4. Copiez l’ID et la clé de l’espace de travail. Vous en aurez besoin lorsque vous installerez MMA sur la machine locale.

### <a name="install-the-mma"></a>Installer MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installer l’agent sur une machine Windows

Pour installer l’agent sur une machine Windows :

1. Double-cliquez sur l’agent téléchargé.
2. Sur la page d’**accueil**, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**.
4. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics** > **Suivant**.
5. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail Log Analytics. Collez l’ID et la clé de l’espace de travail que vous avez copiés sur le portail. Cliquez sur **Suivant**.

Vous pouvez installer l’agent à partir de la ligne de commande ou d’une méthode automatisée telle qu’Azure Automation DSC ou System Center Configuration Manager, ou avec un modèle Azure Resource Manager si vous avez déployé Microsoft Azure Stack dans votre centre de données. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sur l’utilisation de ces méthodes pour installer l’agent MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installer l’agent sur une machine Linux

Pour installer l’agent sur une machine Linux :

1. Transférez le groupe approprié (x86 ou x64) sur votre ordinateur Linux à l’aide de scp/sftp.
2. Installez le bundle avec l’argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Installer l’agent sur une machine surveillée par System Center Operations Manager

Pour les machines surveillées par Operations Manager 2012 R2 ou version ultérieure, il n’est pas nécessaire d’installer l’agent MMA. Service Map dispose d’une intégration à Operations Manager qui tire parti de l’agent MMA d’Operations Manager pour recueillir les données de dépendance nécessaires. Vous pouvez activer l’intégration en suivant les conseils disponibles [ici](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Notez toutefois que l’agent de dépendance doit être installé sur ces machines.

### <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances
1. Pour installer l’agent de dépendances sur une machine Windows, double-cliquez sur le fichier d’installation et suivez l’Assistant.
2. Pour installer l’agent de dépendances sur une machine Linux en tant que racine, exécutez la commande suivante :

    ```sh InstallDependencyAgent-Linux64.bin```

Apprenez-en davantage sur la prise en charge de l’agent de dépendances pour les systèmes d’exploitation [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) et [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[En savoir plus](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sur la façon dont vous pouvez utiliser des scripts pour installer l’agent de dépendances.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Affiner le groupe en fonction de la visualisation de dépendance
Une fois que vous avez installé les agents sur toutes les machines du groupe, vous pouvez visualiser les dépendances du groupe et l’affiner en suivant les étapes ci-dessous.

1. Dans le projet Azure Migrate, sous **Gérer**, cliquez sur  **Groupes** et sélectionnez le groupe.
2. Sur la page du groupe, cliquez sur  **Afficher les dépendances** pour ouvrir le mappage de dépendances du groupe.
3. La carte des dépendances du groupe affiche les informations suivantes :
    - Connexions TCP entrantes (clients) et sortantes (serveurs) vers/depuis toutes les machines faisant partie du groupe
        - Les machines dépendantes sur lesquelles ne sont pas installés l’agent MMA et l’agent de dépendances sont regroupées par numéros de port
        - Les machines dépendantes sur lesquelles sont installés l’agent MMA et l’agent de dépendances apparaissent sous forme de zones distinctes
    - Processus en cours d’exécution dans la machine (vous pouvez développer chaque zone de machine pour afficher les processus correspondants)
    - Propriétés de chaque machine telles que Nom de domaine complet, Système d’exploitation ou Adresse MAC (vous pouvez cliquer sur chaque zone de machine pour afficher ces détails)

     ![Afficher les dépendances de groupe](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Pour afficher des dépendances plus précises, cliquez sur l’intervalle de temps et modifiez-le. Par défaut, il est fixé à une heure. Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.

    > [!NOTE]
      Actuellement, l’interface utilisateur de visualisation des dépendances ne prend pas en charge la sélection d’un intervalle de temps supérieur à une heure. Utilisez Log Analytics pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-a-group#query-dependency-data-from-log-analytics) sur une plus longue durée.

4. Vérifiez les machines dépendantes, le processus en cours d’exécution pour chaque machine et identifiez les machines qui doivent être ajoutées ou supprimées du groupe.
5. Utilisez Ctrl + clic pour sélectionner des machines sur la carte et les ajouter ou supprimer du groupe.
    - Seules les machines qui ont été détectées peuvent être ajoutées.
    - L’ajout et la suppression de machines au sein d’un groupe invalide ses évaluations passées.
    - Vous pouvez, si vous le souhaitez, créer une nouvelle évaluation lorsque vous modifiez le groupe.
5. Cliquez sur **OK** pour enregistrer le groupe.

    ![Ajouter ou supprimer des machines](./media/how-to-create-group-dependencies/add-remove.png)

Si vous souhaitez vérifier les dépendances d’une machine spécifique qui s’affichent dans le mappage de dépendances de groupe, [configurez le mappage de dépendances de la machine](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-log-analytics"></a>Interroger les données de dépendance à partir de Log Analytics

Les données de dépendance capturées par Service Map peuvent être interrogées dans l’espace de travail Log Analytics associé à votre projet Azure Migrate. [Découvrez-en plus](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sur les tables de données Service Map à interroger dans Log Analytics. 

Pour exécuter les requêtes Log Analytics :

1. Après avoir installé les agents, accédez au portail et cliquez sur **Vue d’ensemble**.
2. Dans **Vue d’ensemble**, accédez à la section **Essentials** du projet et cliquez sur le nom de l’espace de travail fourni en regard de **Espace de travail OMS**.
3. Sur la page de l’espace de travail Log Analytics, cliquez sur **Général** > **Journaux**.
4. Rédigez votre requête pour recueillir des données de dépendance à l’aide de Log Analytics. Des exemples de requêtes permettant de recueillir des données de dépendance sont disponibles [ici](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. Exécutez votre requête en cliquant sur Exécuter. 

[Découvrez-en plus](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sur la rédaction de requêtes Log Analytics. 


## <a name="next-steps"></a>Étapes suivantes
- [Découvrez plus en détail](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) les questions fréquemment posées au sujet de la visualisation des dépendances.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
