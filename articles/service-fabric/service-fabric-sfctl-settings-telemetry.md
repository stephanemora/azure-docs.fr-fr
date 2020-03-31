---
title: Interface CLI Azure Service Fabric - sfctl settings telemetry
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient la liste des commandes liées à la configuration de la télémétrie sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903017"
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
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

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