---
title: 'CLI Azure Service Fabric : Télémétrie sfctl settings | Microsoft Docs'
description: Décrit les commandes de télémétrie sfctl settings de l’interface de ligne de commande (CLI) Service Fabric.
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
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556336"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Configurez les paramètres locaux de télémétrie pour cette instance de sfctl.

La télémétrie Sfctl collecte le nom de la commande sans les paramètres fournis ni les valeurs associées, la version sfctl, le type de système d’exploitation, la version de Python, la réussite ou l’échec de la commande, le message d’erreur retourné.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| set-telemetry | Activez ou désactivez la télémétrie. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
Activez ou désactivez la télémétrie.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --off | Désactivez la télémétrie. |
| --on | Activez la télémétrie. Il s’agit de la valeur par défaut. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

### <a name="examples"></a>Exemples

Désactivez la télémétrie.

```
sfctl settings telemetry set_telemetry --off
```

Activez la télémétrie.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).