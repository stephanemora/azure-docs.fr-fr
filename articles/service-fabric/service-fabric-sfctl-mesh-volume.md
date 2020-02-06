---
title: Interface CLI Azure Service Fabric - sfctl mesh volume
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Inclut la liste des commandes permettant d’obtenir et de supprimer des ressources de volume.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0e5ba1a311bd0480c56ee40edf01fc13ec01664a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905914"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Obtenir et supprimer des ressources de volume.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime une ressource de volume. |
| list | Répertorie toutes les ressources de volume. |
| show | Récupère la ressource de volume portant le nom spécifié. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Supprime une ressource de volume.

Supprime la ressource de volume identifiée par le nom.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Nom du volume. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Répertorie toutes les ressources de volume.

Récupère les informations relatives à toutes les ressources de volume d'un groupe de ressources donné. Les informations incluent la description et d'autres propriétés du volume.

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Récupère la ressource de volume portant le nom spécifié.

Récupère les informations relatives à la ressource de volume portant le nom spécifié. Les informations incluent la description et d'autres propriétés du volume.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Nom du volume. |

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