---
title: CLI Azure Service Fabric - sfctl mesh code-package-log | Microsoft Docs
description: Décrit les commandes sfctl mesh code-package-log de l’interface de ligne de commande CLI Service Fabric.
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
ms.openlocfilehash: 81ddcc8c5685a839afabc1e82ecf4246cb813c21
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283335"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obtenez les journaux concernant le conteneur du package de code spécifié pour le réplica de service donné.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| get | Obtient les journaux à partir du conteneur. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtient les journaux à partir du conteneur.

Obtenez les journaux concernant le conteneur du package de code spécifié du réplica de service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name --application-name [obligatoire] | Le nom de l’application. |
| --code-package-name           [obligatoire] | Nom du package de code du service. |
| --replica-name                [obligatoire] | Nom du réplica Service Fabric. |
| --service-name                [obligatoire] | Nom du service. |
| --tail | Nombre de lignes à afficher à partir de la fin des journaux. La valeur par défaut est 100. « all » pour afficher les journaux complets. |

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