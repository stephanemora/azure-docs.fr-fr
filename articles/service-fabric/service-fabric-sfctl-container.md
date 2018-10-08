---
title: CLI Azure Service Fabric - conteneur sfctl | Microsoft Docs
description: Décrit les commandes du conteneur sfctl de l’interface CLI Azure Service Fabric.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495126"
---
# <a name="sfctl-container"></a>sfctl container
Exécutez des commandes relatives à un conteneur sur un nœud de cluster.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| invoke-api | Permet d’appeler l’API REST de conteneur. |
| logs | Permet de récupérer les journaux de conteneur. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Permet d’appeler l’API REST de conteneur.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id           [Requis] | Identité d’application. |
| --code-package-instance-id [Requis] | ID d’instance de package de code, qui peut être récupéré avec 'service code-package-list'. |
| --code-package-name        [Requis] | Nom de package de code. |
| --container-api-uri-path   [Requis] | Chemin d’URI de l’API REST du conteneur. Utilisez « {id} » à la place du nom/ID de conteneur. |
| --node-name [Requis] | Nom du nœud. |
| --service-manifest-name [Requis] | Nom de manifeste de service. |
| --container-api-body | Corps de la demande HTTP pour l’API REST de conteneur. |
| --container-api-content-type | Type de contenu pour l’API REST de conteneur, par défaut 'application/json'. |
| --container-api-http-verb | Verbe HTTP pour l’API REST de conteneur, par défaut GET. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez le site à l’adresse http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-container-logs"></a>journaux de conteneur sfctl
Permet de récupérer les journaux de conteneur.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id           [Requis] | Identité d’application. |
| --code-package-instance-id [Requis] | ID d’instance de package de code, qui peut être récupéré avec 'service code-package-list'. |
| --code-package-name        [Requis] | Nom de package de code. |
| --node-name [Requis] | Nom du nœud. |
| --service-manifest-name [Requis] | Nom de manifeste de service. |
| --tail | Retourner uniquement ce nombre de lignes du journal à partir de la fin des journaux. Spécifier sous forme d’entier ou all (tout) pour extraire toutes les lignes du journal. Valeur par défaut 'all'. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez le site à l’adresse http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).