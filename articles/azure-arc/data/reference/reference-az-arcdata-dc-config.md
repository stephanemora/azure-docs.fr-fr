---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc config.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a7936f1bbe56b105ec65dfa76cb7346fa41953ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524125"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | Initialisez un profil de configuration du contrôleur de données qui peut être utilisé avec `az arcdata dc create`.
[az arcdata dc config list](#az-arcdata-dc-config-list) | Répertoriez les choix de profils de configuration disponibles.
[az arcdata dc config add](#az-arcdata-dc-config-add) | Ajoutez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | Supprimez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | Remplacez une valeur pour un chemin d’accès json dans un fichier de configuration.
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | Corrigez un fichier de configuration basé sur un fichier patch json.
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
Initialisez un profil de configuration du contrôleur de données qui peut être utilisé avec `az arcdata dc create`. La source spécifique du profil de configuration peut être spécifiée dans les arguments.
```bash
az arcdata dc config init [--path -p] 
                          [--source -s]  
                          
[--force -f]
```
### <a name="examples"></a>Exemples
Expérience d’initialisation de configuration du contrôleur de données guidée : vous serez invité à entrer les valeurs nécessaires.
```bash
az arcdata dc config init
```
La commande arcdata dc config init avec des arguments crée un profil de configuration aks-dev-test dans ./custom.
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--path -p`
Chemin de fichier de l’emplacement où vous souhaitez placer le profil de configuration. Il est défini par défaut sur <cwd>/custom.
#### `--source -s`
Source du profil de configuration : ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
#### `--force -f`
Forcez le remplacement du fichier cible.
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
Répertoriez les choix de profils de configuration disponibles à utiliser dans `arcdata dc config init`
```bash
az arcdata dc config list [--config-profile -c] 
                          
```
### <a name="examples"></a>Exemples
Affiche les noms de profils de configuration disponibles.
```bash
az arcdata dc config list
```
Affiche json d’un profil de configuration spécifique.
```bash
az arcdata dc config list --config-profile aks-dev-test
```
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--config-profile -c`
Profil de configuration par défaut : ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
Ajoutez la valeur au niveau du chemin d’accès json dans le fichier config. Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az arcdata dc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>Exemples
Ajoutez un stockage du contrôleur de données.
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--path -p`
Chemin d’accès au fichier config du contrôleur de données de la configuration que vous souhaitez définir, par exemple custom/control.json
#### `--json-values -j`
Liste de paires clé/valeur de chemins d’accès json aux valeurs : key1.subkey1=value1,key2.subkey2=value2. Vous pouvez fournir des valeurs json incluses, telles que : key='{"kind":"cluster","name":"test-cluster"}' ou fournir un chemin d'accès au fichier, tel que key=./values.json. La commande add ne prend PAS en charge les conditionnels.  Si la valeur incluse que vous fournissez est une paire clé-valeur elle-même avec des caractères « = » et « , », mettez ces caractères dans une séquence d’échappement.  Par exemple, key1="key2\=val2\,key3\=val3". Consultez http://jsonpatch.com/ pour obtenir des exemples d’apparence de votre chemin d’accès.  Si vous souhaitez accéder à un tableau, vous devez le faire en indiquant l’index, tel que key.0=value
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
Supprimez la valeur au niveau du chemin d’accès json dans le fichier config.  Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az arcdata dc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>Exemples
Exemple 1 : supprimez un stockage du contrôleur de données.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--path -p`
Chemin d’accès au fichier config du contrôleur de données de la configuration que vous souhaitez définir, par exemple custom/control.json
#### `--json-path -j`
Une liste de chemins d’accès json basés sur la bibliothèque jsonpatch qui indique les valeurs que vous souhaitez supprimer, par exemple : key1.subkey1,key2.subkey2. La commande remove ne prend PAS en charge les conditionnels. Consultez http://jsonpatch.com/ pour obtenir des exemples d’apparence de votre chemin d’accès.  Si vous souhaitez accéder à un tableau, vous devez le faire en indiquant l’index, tel que key.0=value
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
Remplacez la valeur au niveau du chemin d’accès json dans le fichier config.  Tous les exemples ci-dessous sont fournis dans Bash.  Si vous utilisez une autre ligne de commande, vous devrez peut-être placer les guillemets dans une séquence d’échappement de manière appropriée.  Vous pouvez également utiliser la fonctionnalité de fichier de correctif.
```bash
az arcdata dc config replace --path -p 
                             --json-values -j
```
### <a name="examples"></a>Exemples
Exemple 1 : remplacez le port d’un point de terminaison unique (point de terminaison du contrôleur de données).
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Exemple 2 : remplacez un stockage du contrôleur de données.
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--path -p`
Chemin d’accès au fichier config du contrôleur de données de la configuration que vous souhaitez définir, par exemple custom/control.json
#### `--json-values -j`
Liste de paires clé/valeur de chemins d’accès json aux valeurs : key1.subkey1=value1,key2.subkey2=value2. Vous pouvez fournir des valeurs json incluses, telles que : key='{"kind":"cluster","name":"test-cluster"}' ou fournir un chemin d'accès au fichier, tel que key=./values.json. La commande replace prend en charge les conditionnels dans la bibliothèque jsonpath.  Pour ce faire, démarrez votre chemin d’accès par un $. Cela vous permettra d’effectuer une condition telle que -j $.key1.key2[?(@.key3=="someValue"].key4=value. Si la valeur incluse que vous fournissez est une paire clé-valeur elle-même avec des caractères « = » et « , », mettez ces caractères dans une séquence d’échappement.  Par exemple, key1="key2\=val2\,key3\=val3". Consultez les exemples ci-dessous. Pour obtenir de l’aide supplémentaire, consultez : https://jsonpath.com/
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
Corrigez le fichier config en fonction du fichier patch donné. Consultez http://jsonpatch.com/ pour mieux comprendre comment les chemins d’accès doivent être composés. L’opération de remplacement peut utiliser des conditions dans son chemin d’accès en raison de la bibliothèque jsonpath https://jsonpath.com/. Tous les fichiers json des correctifs doivent commencer par une clé « correctif » qui contient un tableau de correctifs avec les opérations (ajouter, remplacer, supprimer), le chemin d’accès et la valeur correspondants. L’opération « supprimer » ne requiert pas de valeur, juste un chemin d’accès. Considérons les exemples ci-dessous.
```bash
az arcdata dc config patch --path 
                           --patch-file -p
```
### <a name="examples"></a>Exemples
Exemple 1 : remplacez le port d’un point de terminaison unique (point de terminaison du contrôleur de données) par un fichier correctif.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Exemple 2 : remplacez un stockage du contrôleur de données par un fichier correctif.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--path`
Chemin d’accès au fichier config du contrôleur de données de la configuration que vous souhaitez définir, par exemple custom/control.json
#### `--patch-file -p`
Chemin d'accès à un fichier json du correctif basé sur la bibliothèque jsonpatch : http://jsonpatch.com/. Vous devez démarrer votre fichier json du correctif par une clé appelée « correctif », dont la valeur est un tableau d’opérations PATCH que vous envisagez de créer. Pour le chemin d’accès d’une opération PATCH, vous pouvez utiliser la notation, telle que key1.key2 pour la plupart des opérations. Si vous souhaitez effectuer une opération de remplacement et que vous remplacez une valeur dans un tableau qui requiert une condition, utilisez la notation jsonpath en commençant votre chemin d’accès par un $. Cela vous permettra d’effectuer une condition telle que $.key1.key2[?(@.key3=="someValue"].key4. Considérons les exemples ci-dessous. Pour obtenir de l’aide supplémentaire sur les conditionnels, consultez : https://jsonpath.com/.
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
