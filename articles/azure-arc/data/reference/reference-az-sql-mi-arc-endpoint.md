---
title: az sql mi-arc endpoint reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql mi-arc endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ca3685c6769e45d43b513ea11355795af332a849
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532128"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | Liste les points de terminaison SQL.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
Liste les points de terminaison SQL.
```bash
az sql mi-arc endpoint list [--name -n] 
                            [--k8s-namespace -k]  
                            
[--use-k8s]
```
### <a name="examples"></a>Exemples
Listez les points de terminaison d’une instance gérée SQL.
```bash
az sql mi-arc endpoint list -n sqlmi1
```
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--name -n`
Nom de l’instance SQL à afficher. En cas d’omission, tous les points de terminaison de toutes les instances s’affichent.
#### `--k8s-namespace -k`
Espace de noms où résident les instances SQL gérées. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
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
