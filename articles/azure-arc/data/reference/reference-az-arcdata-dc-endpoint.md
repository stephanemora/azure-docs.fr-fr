---
title: az arcdata dc endpoint reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c024050777d01bb93fbbcba63a636d4e34c163b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562195"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | Répertoriez le point de terminaison du contrôleur de données.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
Répertoriez le point de terminaison du contrôleur de données.
```bash
az arcdata dc endpoint list --k8s-namespace -k 
                            [--endpoint-name -e]  
                            
[--use-k8s]
```
### <a name="examples"></a>Exemples
Liste tous les points de terminaison du contrôleur de données disponibles.
```bash
az arcdata dc endpoint list --k8s-namespace namespace
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--k8s-namespace -k`
Espace de noms Kubernetes dans lequel le contrôleur de données existe.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--endpoint-name -e`
Nom du point de terminaison du contrôleur de données Arc.
#### `--use-k8s`
Utilisez les API Kubernetes locales pour effectuer cette action.
### <a name="global-arguments"></a>Arguments globaux
#### `--debug`
Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage.
#### `--help -h`
Affichez ce message d’aide et quittez.
#### `--output -o`
Format de sortie.  Valeurs autorisées : json, jsonc, none, table, tsv, yaml, yamlc.  Valeur par défaut : json.
#### `--query -q`
Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nom ou ID de l’abonnement. Vous pouvez configurer l’abonnement par défaut en utilisant `az account set -s NAME_OR_ID`.
#### `--verbose`
Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets.
