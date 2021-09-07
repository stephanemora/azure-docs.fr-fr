---
title: Référence des commandes az arcdata dc status
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc status.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9e795c71a5316960bfc7f0b1138afce3ecc4b957
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524254"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | Affichez l’état du contrôleur de données.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
Affichez l’état du contrôleur de données.
```bash
az arcdata dc status show [--k8s-namespace -k] 
                          [--use-k8s]
```
### <a name="examples"></a>Exemples
Affichez l’état du contrôleur de données dans un espace de noms kubernetes particulier.
```bash
az arcdata dc status show --k8s-namespace <ns>
```
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms Kubernetes dans lequel le contrôleur de données existe.
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
