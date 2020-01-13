---
title: CLI Azure Service Fabric - Déploiement mesh sfctl
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient une liste de commandes pour la création de ressources Service Fabric Mesh.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 6512cce075906fc8708a39fa0747ec27be33e961
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645359"
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
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

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