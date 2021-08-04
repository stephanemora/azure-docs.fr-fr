---
title: Vue d’ensemble de l’agent Azure Monitor
description: Vue d’ensemble de l’agent Azure Monitor (AMA), qui collecte des données de supervision à partir du système d’exploitation invité des machines virtuelles.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.custom: references_regions
ms.openlocfilehash: 7c12fff502d8c4b68470370e0a5eede1dd3866a9
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605366"
---
# <a name="azure-monitor-agent-overview"></a>Vue d’ensemble de l’agent Azure Monitor
L’agent Azure Monitor (AMA) collecte des données de supervision auprès du système d’exploitation invité des machines virtuelles Azure et les délivre à Azure Monitor. Cet article fournit une vue d’ensemble de l’agent Azure Monitor, notamment comment l’installer et comment configurer la collecte des données.

## <a name="relationship-to-other-agents"></a>Relation avec les autres agents
L’agent Azure Monitor remplace les agents antérieurs suivants qui sont actuellement utilisés par Azure Monitor pour collecter des données d’invités auprès des machines virtuelles ([voir les lacunes connues](/azure/azure-monitor/faq#is-the-new-azure-monitor-agent-at-parity-with-existing-agents)) :

- [Agent Log Analytics](./log-analytics-agent.md) : envoie des données à l’espace de travail Log Analytics et prend en charge Insights de machine virtuelle et les solutions de surveillance.
- [Extension Diagnostic](./diagnostics-extension-overview.md) : envoie des données aux Métriques Azure Monitor (Windows uniquement), à Azure Event Hubs et au Stockage Azure.
- [Agent Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) : envoie des données aux métriques Azure Monitor (Linux uniquement).

En plus de consolider cette fonctionnalité en un seul agent, l’agent Azure Monitor offre les avantages suivants par rapport aux agents existants :

- Étendue de l’analyse. Configurez de manière centralisée un regroupement de différents jeux de données à partir de différents ensembles de machines virtuelles.  
- Multihébergement Linux : Envoyer des données à partir de machines virtuelles Linux vers plusieurs espaces de travail.
- Filtrage d’événements Windows : Utilisez des requêtes XPATH pour filtrer les événements Windows qui sont collectés.
- Gestion améliorée des extensions : L’agent Azure Monitor utilise une nouvelle méthode de gestion de l’extensibilité qui est plus transparente et contrôlable que les packs d’administration et les plug-ins Linux dans les agents Log Analytics actuels.

### <a name="changes-in-data-collection"></a>Modifications de la collecte de données
Les méthodes permettant de définir la collecte de données pour les agents existants diffèrent les unes des autres, et chacune d’entre elles présente des défis qui sont résolus avec l’agent Azure Monitor.

- L’agent Log Analytics obtient sa configuration à partir d’un espace de travail Log Analytics. Il est facile de configurer de façon centralisée, mais il est difficile d’établir des définitions indépendantes pour différentes machines virtuelles. Il peut uniquement envoyer des données à un espace de travail Log Analytics.

- L’extension de diagnostic a une configuration pour chaque machine virtuelle. Définir des définitions indépendantes pour différentes machines virtuelles est facile, mais il est difficile de les gérer de façon centralisée. Il peut uniquement envoyer des données aux métriques Azure Monitor, à Azure Event Hubs ou au Stockage Azure. Pour les agents Linux, l’agent Telegraf open source est nécessaire pour envoyer des données aux métriques Azure Monitor.

L’agent Azure Monitor utilise des [Règles de collecte de données (DCR)](data-collection-rule-overview.md) pour configurer les données à collecter à partir de chaque agent. Les règles de collecte de données permettent de gérer les paramètres de regroupement à grande échelle tout en autorisant des configurations uniques et étendues pour les sous-ensembles d’ordinateurs. Elles ne dépendent pas de l’espace de travail et de la machine virtuelle, ce qui leur permet d’être définies une fois réutilisées par les ordinateurs et environnements. Consultez [Configurer la collecte de données pour l’agent Azure Monitor](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Dois-je basculer sur l’agent Azure Monitor ?
L’agent Azure Monitor remplace les [agents antérieurs pour Azure Monitor](agents-overview.md), et vous pouvez commencer à transférer vos machines virtuelles hors des agents actuels vers le nouvel agent en tenant compte des facteurs suivants :

- **Spécifications de l’environnement.** L’agent Azure Monitor prend actuellement en charge [ces systèmes d’exploitation](./agents-overview.md#supported-operating-systems). La prise en charge de versions futures des systèmes d’exploitation, la prise en charge de l’environnement et les exigences réseau seront probablement fournies dans ce nouvel agent. Vous devez déterminer si votre environnement est pris en charge ou non par l’agent Azure Monitor. Si ce n’est pas le cas, il peut être nécessaire de conserver l’agent actuel. Si l’agent Azure Monitor prend en charge votre environnement actuel, vous devriez envisager une transition vers celui-ci.
- **Spécifications des fonctionnalités actuelles et nouvelles.** L’agent Azure Monitor introduit plusieurs nouvelles fonctionnalités comme le filtrage, l’étendue et le multi-hébergement, mais il n’est pas encore sur un pied d’égalité avec les agents actuels pour d’autres fonctionnalités, comme la collecte de journaux personnalisée et l’intégration à toutes les solutions ([consultez les solutions en préversion](/azure/azure-monitor/faq#which-log-analytics-solutions-are-supported-on-the-new-azure-monitor-agent)). La plupart des nouvelles capacités d’Azure Monitor ne seront disponibles qu’avec l’agent Azure Monitor. Par conséquent, au fil du temps, d’autres fonctionnalités seront uniquement disponibles dans le nouvel agent. Déterminez si l’agent Azure Monitor dispose des fonctionnalités dont vous avez besoin et si vous pouvez vous passer temporairement de certaines fonctionnalités pour obtenir d’autres fonctionnalités importantes dans le nouvel agent. Si l’agent Azure Monitor dispose de toutes les fonctionnalités de base dont vous avez besoin, envisagez de passer à celui-ci. Si des fonctionnalités critiques vous sont nécessaires, continuez avec l’agent actuel jusqu’à ce que l’agent Azure Monitor les implémente.
- **Tolérance au remaniement.** Si vous configurez un nouvel environnement avec des ressources comme des scripts de déploiement et des modèles d’intégration, évaluez le travail que cela implique. Si cela représente une quantité importante de travail, envisagez de configurer votre nouvel environnement avec le nouvel agent, car il est maintenant en disponibilité générale. Une date de dépréciation sera publiée pour les agents Log Analytics en août 2021. Les agents actuels seront pris en charge pendant plusieurs années après le début de la dépréciation.

## <a name="supported-resource-types"></a>Types de ressources pris en charge
Les machines virtuelles Azure, les groupes de machines virtuelles identiques et les serveurs Azure Arc activés sont actuellement pris en charge. Azure Kubernetes Service et d’autres types de ressources de calcul ne sont pas pris en charge actuellement.


## <a name="supported-regions"></a>Régions prises en charge
L’agent Azure Monitor est disponible dans toutes les régions publiques qui prennent en charge Log Analytics. Actuellement, les régions et les clouds pour le secteur public ne sont pas pris en charge.
## <a name="supported-services-and-features"></a>Services et fonctionnalités pris en charge
Le tableau suivant indique la prise en charge actuelle de l’agent Azure Monitor avec d’autres services Azure.

| Service Azure | Prise en charge actuelle | Informations complémentaires |
|:---|:---|:---|
| [Centre de sécurité Azure](../../security-center/security-center-introduction.md) | Préversion privée | [Lien d’inscription](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | Préversion privée | [Lien d’inscription](https://aka.ms/AMAgent) |


Le tableau suivant indique la prise en charge actuelle de l’agent Azure Monitor avec les fonctionnalités Azure Monitor.

| Fonctionnalité Azure Monitor | Prise en charge actuelle | Informations complémentaires |
|:---|:---|:---|
| [Insights de machine virtuelle](../vm/vminsights-overview.md) | Préversion privée  | [Lien d’inscription](https://forms.office.com/r/jmyE821tTy) |
| [Intégrité de l’invité - Insights de machine virtuelle](../vm/vminsights-health-overview.md) | Préversion publique | Disponible seulement sur le nouvel agent |
| [Insights SQL](../insights/sql-insights-overview.md) | Préversion publique | Disponible seulement sur le nouvel agent |

Le tableau suivant indique la prise en charge actuelle de l’agent Azure Monitor avec les solutions Azure.

| Solution | Prise en charge actuelle | Informations complémentaires |
|:---|:---|:---|
| [Suivi des modifications](../../automation/change-tracking/overview.md) | Prise en charge en tant que Monitoring d’intégrité de fichier (FIM) dans Azure Security Center en version privée.  | [Lien d’inscription](https://aka.ms/AMAgent) |
| [Gestion des mises à jour](../../automation/update-management/overview.md) | Utilisez Update Management v2 (préversion privée) qui ne nécessite pas d’agent. | [Lien d’inscription](https://www.yammer.com/azureadvisors/threads/1064001355087872) |



## <a name="coexistence-with-other-agents"></a>Coexistence avec d’autres agents
L’agent Azure Monitor peut coexister avec les agents existants afin que vous puissiez continuer à utiliser leurs fonctionnalités existantes lors de l’évaluation ou de la migration. C’est particulièrement important en raison des limitations dans la prise en charge des solutions existantes. Vous devez être prudent lors de la collecte de données en double, car cela peut fausser les résultats de la requête et entraîner des frais supplémentaires pour l’ingestion et la rétention de données. 

Par exemple, VM Insights utilise l’agent Log Analytics pour envoyer des données de performances à un espace de travail Log Analytics. Vous avez peut-être également configuré l’espace de travail pour collecter les événements Windows et les événements Syslog à partir d’agents. Si vous installez l’agent Azure Monitor et que vous créez une règle de collecte de données pour ces mêmes événements et données de performances, des données dupliquées sont générées.

Par conséquent, vérifiez que vous ne collectez pas les mêmes données à partir des deux agents. Si c’est le cas, veillez à ce qu’elles aient des destinations différentes.


## <a name="costs"></a>Coûts
Il n’y a aucun coût lié à l’agent Azure Monitor, mais vous pouvez être facturé pour les données ingérées. Pour plus d’informations sur la collecte et la rétention de données Log Analytics et les mesures client, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="data-sources-and-destinations"></a>Sources et destinations de données
Le tableau suivant répertorie les types de données que vous pouvez actuellement collecter avec l’agent Azure Monitor à l’aide de règles de collecte de données et de l’emplacement où vous pouvez envoyer ces données. Consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md) pour obtenir la liste des analyses, des solutions et d’autres solutions qui utilisent l’agent Azure Monitor pour collecter d’autres types de données.


L’agent Azure Monitor envoie des données aux métriques Azure Monitor ou à un espace de travail Log Analytics qui prend en charge les journaux d’Azure Monitor.

| source de données | Destinations | Description |
|:---|:---|:---|
| Performances        | Métriques Azure Monitor<sup>1</sup><br>Espace de travail Log Analytics | Valeurs numériques mesurant les performances de différents aspects du système d’exploitation et des charges de travail. |
| Journaux d'événements Windows | Espace de travail Log Analytics | Informations envoyées au système de journalisation des événements Windows. |
| syslog             | Espace de travail Log Analytics | Informations envoyées au système de journalisation des événements Linux. |

<sup>1</sup> Il existe actuellement une limitation sur l’agent Azure Monitor pour Linux, où Métriques Azure Monitor comme *seule* destination n’est pas pris en charge. Vous pouvez l’utiliser avec Journaux Azure Monitor. Cette limitation sera levée dans la prochaine mise à jour de l’extension.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Pour obtenir la liste des versions de système d’exploitation Windows et Linux que l’agent Azure Monitor prend actuellement en charge, consultez [Systèmes d’exploitation pris en charge](agents-overview.md#supported-operating-systems).



## <a name="security"></a>Sécurité
L’agent Azure Monitor n’a besoin d’aucune clé, mais nécessite une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Vous devez disposer d’une identité managée affectée par le système activée sur chaque machine virtuelle avant de déployer l’agent.

## <a name="networking"></a>Mise en réseau
L’agent Azure Monitor prend en charge les étiquettes de service Azure (les étiquettes AzureMonitor et AzureResourceManager sont obligatoires), mais il ne fonctionne pas encore avec les étendues de liaison privée Azure Monitor. Si la machine se connecte via un serveur proxy pour communiquer sur Internet, passez en revue les conditions ci-dessous pour bien comprendre la configuration réseau requise.

### <a name="proxy-configuration"></a>Configuration du proxy

Les extensions de l’agent Azure Monitor pour Windows et Linux peuvent communiquer via un serveur proxy ou une passerelle Log Analytics pour Azure Monitor en utilisant le protocole HTTPS (pour les machines virtuelles Azure, les groupes de machines virtuelles identiques Azure et Azure Arc pour serveurs). Ceci est configuré en utilisant des paramètres des extensions, comme décrit ci-dessous, et prend en charge à la fois l’authentification anonyme et l’authentification de base (nom d’utilisateur/mot de passe).  

1. Utilisez cet organigramme simple pour déterminer d’abord les valeurs des paramètres *setting* et *protectedSetting* :

   ![Organigramme pour déterminer les valeurs des paramètres setting et protectedSetting lors de l’activation de l’extension](media/azure-monitor-agent-overview/proxy-flowchart.png)


2. Une fois que les valeurs des paramètres *setting* et *protectedSetting* sont déterminés, spécifiez ces paramètres supplémentaires lors du déploiement de l’agent Azure Monitor en utilisant des commandes PowerShell (exemples ci-dessous pour les machines virtuelles Azure) :

    | Paramètre | Valeur |
    |:---|:---|
    | SettingString | Objet JSON de l’organigramme ci-dessus, converti en chaîne ; à ignorer si non applicable. Exemple : {"proxy":{"mode":"application","address":"http://[adresse]:[port]","auth": false}} |
    | ProtectedSettingString | Objet JSON de l’organigramme ci-dessus, converti en chaîne ; à ignorer si non applicable. Exemple : {"proxy":{"username": "[nom_utilisateur]","password": "[mot_de_passe]"}} |


# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Installez l’agent Azure Monitor](azure-monitor-agent-install.md) sur des machines virtuelles Windows et Linux.
- [Créez une règle de collecte de données](data-collection-rule-azure-monitor-agent.md) pour collecter des données à partir de l’agent et les envoyer à Azure Monitor.
