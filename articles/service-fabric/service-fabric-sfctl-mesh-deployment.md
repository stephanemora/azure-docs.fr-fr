---
title: 'CLI Azure Service Fabric : déploiement cloud sfctl | Microsoft Docs'
description: Décrit les commandes de déploiement cloud sfctl de l’interface de ligne de commande CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035176"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Créer des ressources Service Fabric Mesh.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| create | Crée un déploiement de ressources Service Fabric Mesh. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Crée un déploiement de ressources Service Fabric Mesh.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --input-yaml-files [obligatoire] | Chemins d’accès relatifs/absolus, séparés par des virgules, de tous les fichiers yaml ou chemin d’accès relatif/absolu du répertoire (récursif) qui contient les fichiers yaml. |
| --parameters | Chemin d’accès relatif/absolu du fichier yaml ou d’un objet json qui contient les paramètres devant être remplacés. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

### <a name="examples"></a>Exemples

Consolide et déploie toutes les ressources en cluster en remplaçant les paramètres mentionnés dans le fichier yaml

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Consolide et déploie toutes les ressources d’un répertoire en cluster en remplaçant les paramètres mentionnés dans le fichier yaml

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolide et déploie toutes les ressources d’un répertoire en cluster en remplaçant les paramètres mentionnés, qui sont transmis directement sous forme d’objet json

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).