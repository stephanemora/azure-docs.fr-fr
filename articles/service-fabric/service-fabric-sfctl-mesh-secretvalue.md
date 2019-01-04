---
title: 'CLI Azure Service Fabric : sfctl mesh secretvalue | Microsoft Docs'
description: Décrit les commandes sfctl mesh secretvalue de l'interface de ligne de commande CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 064aeaea47dd59a1dd75cf19ea4060d8f9c2c4bf
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53559057"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Récupère et supprime les ressources « mesh secretvalue ».

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime la valeur spécifiée de la ressource secrète nommée. |
| list | Répertorie les noms de toutes les valeurs de la ressource secrète spécifiée. |
| show | Récupère la valeur d'une version spécifiée d'une ressource secrète. |

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
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

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
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Récupère la valeur d'une version spécifiée d'une ressource secrète.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --secret-name -n [obligatoire] | Nom de la ressource secrète. |
| --version -v     [obligatoire] | Nom de la version secrète. |
| --show-value | Affiche la valeur réelle de la version secrète. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).