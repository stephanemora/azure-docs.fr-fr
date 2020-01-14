---
title: 'CLI Azure Service Fabric : sfctl mesh gateway'
description: Apprenez-en plus sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient une liste de commandes pour l’obtention et la suppression de ressources de passerelle Service Fabric Mesh.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 86e63f88cf1412b54fad6f9e386a1a5052463917
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646158"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Récupère et supprime des ressources de la passerelle maillée.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime la ressource de passerelle. |
| list | Répertorie toutes les ressources de passerelle. |
| show | Récupère la ressource de passerelle portant le nom spécifié. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
Supprime la ressource de passerelle.

Supprime la ressource de passerelle identifiée par le nom.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Le nom de la ressource de passerelle. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
Répertorie toutes les ressources de passerelle.

Récupère les informations relatives à toutes les ressources de passerelle d’un groupe de ressources donné. Les informations incluent la description et d’autres propriétés de la passerelle.

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
Récupère la ressource de passerelle portant le nom spécifié.

Récupère les informations relatives à la ressource de passerelle portant le nom spécifié. Les informations incluent la description et d’autres propriétés de la passerelle.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Le nom de la ressource de passerelle. |

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