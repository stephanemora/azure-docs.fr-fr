---
title: Règles de collecte de données dans Azure Monitor (version préliminaire)
description: Vue d’ensemble des règles de collecte de données (DCR) dans Azure Monitor, notamment leur contenu et leur structure, et comment vous pouvez les créer et les utiliser.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: af5324373359cea643a3e31b6bb94e614ddb7e36
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082789"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Règles de collecte de données dans Azure Monitor (version préliminaire)
Les règles de collecte de données (DCR) définissent les données entrantes dans Azure Monitor et spécifient l’emplacement où ces données doivent être envoyées ou stockées. Cet article fournit une vue d’ensemble des règles de collecte de données, notamment leur contenu et leur structure, et comment vous pouvez les créer et les utiliser.

## <a name="input-sources"></a>Sources d’entrée
Les règles de collecte de données prennent actuellement en charge les sources d’entrée suivantes :

- Machine virtuelle Azure avec l’agent Azure Monitor. Consultez [Configurer la collecte de données pour l’agent Azure Monitor (version préliminaire)](data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Composants d’une règle de collecte de données
Une règle de collecte de données inclut les composants suivants.

| Composant | Description |
|:---|:---|
| Sources de données | Source unique de données de surveillance avec son propre format et sa propre méthode exposant ses données. Le journal des événements Windows, les compteurs de performances et Syslog sont des exemples d’une source de données. Chaque source de données correspond à un type de source de données particulier, comme décrit ci-dessous. |
| Flux | Descripteur unique qui décrit un ensemble de sources de données qui seront transformées et schématisées en un seul type. Chaque source de données nécessite un ou plusieurs flux, et un flux peut être utilisé par plusieurs sources de données. Toutes les sources de données d’un flux partagent un schéma commun. Utilisez plusieurs flux de données, par exemple, lorsque vous souhaitez envoyer une source de données particulière à plusieurs tables dans le même espace de travail Log Analytics. |
| Destinations | Ensemble de destinations où les données doivent être envoyées. L’espace de travail Log Analytics, les métriques Azure Monitor et Azure Event Hubs sont des exemples. | 
| Flux de données | Définition des flux à envoyer à des destinations. | 

Le diagramme suivant montre les composants d’une règle de collecte de données et leurs relations

[![Diagramme de DCR](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Types de sources de données
Chaque source de données a un type de source de données. Chaque type définit un ensemble unique de propriétés qui doivent être spécifiées pour chaque source de données. Les types de sources de données actuellement disponibles sont présentés dans le tableau suivant.

| Type de source de données | Description | 
|:---|:---|
| extension | Sources de données basée sur une extension de machine virtuelle |
| performanceCounters | Compteurs de performances pour Windows et Linux |
| syslog | Événements Syslog sur une machine virtuelle Linux |
| windowsEventLogs | Journaux d'événements Windows |


## <a name="limits"></a>Limites
Le tableau suivant répertorie les limites qui s’appliquent actuellement à chaque règle de collecte de données.

| Limite | Valeur |
|:---|:---|
| Nombre maximal de sources de données | 10 |
| Nombre maximal de spécificateurs de compteur dans les performances | 100 |
| Nombre de caractères maximal des noms des installations dans SysLog | 20 |
| Nombre maximal de requêtes XPath dans EventLog | 100 |
| Taille maximale des flux de données | 10 |
| Taille maximale des streams de données | 10 |
| Nombre maximal d’extensions | 10 |
| Taille maximale des paramètres d’extension | 32 Kb |
| Nombre maximal d’espaces de travail Log Analytics | 10 |


## <a name="create-a-dcr"></a>Créer une DCR
Il existe actuellement deux méthodes pour créer une DCR :

- [Utilisez le Portail Azure](data-collection-rule-azure-monitor-agent.md) pour créer une règle de collecte de données et l’associer à une ou plusieurs machines virtuelles.
- Modifiez directement la règle de collecte de données au format JSON et envoyez-la à l’aide de l’API REST.

## <a name="sample-data-collection-rule"></a>Exemple de règle de collecte de données
L’exemple de règle de collecte de données ci-dessous concerne les machines virtuelles avec l’agent Azure Management et présente les détails suivants :

- Données de performances
  - Collecte des compteurs de processeur, de mémoire, de disque logique et de disque physique spécifiques toutes les 15 secondes et les charge toutes les minutes.
  - Collecte des compteurs de processus spécifiques toutes les 30 secondes et les charge toutes les 5 minutes.
- Événements Windows
  - Collecte des événements de sécurité Windows et les charge toutes les minutes.
  - Collecte des événements d’application et de système Windows et les charge toutes les 5 minutes.
- syslog
  - Collecte des événements de débogage, critiques et d’urgence de l’installation cron.
  - Collecte des événements d’alerte, critiques et d’urgence de l’installation Syslog.
- Destinations
  - Envoie toutes les données à un espace de travail Log Analytics nommé centralTeamWorkspace.
  - Envoie des données de performances aux Métriques Azure Monitor de l’abonnement actuel.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "sylogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Étapes suivantes

- [Créez une règle de collecte de données](data-collection-rule-azure-monitor-agent.md) et une association à celle-ci à partir d’une machine virtuelle à l’aide de l’agent Azure Monitor.