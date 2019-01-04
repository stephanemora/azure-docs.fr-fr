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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 455b2a70568566bff5b1ea4c185568a1758f7db3
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274902"
---
# <a name="sfctl-container"></a>sfctl container
Exécutez des commandes relatives à un conteneur sur un nœud de cluster.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| invoke-api | Appelle l’API de conteneur sur un conteneur déployé sur un nœud Service Fabric pour le package de code donné. |
| logs | Permet d’obtenir les journaux du conteneur déployé sur un nœud Service Fabric pour le package de code donné. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Appelle l’API de conteneur sur un conteneur déployé sur un nœud Service Fabric pour le package de code donné.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id           [Requis] | Identité de l’application. <br><br> Il s’agit généralement du nom complet de l’application, sans le schéma d’URI « fabric\: ». À compter de la version 6.0, les noms hiérarchiques sont délimités par le caractère « \~ ». Par exemple, si l’application est nommée « fabric\:/myapp/app1 », son identité est « myapp\~app1 » dans les versions 6.0 et ultérieures, et « myapp/app1 » dans les versions précédentes. |
| --code-package-instance-id [Requis] | ID qui identifie de façon unique une instance de package de code déployée sur un nœud Service Fabric. <br><br> Peut être récupéré par « service code-package-list ». |
| --code-package-name        [Requis] | Nom de package de code spécifié dans un manifeste de service inscrit dans le cadre d’un type d’application dans un cluster Service Fabric. |
| --container-api-uri-path   [Requis] | Chemin d’URI de l’API REST du conteneur. Utilisez « {id} » à la place du nom/ID de conteneur. |
| --node-name [Requis] | Nom du nœud. |
| --service-manifest-name [Requis] | Nom d’un manifeste de service inscrit dans le cadre d’un type d’application dans un cluster Service Fabric. |
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
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-container-logs"></a>journaux de conteneur sfctl
Permet d’obtenir les journaux du conteneur déployé sur un nœud Service Fabric pour le package de code donné.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id           [Requis] | Identité de l’application. <br><br> Il s’agit généralement du nom complet de l’application, sans le schéma d’URI « fabric\: ». À compter de la version 6.0, les noms hiérarchiques sont délimités par le caractère « \~ ». Par exemple, si l’application est nommée « fabric\:/myapp/app1 », son identité est « myapp\~app1 » dans les versions 6.0 et ultérieures, et « myapp/app1 » dans les versions précédentes. |
| --code-package-instance-id [Requis] | ID d’instance de package de code, qui peut être récupéré avec 'service code-package-list'. |
| --code-package-name        [Requis] | Nom de package de code spécifié dans un manifeste de service inscrit dans le cadre d’un type d’application dans un cluster Service Fabric. |
| --node-name [Requis] | Nom du nœud. |
| --service-manifest-name [Requis] | Nom d’un manifeste de service inscrit dans le cadre d’un type d’application dans un cluster Service Fabric. |
| --tail | Nombre de lignes à afficher à partir de la fin des journaux. La valeur par défaut est 100. « all » pour afficher les journaux complets. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

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