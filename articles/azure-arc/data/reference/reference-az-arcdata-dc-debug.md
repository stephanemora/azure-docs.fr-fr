---
title: Informations de référence sur az arcdata dc debug
titleSuffix: Azure Arc-enabled data services
description: Article d’informations de référence sur les commandes az arcdata dc degug.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 219471cf793394c024061f39319a98cc89477c76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531800"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | Copie les journaux.
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | Déclenchez le vidage de la mémoire.
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
Copiez les journaux de débogage à partir du contrôleur de données. La configuration Kubernetes est requise sur votre système.
```bash
az arcdata dc debug copy-logs --k8s-namespace -k 
                              [--container -c]  
                              
[--target-folder -d]  
                              
[--pod]  
                              
[--resource-kind]  
                              
[--resource-name]  
                              
[--timeout -t]  
                              
[--skip-compress]  
                              
[--exclude-dumps]  
                              
[--exclude-system-logs ]  
                              
[--use-k8s]
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--k8s-namespace -k`
Espace de noms Kubernetes du contrôleur de données.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--container -c`
Copier les journaux des conteneurs avec un nom similaire. Facultatif : par défaut, copie les journaux de tous les conteneurs. Ne peut pas être spécifié plusieurs fois. S’il est spécifié plusieurs fois, c’est le dernier qui sera utilisé.
#### `--target-folder -d`
Chemin du dossier cible dans lequel copier les journaux. Facultatif : par défaut, crée le résultat dans le dossier local.  Ne peut pas être spécifié plusieurs fois. S’il est spécifié plusieurs fois, c’est le dernier qui sera utilisé.
#### `--pod`
Copier les journaux des pods avec un nom similaire. Facultatif : par défaut, copie les journaux de tous les pods. Ne peut pas être spécifié plusieurs fois. S’il est spécifié plusieurs fois, c’est le dernier qui sera utilisé.
#### `--resource-kind`
Copiez les journaux de la ressource d’un type particulier. Ne peut pas être spécifié plusieurs fois. S’il est spécifié plusieurs fois, c’est le dernier qui sera utilisé. S’il est spécifié, --resource-name doit également être spécifié pour identifier la ressource.
#### `--resource-name`
Copiez les journaux de la ressource du nom spécifié. Ne peut pas être spécifié plusieurs fois. S’il est spécifié plusieurs fois, c’est le dernier qui sera utilisé. S’il est spécifié, --resource-kind doit également être spécifié pour identifier la ressource.
#### `--timeout -t`
Nombre de secondes à attendre pour l’exécution de la commande. La valeur par défaut est 0, ce qui signifie qu’elle est illimitée.
#### `--skip-compress`
Indique s’il convient ou non d’ignorer la compression du dossier de résultats. La valeur par défaut est False, ce qui compresse le dossier de résultats.
#### `--exclude-dumps`
Indique s’il convient ou non d’exclure les vidages du dossier de résultats. La valeur par défaut est False, ce qui comprend les vidages.
#### `--exclude-system-logs `
Indique s’il faut, ou non, exclure les journaux système de la collection. La valeur par défaut est False, ce qui comprend les journaux système.
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
Déclenchez le vidage de la mémoire et copiez le contenu hors du conteneur. La configuration Kubernetes est requise sur votre système.
```bash
az arcdata dc debug dump --k8s-namespace -k 
                         [--container -c]  
                         
[--target-folder -d]  
                         
[--use-k8s]
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--k8s-namespace -k`
Espace de noms Kubernetes du contrôleur de données.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--container -c`
Conteneur cible à déclencher pour vider les processus en cours d’exécution.
`controller`
#### `--target-folder -d`
Dossier cible où copier le contenu du vidage. `./output/dump`
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
