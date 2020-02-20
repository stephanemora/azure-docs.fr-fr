---
title: Grouper des machines à l’aide des dépendances de machine avec Azure Migrate | Microsoft Docs
description: Explique comment créer une évaluation à l’aide des dépendances de machine avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: bf6798d557cb1d27030565e4706864e945de6f04
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472092"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Configurer la visualisation des dépendances pour l'évaluation

Cet article explique comment configurer le mappage de dépendances dans Azure Migrate : Server Assessment.

Le mappage des dépendances vous permet de visualiser les dépendances entre les machines que vous voulez utiliser et migrer.

- Dans Azure Migrate : Server Assessment, vous rassemblez les machines à évaluer. Généralement des machines que vous souhaitez migrer ensemble.
- En général, vous utilisez le mappage des dépendances lorsque vous souhaitez évaluer les groupes avec des niveaux de confiance élevés.
- Le mappage des dépendances vous aide à vérifier les dépendances des machines avant d'exécuter une évaluation et une migration.
- Le mappage et la visualisation des dépendances vous aident à planifier efficacement votre migration vers Azure. Cela permet de s'assurer que rien n'est laissé en arrière, évitant ainsi les pannes surprises lors de la migration.
- Grâce au mappage, vous pouvez découvrir les systèmes interdépendants devant migrer en même temps. Vous pouvez également déterminer si un système en cours d’exécution continue de servir les utilisateurs ou si une mise hors service peut être envisagée au lieu de la migration.

[En savoir plus](concepts-dependency-visualization.md#how-does-it-work) sur visualisation de dépendance.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](how-to-add-tool-first-time.md) un projet Azure Migrate.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) Azure Migrate : Server Assessment.
- Assurez-vous d'avoir découvert vos machines dans Azure Migrate ; vous pouvez le faire en configurant une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)


**Caractéristiques** | **Remarque**
--- | ---
Disponibilité | La visualisation des dépendances n’est pas disponible dans Azure Government.
Service Map | La visualisation des dépendances utilise la solution Service Map dans Azure Monitor. [Service Map](../azure-monitor/insights/service-map.md) détecte et affiche automatiquement les connexions entre serveurs.
Agents | Pour utiliser la visualisation des dépendances, installez les agents suivants sur les machines que vous voulez mapper :<br/> - [Agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (anciennement Microsoft Monitoring Agent (MMA)).<br/> - [Service Map Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Pour automatiser l’installation de l’agent, vous pouvez utiliser un outil de déploiement tel que Configuration Manager, qui propose une solution de déploiement d’agent pour Azure Migrate.
Agent de dépendances | Passez en revue la [prise en charge de Dependency Agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) pour Windows et Linux.<br/><br/> [En savoir plus](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sur l’utilisation des scripts pour installer l’agent de dépendances.
Log Analytics agent (MMA) | [En savoir](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) plus sur les méthodes d’installation MMA.<br/><br/> Pour les machines surveillées par System Center Operations Manager 2012 R2 ou version ultérieure, il n'est pas nécessaire d'installer l'agent MMA. Service Map s’intègre à Operations Manager. Vous pouvez activer l’intégration en suivant les conseils disponibles [ici](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Notez toutefois que Dependency Agent doit être installé sur ces machines.<br/><br/> [Passez en revue](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) les systèmes d'exploitation Linux pris en charge par l'agent Log Analytics.
Groupes d’évaluation | Les groupes pour lesquels vous souhaitez visualiser les dépendances ne doivent pas contenir plus de 10 machines. Si vous avez plus de 10 machines, divisez-les en petits groupes pour visualiser les dépendances.

## <a name="associate-a-log-analytics-workspace"></a>Associer un espace de travail Log Analytics

Pour utiliser la visualisation des dépendances, vous pouvez associer un [espace de travail Log Analytics](../azure-monitor/platform/manage-access.md) à un projet Azure Migrate.

- Vous pouvez uniquement joindre un espace de travail dans l'abonnement au projet Azure Migrate.
- Vous pouvez joindre un espace de travail existant ou en créer un.
- Vous joignez l'espace de travail la première fois que vous configurez la visualisation des dépendances pour une machine.
- Vous ne pouvez joindre un espace de travail qu'après avoir découvert des machines dans le projet Azure Migrate. Vous pouvez le faire en configurant une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)

Joignez un espace de travail comme suit :

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Vue d’ensemble**. Si vous n'avez pas encore ajouté l'outil Server Assessment, [faites-le en priorité](how-to-assess.md).
2. Dans **Vue d’ensemble**, cliquez sur la flèche vers le bas pour développer **Éléments principaux**.
3. Dans **Espace de travail OMS**, cliquez sur **Configuration obligatoire**.
4. Dans **Configurer l’espace de travail**, spécifiez si vous voulez créer un espace de travail ou utiliser un espace existant :

    ![Ajouter un espace de travail](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Une fois que vous avez spécifié un nom pour un nouvel espace de travail, vous pouvez choisir la [région](https://azure.microsoft.com/global-infrastructure/regions/) dans laquelle l’espace de travail sera créé.
    - Quand vous attachez un espace de travail existant, vous pouvez choisir parmi tous les espaces de travail disponibles dans le même abonnement que le projet de migration.
    - Vous avez besoin d’un accès Lecteur à l'espace de travail pour pouvoir le joindre.
    - Vous ne pouvez pas modifier l'espace de travail associé à un projet une fois qu'il est joint.

## <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles

Téléchargez et installez les agents sur chaque machine locale que vous souhaitez visualiser avec le mappage des dépendances.

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Pour chaque machine pour laquelle vous voulez utiliser la visualisation des dépendances, cliquez sur **Installation de l’agent requise**.
3. Dans la page **Dépendances** pour une machine > **Télécharger et installer MMA**, téléchargez l'agent approprié, et installez-le comme décrit ci-dessous.
4. Dans **Télécharger et installer Dependency Agent**, téléchargez l'agent approprié et installez-le comme décrit ci-dessous.
5. Sous **Configurer l'agent MMA**, copiez l'ID et la clé de l'espace de travail. Ces éléments sont nécessaires lorsque vous installez l’agent MMA.

### <a name="install-the-mma"></a>Installer MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installer l’agent sur une machine Windows

Pour installer l’agent sur une machine Windows :

1. Double-cliquez sur l’agent téléchargé.
2. Sur la page d’**accueil**, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**.
4. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics** > **Suivant**.
5. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail Log Analytics. Collez l’ID et la clé de l’espace de travail que vous avez copiés sur le portail. Cliquez sur **Suivant**.

Vous pouvez installer l’agent à partir de la ligne de commande ou en utilisant une solution automatisée telle que Configuration Manager ou [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sur l’utilisation de ces méthodes pour installer l’agent MMA. Ce [script](https://go.microsoft.com/fwlink/?linkid=2104394) permet également d’installer l’agent MMA.

[En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) sur les systèmes d’exploitation Windows pris en charge par MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installer l’agent sur une machine Linux

Pour installer l’agent sur une machine Linux :

1. Transférez le groupe approprié (x86 ou x64) sur votre ordinateur Linux à l’aide de scp/sftp.
2. Installez le bundle avec l’argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Découvrez plus en détail](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) la liste des systèmes d’exploitation Linux pris en charge par MMA. 

### <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances
1. Pour installer l’agent de dépendances sur une machine Windows, double-cliquez sur le fichier d’installation et suivez l’Assistant.
2. Pour installer l’agent de dépendances sur une machine Linux en tant que racine, exécutez la commande suivante :

    ```sh InstallDependencyAgent-Linux64.bin```

[En savoir plus](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) sur la façon dont vous pouvez utiliser des scripts pour installer l’agent de dépendances.

[En savoir plus](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) sur les systèmes d’exploitation pris en charge par l’agent de dépendances.


## <a name="create-a-group-using-dependency-visualization"></a>Créer un groupe à l’aide de la visualisation des dépendances

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Dans la colonne **Dépendances**, cliquez sur **Afficher les dépendances** pour chaque machine que vous voulez examiner.
3. Dans le mappage des dépendances, vous pouvez voir les éléments suivants :
    - Connexions TCP entrantes (clients) et sortantes (serveurs) vers et depuis la machine.
    - Les machines dépendantes sur lesquels les agents de dépendance ne sont pas installés sont regroupées par numéro de port.
    - Les machines dépendantes avec des agents de dépendance installés sont affichées dans des zones distinctes.
    - Processus en cours d’exécution à l’intérieur de la machine. Développez chaque boîte de machine pour afficher les processus.
    - Propriétés de la machine ( y compris le nom de domaine complet, le système d’exploitation, l’adresse MAC). Cliquez sur chaque zone de la machine pour afficher les détails.

4. Vous pouvez examiner les dépendances pour différentes durées en cliquant sur la durée dans l’étiquette de l’intervalle de temps. Par défaut, il est fixé à une heure. Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.

    > [!NOTE]
    > L’intervalle de temps peut atteindre une heure. Si vous avez besoin d’un intervalle plus long, utilisez Azure Monitor pour interroger les données dépendantes pendant une période plus longue.

5. Quand vous avez identifié des machines dépendantes à grouper, utilisez la commande Ctrl+clic pour les sélectionner sur le mappage, puis cliquez sur **Grouper les machines**.
6. Spécifiez un nom de groupe.
7. Vérifiez que les machines dépendantes sont découvertes par Azure Migrate.

    - Si une machine dépendante n'est pas découverte par Azure Migrate : Server Assessment, vous ne pouvez pas l'ajouter au groupe.
    - Pour ajouter une machine, relancez la découverte et vérifiez que la machine est découverte.

8. Si vous souhaitez créer une évaluation pour ce groupe, cochez la case prévue à cet effet.
8. Cliquez sur **OK** pour enregistrer le groupe.

Après avoir créé le groupe, nous vous recommandons d'installer les agents sur toutes les machines du groupe, puis de visualiser les dépendances pour l'ensemble du groupe.

## <a name="query-dependency-data-in-azure-monitor"></a>Interroger les données de dépendance dans Azure Monitor

Vous pouvez interroger les données de dépendance capturées par Service Map dans l'espace de travail Log Analytics associé à votre projet Azure Migrate. Log Analytics est utilisé pour écrire et exécuter des requêtes de journal Azure Monitor.

- [Découvrez comment](../azure-monitor/insights/service-map.md#log-analytics-records) rechercher des données Service Map dans Log Analytics.
- [Obtenez une vue d’ensemble](../azure-monitor/log-query/get-started-queries.md)  de l’écriture de requêtes de journal dans [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Exécutez une requête pour les données de dépendance comme suit :

1. Après avoir installé les agents, accédez au portail et cliquez sur **Vue d’ensemble**.
2. Dans **Azure Migrate : Server Assessment**, cliquez sur **Vue d’ensemble**. Cliquez sur la flèche vers le bas pour développer **Éléments principaux**.
3. Dans **Espace de travail OMS**, cliquez sur le nom de l’espace de travail.
3. Sur la page de l’espace de travail Log Analytics, cliquez sur > **Général**, **Journaux d'activité**.
4. Écrivez votre requête, puis cliquez sur **Exécuter**.

### <a name="sample-queries"></a>Exemples de requêtes

Nous fournissons quelques exemples de requêtes que vous pouvez utiliser pour extraire des données de dépendance.

- Vous pouvez modifier les requêtes pour extraire les points de données de votre choix.
- [Consultez](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) une liste complète d’enregistrements de données de dépendance.
- [Consultez](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) d’autres exemples de requêtes.

#### <a name="sample-review-inbound-connections"></a>Exemple : Examiner les connexions entrantes

Examiner les connexions entrantes pour un ensemble de machines virtuelles.

- Les enregistrements de la table des métriques de connexion (VMConnection), ne représentent pas des connexions réseau physiques individuelles.
- Différentes connexions réseau physiques sont regroupées au sein d'une connexion logique.
- [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sur la manière dont les données de connexion réseau physique sont agrégées dans VMConnection.

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
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Exemple : Résumer les données envoyées et reçues

Cet exemple résume le volume de données envoyées et reçues sur les connexions entrantes entre un ensemble de machines.

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

[Créez une évaluation](how-to-create-assessment.md) pour un groupe.
