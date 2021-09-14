---
title: Référence az arcdata resource-kind
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata resource-kind.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 597bc660140ec2f0ac856a3f6c67db600480aa63
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525089"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | Listez les types de ressources personnalisés disponibles pour Arc qui peuvent être définis et créés.
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | Obtenez le fichier de modèle de type de ressource Arc.
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
Listez les types de ressources personnalisés disponibles pour Arc qui peuvent être définis et créés. Après la création de la liste, vous pouvez passer à l’obtention du fichier de modèle nécessaire pour définir ou créer cette ressource personnalisée.
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>Exemples
Exemple de commande permettant de répertorier les types de ressources personnalisés disponibles pour Arc.
```bash
az arcdata resource-kind list
```
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Obtenez le fichier de modèle de type de ressource Arc.
```bash
az arcdata resource-kind get --kind -k 
                             [--dest -d]
```
### <a name="examples"></a>Exemples
Exemple de commande pour obtenir un fichier de modèle CRD de type de ressource Arc.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--kind -k`
Type de ressource arc pour lequel vous souhaitez obtenir le fichier de modèle.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--dest -d`
Répertoire dans lequel vous souhaitez placer les fichiers de modèle.
`template`
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
