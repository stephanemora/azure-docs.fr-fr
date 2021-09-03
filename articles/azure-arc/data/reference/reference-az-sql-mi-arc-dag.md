---
title: Référence az sql mi-arc dag
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql mi-arc dag.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4ebd6e71c14c9a319e99e68eca15f19a21d52033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563242"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | Créer une ressource personnalisée de groupe de disponibilité distribué
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | Supprimer une ressource personnalisée de groupe de disponibilité distribuée sur une instance sqlmi.
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | Afficher une ressource personnalisée de groupe de disponibilité distribué.
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
Créer une ressource personnalisée de groupe de disponibilité distribuée pour créer un groupe de disponibilité distribué
```bash
az sql mi-arc dag create --name -n 
                         --dag-name -d  
                         
--local-instance-name -l  
                         
--local-primary -p  
                         
--remote-instance-name -r  
                         
--remote-mirroring-url -u  
                         
--remote-mirroring-cert-file -f  
                         
[--k8s-namespace -k]  
                         
[--path]  
                         
[--use-k8s]
```
### <a name="examples"></a>Exemples
Exemple 1 : créer une ressource personnalisée de groupe de disponibilité distribué dagCr1 pour créer un groupe de disponibilité distribué dagName1 entre l’instance sqlmi locale sqlmi1 et l’instance sqlmi distante sqlmi2. Elle requiert le miroir principal sqlmi distant remotePrimary : 5022 et le fichier de certificat du point de terminaison de miroir distant sqlmi./sqlmi2.cer.
```bash
az sql mi-arc dag create --name=dagCr1 --dag-name=dagName1  --local-instance-name=sqlmi1 --local-primary=true --remote-instance-name=sqlmi2  --remote-mirroring-url==remotePrimary:5022 --remote-mirroing-cert-file="./sqlmi2.cer"
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom de la ressource du groupe de disponibilité distribué.
#### `--dag-name -d`
Nom du groupe de disponibilité distribué pour cette SQL instance gérée. Les paramètres locaux et distants doivent utiliser le même nom.
#### `--local-instance-name -l`
Nom de l’instance SQL locale gérée
#### `--local-primary -p`
True indique que l’instance locale gérée SQL est géo-primaire. False indique que l’instance locale gérée SQL est géo-secondaire
#### `--remote-instance-name -r`
Nom du groupe de disponibilité distante SQL ou de l’instance gérée distante SQL
#### `--remote-mirroring-url -u`
URL de point de terminaison de mise en miroir du groupe de disponibilité distante SQL ou de l’instance gérée distante SQL
#### `--remote-mirroring-cert-file -f`
Nom de fichier du certificat public du point de terminaison de mise en miroir pour le groupe de disponibilité distante SQL ou l’instance gérée distante SQL. Seul le format PEM est pris en charge
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms où réside l’instance SQL gérée. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
#### `--path`
Chemin d’accès à la spécification de ressource personnalisée, ici custom/spec.json
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
Supprimer une ressource personnalisée de groupe de disponibilité distribuée sur une instance sqlmi pour supprimer un groupe de disponibilité distribué. Il requiert un nom de ressource personnalisé
```bash
az sql mi-arc dag delete --name 
                         [--k8s-namespace -k]  
                         
[--use-k8s]
```
### <a name="examples"></a>Exemples
Ex 1 : supprimer les ressources du groupe de disponibilité distribué nommé dagCr1.
```bash
az sql mi-arc dag delete --name=dagCr1
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name`
Nom de la ressource du groupe de disponibilité distribué.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms où réside l’instance SQL gérée. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
Afficher une ressource personnalisée de groupe de disponibilité distribué. Il requiert un nom de ressource personnalisé
```bash
az sql mi-arc dag show --name 
                       [--k8s-namespace -k]  
                       
[--use-k8s]
```
### <a name="examples"></a>Exemples
Ex 1 : afficher les ressources du groupe de disponibilité distribué nommé dagCr1.
```bash
az sql mi-arc dag show --name=dagCr1
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name`
Nom de la ressource du groupe de disponibilité distribué.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms où réside l’instance SQL gérée. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
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
