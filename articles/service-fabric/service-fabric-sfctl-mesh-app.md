---
title: 'CLI Azure Service Fabric : sfctl mesh app'
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient une liste de commandes pour la gestion de ressources de l'application Service Fabric Mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7c571136e6bef64c2024c81db85ef736f64a4e01
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906059"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Obtenir et supprimer les ressources d’application.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime les ressources d’application. |
| list | Répertorie toutes les ressources d’application. |
| show | Récupère la ressource d’application portant le nom spécifié. |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
Supprime les ressources d’application.

Supprime la ressource d’application identifiée par le nom.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Le nom de l’application. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
Répertorie toutes les ressources d’application.

Récupère les informations relatives à toutes les ressources d’application d’un groupe de ressources donné. Les informations incluent la description et d’autres propriétés de l’application.

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
Récupère la ressource d’application portant le nom spécifié.

Récupère les informations relatives à la ressource d’application portant le nom spécifié. Les informations incluent la description et d’autres propriétés de l’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Le nom de l’application. |

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