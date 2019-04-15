---
title: Affiner un groupe d’évaluation avec mappage de dépendances de groupe dans Azure Migrate | Microsoft Docs
description: Explique comment affiner une évaluation à l’aide du mappage de dépendances de groupe dans le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357177"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Affiner un groupe à l’aide du mappage de dépendances de groupe

Cet article décrit comment affiner un groupe en visualisant les dépendances de tous les ordinateurs dans le groupe. On utilise généralement cette méthode pour affiner l’appartenance à un groupe existant, en vérifiant par recoupement les dépendances du groupe avant d’exécuter une évaluation. Le fait d’affiner un groupe à l’aide de la visualisation des dépendances peut vous aider à planifier efficacement votre migration vers Azure. Vous pouvez découvrir tous les systèmes interdépendants devant migrer en même temps. Elle vous permet de ne rien oublier et vous épargne les pannes inopinées pendant la migration vers Azure.


> [!NOTE]
> Les groupes pour lesquels vous souhaitez visualiser les dépendances ne doivent pas contenir plus de 10 machines. Si vous avez plus de 10 machines dans le groupe, nous vous recommandons de le diviser en groupes plus petits pour tirer parti des fonctionnalités de visualisation de dépendance.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>Préparer la visualisation des dépendances
Azure Migrate s’appuie sur la solution Service Map dans les journaux Azure Monitor pour permettre la visualisation des dépendances des machines.

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

Vous pouvez installer l’agent à partir de la ligne de commande ou à l’aide d’une méthode automatisée telle que System Center Configuration Manager. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sur l’utilisation de ces méthodes pour installer l’agent MMA.

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
   >    Actuellement, l’interface utilisateur de visualisation des dépendances ne prend pas en charge la sélection d’un intervalle de temps supérieur à une heure. Utiliser Azure Monitor enregistre dans [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) pendant une durée plus longue.

4. Vérifiez les machines dépendantes, le processus en cours d’exécution pour chaque machine et identifiez les machines qui doivent être ajoutées ou supprimées du groupe.
5. Utilisez Ctrl + clic pour sélectionner des machines sur la carte et les ajouter ou supprimer du groupe.
    - Seules les machines qui ont été détectées peuvent être ajoutées.
    - L’ajout et la suppression de machines au sein d’un groupe invalide ses évaluations passées.
    - Vous pouvez, si vous le souhaitez, créer une nouvelle évaluation lorsque vous modifiez le groupe.
5. Cliquez sur **OK** pour enregistrer le groupe.

    ![Ajouter ou supprimer des machines](./media/how-to-create-group-dependencies/add-remove.png)

Si vous souhaitez vérifier les dépendances d’une machine spécifique qui s’affichent dans le mappage de dépendances de groupe, [configurez le mappage de dépendances de la machine](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Interroger des données de dépendance à partir des journaux d’Azure Monitor

Les données de dépendance capturées par la carte de Service soient disponibles pour l’interrogation dans l’espace de travail Analytique de journal associé à votre projet Azure Migrate. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sur les tables de données de carte de Service pour interroger dans Azure Monitor enregistre. 

Pour exécuter des requêtes Kusto :

1. Après avoir installé les agents, accédez au portail et cliquez sur **Vue d’ensemble**.
2. Dans **Vue d’ensemble**, accédez à la section **Essentials** du projet et cliquez sur le nom de l’espace de travail fourni en regard de **Espace de travail OMS**.
3. Sur la page de l’espace de travail Log Analytics, cliquez sur **Général** > **Journaux d’activité**.
4. Écrivez votre requête pour collecter des données de dépendance à l’aide des journaux Azure Monitor. Rechercher des exemples de requêtes dans la section suivante.
5. Exécutez votre requête en cliquant sur Exécuter. 

[En savoir plus](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sur la façon d’écrire des requêtes de Kusto. 

## <a name="sample-azure-monitor-logs-queries"></a>Exemple Azure Monitor enregistre les requêtes

Voici des exemples de requêtes que vous pouvez utiliser pour extraire des données de dépendance. Vous pouvez modifier les requêtes pour extraire vos points de données par défaut. Une liste exhaustive des champs dans les enregistrements de données de dépendance est disponible [ici](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Rechercher des exemples de requêtes plus [ici](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Résumer les connexions entrantes sur un ensemble de machines

Notez que les enregistrements dans la table pour les métriques de connexion, VMConnection, ne représentent pas les connexions réseau physiques individuels. Plusieurs connexions réseau physiques sont regroupées dans une connexion logique. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sur la connexion de réseau physique comment les données sont agrégées en un seul enregistrement logique dans VMConnection. 

```
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Résumer le volume de données envoyées et reçues sur les connexions entrantes entre un ensemble d’ordinateurs

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez plus en détail](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) les questions fréquemment posées au sujet de la visualisation des dépendances.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
