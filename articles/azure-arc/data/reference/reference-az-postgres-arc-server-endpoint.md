---
title: Informations de référence sur az postgres arc-server endpoint
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az postgres arc-server endpoint.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8b3bf65f4e38177712f6deb333ba5d4994542b2f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532609"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az postgres arc-server endpoint list](#az-postgres-arc-server-endpoint-list) | Lister les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint list
Lister les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server endpoint list [--name -n] 
                                     [--k8s-namespace -k]  
                                     
[--use-k8s]
```
### <a name="examples"></a>Exemples
Lister les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--name -n`
Nom du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
#### `--k8s-namespace -k`
Espace de noms Kubernetes où le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est déployé. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
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
