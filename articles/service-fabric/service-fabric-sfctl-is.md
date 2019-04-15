---
title: 'CLI Azure Service Fabric : sfctl is | Microsoft Docs'
description: Décrit les commandes sfctl is de l’interface de ligne de commande (CLI) Service Fabric.
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
ms.openlocfilehash: 2039dd9222809d2c05aaeaf01f9d38c51f3b3797
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661588"
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
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-is-query"></a>sfctl is query
Appelle une requête en lecture seule dans l’instance de service d’infrastructure donnée.

Pour les clusters pour lesquels une ou plusieurs instances du service d’infrastructure sont configurées, cette API permet d’envoyer des requêtes propres à l’infrastructure vers une instance particulière du service d’infrastructure. Les commandes disponibles et leurs formats de réponse correspondants varient en fonction de l’infrastructure sur laquelle le cluster s’exécute. Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --command [Requis] | Texte de la commande à appeler. Le contenu de la commande dépend de l’infrastructure. |
| --service-id | Identité du service d’infrastructure. <br><br> Il s’agit du nom complet du service d’infrastructure sans le schéma d’URI « fabric »\:. Ce paramètre est obligatoire uniquement pour les clusters pour lesquels plusieurs instances du service d’infrastructure s’exécutent. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).