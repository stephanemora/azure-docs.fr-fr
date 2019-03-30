---
title: 'CLI Azure Service Fabric : sfctl mesh service | Microsoft Docs'
description: Décrit les commandes sfctl mesh service de l’interface de ligne de commande (CLI) Service Fabric.
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
ms.openlocfilehash: e8b735780f4ed3402845d9d401f8e37701b9a1a6
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667580"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Obtenez les détails du service et répertoriez les services d’une ressource d’application.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| list | Répertorie toutes les ressources de service. |
| show | Récupère la ressource Service portant le nom spécifié. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Répertorie toutes les ressources de service.

Obtient les informations relatives à tous les services d’une ressource d’application. Les informations incluent la description et d’autres propriétés du Service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name --application-name [obligatoire] | Le nom de l’application. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Récupère la ressource Service portant le nom spécifié.

Récupère les informations relatives à la ressource Service portant le nom spécifié. Les informations incluent la description et d’autres propriétés du Service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name --application-name [obligatoire] | Le nom de l’application. |
| --name -n                     [obligatoire] | Nom du service. |

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