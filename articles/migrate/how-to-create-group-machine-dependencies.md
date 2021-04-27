---
title: Configurer l’analyse des dépendances basée sur agent dans Azure Migrate.
description: Cet article explique comment configurer l’analyse des dépendances basée sur agent dans Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714773"
---
# <a name="set-up-dependency-visualization"></a>Configurer la visualisation des dépendances

Cet article explique comment configurer l’analyse des dépendances basée sur agent dans Azure Migrate : découverte et évaluation. L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier et de comprendre les dépendances entre les serveurs que vous souhaitez évaluer et faire migrer vers Azure.

## <a name="before-you-start"></a>Avant de commencer

- Examinez les exigences de support et de déploiement pour l’analyse des dépendances basées sur l’agent pour :
    - [Serveurs dans un environnement VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Serveurs physiques](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Serveurs dans un environnement Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Assurez-vous que vous remplissez les critères suivants :
    - Vous disposez d’un projet Azure Migrate. Si ce n’est pas le cas, [créez-en un](./create-manage-projects.md) maintenant.
    - Vérifiez que vous avez [ajouté](how-to-assess.md) l’outil Azure Migrate : découverte et évaluation au projet.
    - Vous avez configuré une [appliance Azure Migrate](migrate-appliance.md) pour découvrir les serveurs locaux. L’appliance découvre les serveurs locaux et envoie des métadonnées et des données de performances à Azure Migrate : découverte et évaluation. Configurer une appliance pour :
        - [Serveurs dans un environnement VMware](how-to-set-up-appliance-vmware.md)
        - [Serveurs dans un environnement Hyper-V](how-to-set-up-appliance-hyper-v.md)
        - [Serveurs physiques](how-to-set-up-appliance-physical.md)
- Pour utiliser la visualisation des dépendances, associez un [espace de travail Log Analytics](../azure-monitor/logs/manage-access.md) à un projet Azure Migrate :
    - Vous pouvez attacher un espace de travail uniquement après avoir configuré l’appliance Azure Migrate et découvert les serveurs dans le projet Azure Migrate.
    - Veillez à disposer d’un espace de travail dans l’abonnement qui contient le projet Azure Migrate.
    - L’espace de travail doit résider dans les régions USA Est, Asie Sud-Est ou Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet.
    - L’espace de travail doit se trouver au sein d’une région dans laquelle [Service Map est pris en charge](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - Vous pouvez associer un espace de travail Log Analytics nouveau ou déjà existant à un projet Azure Migrate.
    - Vous attachez l’espace de travail la première fois que vous configurez la visualisation des dépendances pour un serveur. L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté.
    - Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet de migration et le nom du projet.

## <a name="associate-a-workspace"></a>Associer un espace de travail

1. Après avoir découvert les serveurs à évaluer, dans **Serveurs** > **Azure Migrate : découverte et évaluation**, cliquez sur **Vue d’ensemble**.  
2. Dans **Azure Migrate : découverte et évaluation**, cliquez sur **Essentials**.
3. Dans **Espace de travail OMS**, cliquez sur **Configuration obligatoire**.

     ![Configurer l’espace de travail Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Dans **Configurer l’espace de travail OMS**, indiquez si vous souhaitez créer un espace de travail, ou en utiliser un qui existe déjà.
    - Vous pouvez sélectionner un espace de travail existant parmi tous les espaces de travail de l’abonnement du projet.
    - Vous devez disposer d’un accès de lecteur à l’espace de travail pour pouvoir l’associer.
5. Si vous créez un espace de travail, sélectionnez son emplacement.

    ![Ajouter un espace de travail](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Découvrez comment](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) configurer l’espace de travail OMS pour la connectivité de point de terminaison privé.  

## <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles

Sur chaque serveur à analyser, installez les agents.

> [!NOTE]
> Pour les serveurs surveillés par System Center Operations Manager 2012 R2 ou version ultérieure, il n’est pas nécessaire d’installer l’agent MMA. Service Map s’intègre à Operations Manager. [Suivez](../azure-monitor/vm/service-map-scom.md#prerequisites) l’aide à l’intégration.

1. Dans **Azure Migrate : découverte et évaluation**, cliquez sur **Serveurs découverts**.
2. Pour chaque serveur à analyser à l’aide de la visualisation des dépendances, dans la colonne **Dépendances**, cliquez sur **Installation de l’agent requise**.
3. Dans la page **Dépendances**, téléchargez l’agent MMA et Dependency Agent pour Windows ou Linux.
4. Sous **Configurer l'agent MMA**, copiez l'ID et la clé de l'espace de travail. Ces éléments sont nécessaires lorsque vous installez l’agent MMA.

    ![Installer les agents](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installer MMA

Installez MMA sur chaque serveur Windows ou Linux à analyser.

### <a name="install-mma-on-a-windows-server"></a>Installer MMA sur un serveur Windows

Pour installer l’agent sur un serveur Windows :

1. Double-cliquez sur l’agent téléchargé.
2. Sur la page d’**accueil**, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**.
4. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics** > **Suivant**.
5. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail Log Analytics. Collez l’ID et la clé de l’espace de travail que vous avez copiés sur le portail. Cliquez sur **Suivant**.

Vous pouvez installer l’agent à partir de la ligne de commande ou en utilisant une solution automatisée telle que Configuration Manager ou [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [En savoir plus](../azure-monitor/agents/log-analytics-agent.md#installation-options) sur l’utilisation de ces méthodes pour installer l’agent MMA.
- Ce [script](https://github.com/brianbar-MSFT/Install-MMA) permet également d’installer l’agent MMA.
- [En savoir plus](../azure-monitor/agents/agents-overview.md#supported-operating-systems) sur les systèmes d’exploitation Windows pris en charge par MMA.

### <a name="install-mma-on-a-linux-server"></a>Installer MMA sur un serveur Linux

Pour installer MMA sur un serveur Linux :

1. Transférez le groupe approprié (x86 ou x64) sur votre ordinateur Linux à l’aide de scp/sftp.
2. Installez le bundle avec l’argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Découvrez plus en détail](../azure-monitor/agents/agents-overview.md#supported-operating-systems) la liste des systèmes d’exploitation Linux pris en charge par MMA. 

## <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances

1. Pour installer le Dependency Agent sur un serveur Windows, double-cliquez sur le fichier d’installation, puis suivez l’Assistant.
2. Pour installer le Dependency Agent sur un serveur Linux, en tant que racine, exécutez la commande suivante :

    ```sh InstallDependencyAgent-Linux64.bin```

- [En savoir plus](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) sur la façon dont vous pouvez utiliser des scripts pour installer l’agent de dépendances.
- [En savoir plus](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) sur les systèmes d’exploitation pris en charge par l’agent de dépendances.


## <a name="create-a-group-using-dependency-visualization"></a>Créer un groupe à l’aide de la visualisation des dépendances

Créez à présent un groupe pour l’évaluation. 


> [!NOTE]
> Les groupes pour lesquels vous souhaitez visualiser les dépendances ne doivent pas contenir plus de 10 serveurs. Si vous avez plus de 10 serveurs, divisez-les en groupes plus petits.

1. Dans **Azure Migrate : découverte et évaluation**, cliquez sur **Serveurs découverts**.
2. Dans la colonne **Dépendances**, cliquez sur **Afficher les dépendances** pour chaque serveur que vous voulez examiner.
3. Dans le mappage des dépendances, vous pouvez voir les éléments suivants :
    - Connexions TCP entrantes (clients) et sortantes (serveurs) vers et depuis le serveur.
    - Les serveurs dépendants sur lesquels les agents de dépendance ne sont pas installés sont regroupés par numéro de port.
    - Les serveurs dépendants sur lesquels des agents de dépendance sont installés sont affichés dans des zones distinctes.
    - Processus en cours d’exécution à l’intérieur du serveur. Développez la zone de chaque serveur pour afficher les processus.
    - Propriétés du serveur ( incluant le nom de domaine complet, le système d’exploitation, l’adresse MAC). Cliquez la zone de chaque serveur pour afficher les détails.

4. Vous pouvez examiner les dépendances pour différentes durées en cliquant sur la durée dans l’étiquette de l’intervalle de temps.
    - Par défaut, il est fixé à une heure. 
    - Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.
    - L’intervalle de temps peut atteindre une heure. Si vous avez besoin d’un intervalle plus long, utilisez Azure Monitor pour interroger les données dépendantes pendant une période plus longue.

5. Une fois que vous avez identifié des serveurs dépendants à grouper, utilisez la commande Ctrl+Clic pour sélectionner plusieurs serveurs sur la carte, puis cliquez sur **Grouper les machines**.
6. Spécifiez un nom de groupe.
7. Vérifiez que les serveurs dépendants sont découverts par Azure Migrate.

    - Si Azure Migrate : découverte et évaluation ne découvre pas un serveur dépendant, vous ne pouvez pas l’ajouter au groupe.
    - Pour ajouter un serveur, relancez la découverte et vérifiez que le serveur est découvert.

8. Si vous souhaitez créer une évaluation pour ce groupe, cochez la case prévue à cet effet.
8. Cliquez sur **OK** pour enregistrer le groupe.

Après avoir créé le groupe, nous vous recommandons d’installer les agents sur tous les serveurs du groupe, puis de visualiser les dépendances pour le groupe entier.

## <a name="query-dependency-data-in-azure-monitor"></a>Interroger les données de dépendance dans Azure Monitor

Vous pouvez interroger les données de dépendance capturées par Service Map dans l’espace de travail Log Analytics associé au projet Azure Migrate. Log Analytics est utilisé pour écrire et exécuter des requêtes de journal Azure Monitor.

- [Découvrez comment](../azure-monitor/vm/service-map.md#log-analytics-records) rechercher des données Service Map dans Log Analytics.
- [Obtenez une vue d’ensemble](../azure-monitor/logs/get-started-queries.md)  de l’écriture de requêtes de journal dans [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Exécutez une requête pour les données de dépendance comme suit :

1. Après avoir installé les agents, accédez au portail et cliquez sur **Vue d’ensemble**.
2. Dans **Azure Migrate : découverte et évaluation**, cliquez sur **Vue d’ensemble**. Cliquez sur la flèche vers le bas pour développer **Éléments principaux**.
3. Dans **Espace de travail OMS**, cliquez sur le nom de l’espace de travail.
3. Sur la page de l’espace de travail Log Analytics, cliquez sur > **Général**, **Journaux d'activité**.
4. Écrivez votre requête, puis cliquez sur **Exécuter**.

### <a name="sample-queries"></a>Exemples de requêtes

Voici quelques exemples de requêtes qui vous permettent d’extraire des données de dépendance.

- Vous pouvez modifier les requêtes pour extraire les points de données de votre choix.
- [Consultez](../azure-monitor/vm/service-map.md#log-analytics-records) une liste complète d’enregistrements de données de dépendance.
- [Consultez](../azure-monitor/vm/service-map.md#sample-log-searches) d’autres exemples de requêtes.

#### <a name="sample-review-inbound-connections"></a>Exemple : Examiner les connexions entrantes

Examinez les connexions entrantes pour un ensemble de serveurs.

- Les enregistrements de la table des métriques de connexion (VMConnection), ne représentent pas des connexions réseau physiques individuelles.
- Différentes connexions réseau physiques sont regroupées au sein d'une connexion logique.
- [En savoir plus](../azure-monitor/vm/service-map.md#connections) sur la manière dont les données de connexion réseau physique sont agrégées dans VMConnection.

```
// the servers of interest
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

#### <a name="sample-summarize-sent-and-received-data"></a>Exemple : Résumer les données envoyées et reçues

Cet exemple résume le volume de données envoyées et reçues sur les connexions entrantes entre un ensemble de serveurs.

```
// the servers of interest
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

[Créez une évaluation](how-to-create-assessment.md) pour un groupe.
