---
title: Référence az sql mi-arc
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az sql mi-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 81c8b2dbca45ac25db35fe15c2510b967bab1ae8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562534"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | Afficher et gérer les points de terminaison SQL.
[az sql mi-arc create](#az-sql-mi-arc-create) | Créez une instance SQL gérée.
[az sql mi-arc edit](#az-sql-mi-arc-edit) | Modifiez la configuration d’une instance SQL gérée.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | Supprimez une instance SQL gérée.
[az sql mi-arc show](#az-sql-mi-arc-show) | Affichez les détails d’une instance SQL gérée.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | Récupère le certificat du point de terminaison de mise en miroir de groupe de disponibilité à partir de sql mi et le stocke dans un fichier.
[az sql mi-arc list](#az-sql-mi-arc-list) | Répertoriez les instances SQL gérées.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | Commandes de configuration.
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | Créer ou supprimer un groupe de disponibilité distribué.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
Pour définir le mot de passe de l’instance SQL gérée, définissez la variable d’environnement AZDATA_PASSWORD
```bash
az sql mi-arc create --name -n 
                     --k8s-namespace -k  
                     
[--path]  
                     
[--replicas]  
                     
[--cores-limit -c]  
                     
[--cores-request]  
                     
[--memory-limit -m]  
                     
[--memory-request]  
                     
[--storage-class-data -d]  
                     
[--storage-class-logs -g]  
                     
[--storage-class-datalogs]  
                     
[--storage-class-backups]  
                     
[--volume-size-data]  
                     
[--volume-size-logs]  
                     
[--volume-size-datalogs]  
                     
[--volume-size-backups]  
                     
[--no-wait]  
                     
[--no-external-endpoint]  
                     
[--cert-public-key-file]  
                     
[--cert-private-key-file]  
                     
[--service-cert-secret]  
                     
[--admin-login-secret]  
                     
[--license-type -l]  
                     
[--tier -t]  
                     
[--dev]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-labels]  
                     
[--service-annotations]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]  
                     
[--collation]  
                     
[--language]  
                     
[--agent-enabled]  
                     
[--trace-flags]
```
### <a name="examples"></a>Exemples
Créez une instance SQL gérée.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace
```
Créez une instance managée SQL avec 3 réplicas dans un scénario de haute disponibilité.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3 --k8s-namespace namespace
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom de l’instance SQL gérée.
#### `--k8s-namespace -k`
Espace de noms où l’instance SQL gérée doit être déployée. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--path`
Chemin d’accès au fichier azext_arcdata pour le fichier json de l’instance SQL gérée.
#### `--replicas`
Cette option spécifie le nombre de réplicas SQL Managed Instance qui seront déployés dans votre cluster Kubernetes à des fins de haute disponibilité. Les valeurs autorisées sont « 3 » ou « 1 », avec « 1 » comme valeur par défaut.
#### `--cores-limit -c`
Limite de cœurs de l’instance gérée sous la forme d’un entier.
#### `--cores-request`
Demande de cœurs de l’instance gérée sous la forme d’un entier.
#### `--memory-limit -m`
Limite de la capacité de l’instance gérée sous forme d’un nombre entier suivi de Gi (gigaoctets). Exemple : 4 Gi
#### `--memory-request`
Demande de capacité de l’instance gérée sous forme d’un nombre entier suivi de Gi (gigaoctets). Exemple : 4 Gi
#### `--storage-class-data -d`
Classe de stockage à utiliser pour les données (.mdf, .ndf). Si aucune valeur n’est indiquée, aucune classe de stockage n’est indiquée. Par conséquent, Kubernetes utilisera la classe de stockage par défaut.
#### `--storage-class-logs -g`
Classe de stockage à utiliser pour les journaux (/var/log). Si aucune valeur n’est indiquée, aucune classe de stockage n’est indiquée. Par conséquent, Kubernetes utilisera la classe de stockage par défaut.
#### `--storage-class-datalogs`
Classe de stockage à utiliser pour les journaux de base de données (.ldf). Si aucune valeur n’est indiquée, aucune classe de stockage n’est indiquée. Par conséquent, Kubernetes utilisera la classe de stockage par défaut.
#### `--storage-class-backups`
Classe de stockage à utiliser pour les sauvegardes (/var/opt/mssql/backups). Si aucune valeur n’est indiquée, aucune classe de stockage n’est indiquée. Par conséquent, Kubernetes utilisera la classe de stockage par défaut.
#### `--volume-size-data`
Taille du volume de stockage à utiliser pour les données sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--volume-size-logs`
Taille du volume de stockage à utiliser pour les journaux sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--volume-size-datalogs`
Taille du volume de stockage à utiliser pour les journaux de données sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--volume-size-backups`
Taille du volume de stockage à utiliser pour les sauvegardes sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--no-wait`
Si elle est spécifiée, la commande n’attend pas que l’instance soit à l’état prêt avant un retour.
#### `--no-external-endpoint`
Si elle est spécifiée, aucun service externe n’est créé. Dans le cas contraire, un service externe est créé en utilisant le même type de service que le contrôleur de données.
#### `--cert-public-key-file`
Chemin d’accès au fichier contenant une clé publique de certificat au format PEM à utiliser pour SQL Server.
#### `--cert-private-key-file`
Chemin d’accès au fichier contenant une clé privée de certificat au format PEM à utiliser pour SQL Server.
#### `--service-cert-secret`
Nom du secret Kubernetes à générer qui héberge ou hébergera le certificat de service SQL.
#### `--admin-login-secret`
Nom du secret Kubernetes à générer qui héberge ou hébergera les informations d’identification du compte administrateur.
#### `--license-type -l`
Type de licence à appliquer pour cette instance gérée. Les valeurs autorisées sont : BasePrice, LicenseIncluded. La valeur par défaut est LicenseIncluded. Le type de licence ne peut pas être modifié.
#### `--tier -t`
Le niveau tarifaire pour cette instance. Valeurs autorisées : BusinessCritical (BC en abrégé) ou GeneralPurpose (GP en abrégé). La valeur par défaut est GeneralPurpose. Le niveau de prix ne peut pas être modifié.
#### `--dev`
Si elle est spécifiée, elle est considérée comme une instance dev et n’est pas facturée.
#### `--labels`
Liste d’étiquettes séparées par des virgules de l’instance SQL gérée.
#### `--annotations`
Liste d’étiquettes séparées par des virgules des annotations de l’instance SQL gérée.
#### `--service-labels`
Liste d’étiquettes séparées par des virgules à appliquer à tous les services externes.
#### `--service-annotations`
Liste d’annotations séparées par des virgules à appliquer à tous les services externes.
#### `--storage-labels`
Liste d’étiquettes séparées par des virgules à appliquer à tous les PVC.
#### `--storage-annotations`
Liste d’annotations séparées par des virgules à appliquer à tous les PVC.
#### `--use-k8s`
Créez une instance SQL gérée à l’aide des API Kubernetes locales.
#### `--collation`
Classement SQL Server pour l’instance.
#### `--language`
Paramètres régionaux de SQL Server par tout identificateur de langue (LCID) pris en charge pour l’instance.
#### `--agent-enabled`
Activez l’agent SQL Server pour l’instance. Par défaut, elle est désactivée. Les valeurs autorisées sont : True ou False.
#### `--trace-flags`
Liste d’indicateurs de trace séparée par des virgules. Aucun indicateur par défaut.
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
## <a name="az-sql-mi-arc-edit"></a>az sql mi-arc edit
Modifiez la configuration d’une instance SQL gérée.
```bash
az sql mi-arc edit --name -n 
                   [--k8s-namespace -k]  
                   
[--path]  
                   
[--cores-limit -c]  
                   
[--cores-request]  
                   
[--memory-limit -m]  
                   
[--memory-request]  
                   
[--no-wait]  
                   
[--dev]  
                   
[--labels]  
                   
[--annotations]  
                   
[--service-labels]  
                   
[--service-annotations]  
                   
[--agent-enabled]  
                   
[--trace-flags]  
                   
[--use-k8s]
```
### <a name="examples"></a>Exemples
Modifiez la configuration d’une instance SQL gérée.
```bash
az sql mi-arc edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom de l’instance SQL gérée en cours de modification. Le nom sous lequel votre instance est déployée ne peut pas être modifié.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms où réside l’instance SQL gérée. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
#### `--path`
Chemin d’accès au fichier azext_arcdata pour le fichier json de l’instance SQL gérée.
#### `--cores-limit -c`
Limite de cœurs de l’instance gérée sous la forme d’un entier.
#### `--cores-request`
Demande de cœurs de l’instance gérée sous la forme d’un entier.
#### `--memory-limit -m`
Limite de la capacité de l’instance gérée sous forme d’un nombre entier suivi de Gi (gigaoctets). Exemple : 4 Gi
#### `--memory-request`
Demande de capacité de l’instance gérée sous forme d’un nombre entier suivi de Gi (gigaoctets). Exemple : 4 Gi
#### `--no-wait`
Si elle est spécifiée, la commande n’attend pas que l’instance soit à l’état prêt avant un retour.
#### `--dev`
Si elle est spécifiée, elle est considérée comme une instance dev et n’est pas facturée.
#### `--labels`
Liste d’étiquettes séparées par des virgules de l’instance SQL gérée.
#### `--annotations`
Liste d’étiquettes séparées par des virgules des annotations de l’instance SQL gérée.
#### `--service-labels`
Liste d’étiquettes séparées par des virgules à appliquer à tous les services externes.
#### `--service-annotations`
Liste d’annotations séparées par des virgules à appliquer à tous les services externes.
#### `--agent-enabled`
Activez l’agent SQL Server pour l’instance. Par défaut, elle est désactivée.
#### `--trace-flags`
Liste d’indicateurs de trace séparée par des virgules. Aucun indicateur par défaut.
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
Supprimez une instance SQL gérée.
```bash
az sql mi-arc delete --name -n 
                     [--k8s-namespace -k]  
                     
[--use-k8s]
```
### <a name="examples"></a>Exemples
Supprimez une instance SQL gérée.
```bash
az sql mi-arc delete -n sqlmi1
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom de l’instance SQL gérée à supprimer.
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
Affichez les détails d’une instance SQL gérée.
```bash
az sql mi-arc show --name -n 
                   [--path -p]  
                   
[--k8s-namespace -k]  
                   
[--use-k8s]
```
### <a name="examples"></a>Exemples
Affichez les détails d’une instance SQL gérée.
```bash
az sql mi-arc show -n sqlmi1
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom de l’instance SQL gérée à afficher.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--path -p`
Chemin d’accès où l’ensemble de la spécification de l’instance SQL gérée doit être écrit. S’il est omis, la spécification est écrite dans la sortie standard.
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
Récupère le certificat du point de terminaison de mise en miroir de groupe de disponibilité à partir de sql mi et le stocke dans un fichier.
```bash
az sql mi-arc get-mirroring-cert --name -n 
                                 --cert-file  
                                 
[--k8s-namespace -k]  
                                 
[--use-k8s]
```
### <a name="examples"></a>Exemples
Récupère le certificat du point de terminaison de mise en miroir de groupe de disponibilité à partir de sqlmi1 et le stocke dans le fichier fileName1
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom de l’instance SQL gérée.
#### `--cert-file`
Nom de fichier local pour stocker le certificat récupéré au format PEM.
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
Répertoriez les instances SQL gérées.
```bash
az sql mi-arc list [--k8s-namespace -k] 
                   [--use-k8s]
```
### <a name="examples"></a>Exemples
Répertoriez les instances SQL gérées.
```bash
az sql mi-arc list
```
### <a name="optional-parameters"></a>Paramètres facultatifs
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
