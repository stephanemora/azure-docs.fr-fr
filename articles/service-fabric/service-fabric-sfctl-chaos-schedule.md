---
title: Interface de ligne de commande CLI Azure Service Fabric - sfctl chaos schedule| Microsoft Docs
description: Décrit les commandes sfctl chaos schedule de l’interface de ligne de commande CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668311"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Permet d’obtenir et de définir la planification de chaos.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| get | Obtenez la planification de chaos définissant quand et comment exécuter Chaos. |
| set | Définissez la planification utilisée par Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Obtenez la planification de chaos définissant quand et comment exécuter Chaos.

Obtient la version de la planification de chaos en cours d’utilisation et la planification de chaos qui définit quand et comment exécuter Chaos.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Définissez la planification utilisée par Chaos.

Chaos va planifier automatiquement les exécutions en fonction de la planification de chaos. La version utilisée dans la planification d’entrée fournie doit correspondre à la version de la planification de chaos sur le serveur. Si la version fournie ne correspond pas à la version sur le serveur, la planification de chaos n’est pas mise à jour. Si la version fournie correspond à la version sur le serveur, alors la planification de chaos est mise à jour et la version de la planification de chaos sur le serveur est incrémentée de un et revient à 0 après 2 147 483 647. Si Chaos est en cours d’exécution lorsque cet appel est effectué, l’appel va échouer.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --chaos-parameters-dictionary | Liste encodée JSON représentant un mappage des noms de chaîne pour ChaosParameters à utiliser par Jobs. |
| --expiry-date-utc | La date et l’heure d’arrêt à l’aide de la planification pour planifier Chaos.  Valeur par défaut\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Liste encodée JSON de ChaosScheduleJobs représentant quand exécuter Chaos et avec quels paramètres. |
| --start-date-utc | La date et l’heure de démarrage à l’aide de la planification pour planifier Chaos.  Valeur par défaut\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |
| --version | Le numéro de version de la planification. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

### <a name="examples"></a>Exemples

La commande suivante définit une planification (en supposant que la planification actuelle a la version 0) qui démarre le 01-01-2016 et expire le 01-01-2038, et qui exécute Chaos 24 heures/24 et 7 jours/7. Chaos va être planifié sur le cluster pour ces dates.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
