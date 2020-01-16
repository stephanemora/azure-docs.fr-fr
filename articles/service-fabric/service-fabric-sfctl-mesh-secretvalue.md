---
title: 'Interface CLI Azure Service Fabric : sfctl mesh secretvalue'
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient une liste de commandes pour l’obtention et la suppression de ressources mesh secretvalue Service Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: a29d32dff9ad51942acb30dd834ad6fbd362ac65
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646107"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Récupère et supprime les ressources mesh secretvalue.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime la valeur spécifiée de la ressource secrète nommée. |
| list | Répertorie les noms de toutes les valeurs de la ressource secrète spécifiée. |
| show | Répertorie la valeur spécifiée de la ressource secrète. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
Supprime la valeur spécifiée de la ressource secrète nommée.

Supprime la ressource de valeur secrète identifiée par le nom. Le nom de la ressource est généralement la version associée à cette valeur. La suppression échouera si la valeur spécifiée est en cours d'utilisation.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --secret-name -n [obligatoire] | Nom de la ressource secrète. |
| --version -v     [obligatoire] | Nom de la version secrète. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
Répertorie les noms de toutes les valeurs de la ressource secrète spécifiée.

Récupère des informations sur toutes les ressources de valeur secrète de la ressource secrète spécifiée. Les informations incluent les noms des ressources de valeur secrète, mais pas les valeurs réelles.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --secret-name -n [obligatoire] | Nom de la ressource secrète. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Répertorie la valeur spécifiée de la ressource secrète.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --secret-name -n [obligatoire] | Nom de la ressource secrète. |
| --version -v     [obligatoire] | Nom de la version secrète. |
| --show-value | Affiche la valeur réelle de la version secrète. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).