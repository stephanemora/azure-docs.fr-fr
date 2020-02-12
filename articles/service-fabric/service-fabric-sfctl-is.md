---
title: 'Interface CLI Azure Service Fabric : sfctl is'
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient une liste de commandes pour la gestion de l’infrastructure.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906077"
---
# <a name="sfctl-is"></a>sfctl is
Interroge et envoie des commandes vers le service d’infrastructure.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| command | Appelle une commande d’administration dans l’instance de service d’infrastructure donnée. |
| query | Appelle une requête en lecture seule dans l’instance de service d’infrastructure donnée. |

## <a name="sfctl-is-command"></a>sfctl is command
Appelle une commande d’administration dans l’instance de service d’infrastructure donnée.

Pour les clusters pour lesquels une ou plusieurs instances du service d’infrastructure sont configurées, cette API permet d’envoyer des commandes propres à l’infrastructure vers une instance particulière du service d’infrastructure. Les commandes disponibles et leurs formats de réponse correspondants varient en fonction de l’infrastructure sur laquelle le cluster s’exécute. Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --command [Requis] | Texte de la commande à appeler. Le contenu de la commande dépend de l’infrastructure. |
| --service-id | Identité du service d’infrastructure. <br><br> Il s’agit du nom complet du service d’infrastructure sans le schéma d’URI « fabric »\:. Ce paramètre est obligatoire uniquement pour les clusters pour lesquels plusieurs instances du service d’infrastructure s’exécutent. |
| --timeout -t | Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-is-query"></a>sfctl is query
Appelle une requête en lecture seule dans l’instance de service d’infrastructure donnée.

Pour les clusters pour lesquels une ou plusieurs instances du service d’infrastructure sont configurées, cette API permet d’envoyer des requêtes propres à l’infrastructure vers une instance particulière du service d’infrastructure. Les commandes disponibles et leurs formats de réponse correspondants varient en fonction de l’infrastructure sur laquelle le cluster s’exécute. Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --command [Requis] | Texte de la commande à appeler. Le contenu de la commande dépend de l’infrastructure. |
| --service-id | Identité du service d’infrastructure. <br><br> Il s’agit du nom complet du service d’infrastructure sans le schéma d’URI « fabric »\:. Ce paramètre est obligatoire uniquement pour les clusters pour lesquels plusieurs instances du service d’infrastructure s’exécutent. |
| --timeout -t | Valeur par défaut \: 60. |

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
