---
title: Référence des commandes az postgres arc-server
titleSuffix: Azure Arc-enabled data services
description: Article de référence pour les commandes az postgres arc-server.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 12252e0bfa9d52da1b70de15b42a0b7014183202
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794573"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Modifier la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Supprimer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server show](#az-postgres-arc-server-show) | Afficher les détails d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server list](#az-postgres-arc-server-list) | Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc.
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Gérer les points de terminaison de groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
Pour définir le mot de passe du groupe de serveurs, définissez la variable d’environnement AZDATA_PASSWORD
```bash
az postgres arc-server create --name -n 
                              [--path]  
                              
[--k8s-namespace -k]  
                              
[--cores-limit]  
                              
[--cores-request]  
                              
[--memory-limit]  
                              
[--memory-request]  
                              
[--storage-class-data]  
                              
[--storage-class-logs]  
                              
[--storage-class-backups]  
                              
[--volume-claim-mounts]  
                              
[--extensions]  
                              
[--volume-size-data]  
                              
[--volume-size-logs]  
                              
[--volume-size-backups]  
                              
[--workers -w]  
                              
[--engine-version]  
                              
[--no-external-endpoint]  
                              
[--port]  
                              
[--no-wait]  
                              
[--engine-settings]  
                              
[--coordinator-settings]  
                              
[--worker-settings]  
                              
[--use-k8s]
```
### <a name="examples"></a>Exemples
Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc en utilisant les paramètres du moteur. Les deux exemples ci-dessous sont valides.
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
Créer un groupe de serveurs PostgreSQL avec des montages de revendications de volume.
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
Créer un groupe de serveurs PostgreSQL avec une limite de mémoire spécifique pour différents rôles de nœud.
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--path`
Chemin du fichier JSON source du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc. Cette étape est facultative.
#### `--k8s-namespace -k`
Espace de noms Kubernetes où le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est déployé. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
#### `--cores-limit`
Nombre maximal de cœurs de processeur pour le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc qui peuvent être utilisés par nœud. Les cœurs fractionnels sont pris en charge. Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--cores-request`
Nombre minimum de cœurs du processeur qui doivent être disponibles par nœud pour planifier le service. Les cœurs fractionnels sont pris en charge. Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--memory-limit`
Limite de mémoire du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc, sous la forme d’un nombre suivi de Ko (kilo-octets), de Mo (mégaoctets) ou de Go (gigaoctets). Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--memory-request`
Demande de mémoire du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc, sous la forme d’un nombre suivi de Ko (kilo-octets), de Mo (mégaoctets) ou de Go (gigaoctets). Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--storage-class-data`
Classe de stockage à utiliser pour les volumes persistants de données.
#### `--storage-class-logs`
Classe de stockage à utiliser pour les volumes persistants de journaux.
#### `--storage-class-backups`
Classe de stockage à utiliser pour les volumes persistants de sauvegarde.
#### `--volume-claim-mounts`
Liste de montages de revendications de volume séparés par des virgules. Un montage de revendication de volume est une paire constituée d’une revendication de volume persistant existante (dans le même espace de noms) et du type de volume (et des métadonnées facultatives selon le type de volume), séparés par le signe deux-points. Le volume persistant est monté dans chaque pod pour le groupe de serveurs PostgreSQL. Le chemin de montage peut dépendre du type de volume.
#### `--extensions`
Liste séparée par des virgules des extensions Postgres à charger au démarrage. Reportez-vous à la documentation Postgres pour les valeurs prises en charge.
#### `--volume-size-data`
Taille du volume de stockage à utiliser pour les données sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--volume-size-logs`
Taille du volume de stockage à utiliser pour les journaux sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--volume-size-backups`
Taille du volume de stockage à utiliser pour les sauvegardes sous la forme d’un nombre positif suivi de Ki (kilo-octets), Mi (mégaoctets) ou Gi (gigaoctets).
#### `--workers -w`
Nombre de nœuds worker à provisionner dans un groupe de serveurs. En préversion, la réduction du nombre de nœuds worker n’est pas prise en charge. Pour plus de détails, reportez-vous à la documentation.
#### `--engine-version`
Doit être 11 ou 12. La valeur par défaut est 12.
`12`
#### `--no-external-endpoint`
Si elle est spécifiée, aucun service externe n’est créé. Dans le cas contraire, un service externe est créé en utilisant le même type de service que le contrôleur de données.
#### `--port`
Optionnel.
#### `--no-wait`
Si elle est spécifiée, la commande n’attend pas que l’instance soit à l’état prêt avant un retour.
#### `--engine-settings`
Liste séparée par des virgules des paramètres du moteur Postgres au format « key1=val1, key2=val2 ».
#### `--coordinator-settings`
Liste séparée par des virgules des paramètres du moteur postgres au format « key1=val1, key2=val2 » à appliquer au rôle de nœud « coordinateur ». Lorsque des paramètres spécifiques au rôle de nœud sont spécifiés, les paramètres par défaut sont ignorés et remplacés par les paramètres fournis ici.
#### `--worker-settings`
Liste séparée par des virgules des paramètres du moteur postgres au format « key1=val1, key2=val2 » à appliquer au rôle de nœud « worker ». Lorsque des paramètres spécifiques au rôle de nœud sont spécifiés, les paramètres par défaut sont ignorés et remplacés par les paramètres fournis ici.
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Modifier la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server edit --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--workers -w]  
                            
[--cores-limit]  
                            
[--cores-request]  
                            
[--memory-limit]  
                            
[--memory-request]  
                            
[--extensions]  
                            
[--port]  
                            
[--no-wait]  
                            
[--engine-settings]  
                            
[--replace-settings]  
                            
[--coordinator-settings]  
                            
[--worker-settings]  
                            
[--admin-password]  
                            
[--use-k8s]
```
### <a name="examples"></a>Exemples
Modifier la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
Modifier un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc en utilisant les paramètres du moteur pour le nœud coordinateur.
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Modifie un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc et remplace les paramètres du moteur existants par le nouveau paramètre « key1=val1 ».
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc en cours de modification. Le nom sous lequel votre instance est déployée ne peut pas être modifié.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms Kubernetes où le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est déployé. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
#### `--path`
Chemin du fichier JSON source du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc. Cette étape est facultative.
#### `--workers -w`
Nombre de nœuds worker à provisionner dans un groupe de serveurs. En préversion, la réduction du nombre de nœuds worker n’est pas prise en charge. Pour plus de détails, reportez-vous à la documentation.
#### `--cores-limit`
Nombre maximal de cœurs de processeur pour le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc qui peuvent être utilisés par nœud. Les cœurs fractionnels sont pris en charge. Pour supprimer la cores_limit, spécifiez sa valeur en tant que chaîne vide. Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--cores-request`
Nombre minimum de cœurs du processeur qui doivent être disponibles par nœud pour planifier le service ; les cœurs fractionnels sont pris en charge. Pour supprimer la cores_request, spécifiez sa valeur en tant que chaîne vide. Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--memory-limit`
Limite de mémoire pour le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc, sous la forme d’un nombre suivi de Ko (kilo-octets), de Mo (mégaoctets) ou de Go (gigaoctets). Pour supprimer la memory_limit, spécifiez sa valeur en tant que chaîne vide. Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--memory-request`
Demande de mémoire pour le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc, sous la forme d’un nombre suivi de Ko (kilo-octets), de Mo (mégaoctets) ou de Go (gigaoctets). Pour supprimer la memory_request, spécifiez sa valeur en tant que chaîne vide. Éventuellement, une liste de rôles séparés par des virgules avec des valeurs peut être spécifiée au format \<role\>=\<value\>. Les rôles valides sont : « coordinateur » ou « c », « worker » ou « w ». Si aucun rôle n’est spécifié, les paramètres s’appliquent à tous les nœuds du groupe de serveurs PostgreSQL Hyperscale.
#### `--extensions`
Liste séparée par des virgules des extensions Postgres à charger au démarrage. Reportez-vous à la documentation Postgres pour les valeurs prises en charge.
#### `--port`
Optionnel.
#### `--no-wait`
Si elle est spécifiée, la commande n’attend pas que l’instance soit à l’état prêt avant un retour.
#### `--engine-settings`
Liste séparée par des virgules des paramètres du moteur Postgres au format « key1=val1, key2=val2 ». Les paramètres fournis sont fusionnés avec les paramètres existants. Pour supprimer un paramètre, indiquez une valeur vide telle que « removedKey= ». Si vous modifiez un paramètre du moteur qui requiert un redémarrage, le service est redémarré pour appliquer les paramètres immédiatement.
#### `--replace-settings`
Lorsqu’elle est spécifiée avec --engine-settings, elle remplace tous les paramètres du moteur personnalisés existants par un nouvel ensemble de paramètres et de valeurs.
#### `--coordinator-settings`
Liste séparée par des virgules des paramètres du moteur postgres au format « key1=val1, key2=val2 » à appliquer au rôle de nœud « coordinateur ». Lorsque des paramètres spécifiques au rôle de nœud sont spécifiés, les paramètres par défaut sont ignorés et remplacés par les paramètres fournis ici.
#### `--worker-settings`
Liste séparée par des virgules des paramètres du moteur postgres au format « key1=val1, key2=val2 » à appliquer au rôle de nœud « worker ». Lorsque des paramètres spécifiques au rôle de nœud sont spécifiés, les paramètres par défaut sont ignorés et remplacés par les paramètres fournis ici.
#### `--admin-password`
S’il est spécifié, le mot de passe d’administrateur du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est défini sur la valeur de la variable d’environnement AZDATA_PASSWORD, si elle est présente, et sur une valeur demandée dans le cas contraire.
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Supprimer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server delete --name -n 
                              [--k8s-namespace -k]  
                              
[--force -f]  
                              
[--use-k8s]
```
### <a name="examples"></a>Exemples
Supprimer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms Kubernetes où le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est déployé. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
#### `--force -f`
Forcer la suppression du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc sans confirmation.
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Afficher les détails d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server show --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--use-k8s]
```
### <a name="examples"></a>Exemples
Afficher les détails d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms Kubernetes où le groupe de serveurs PostgreSQL Hyperscale avec Azure Arc est déployé. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
#### `--path`
Chemin où la spécification complète du groupe de serveurs PostgreSQL Hyperscale avec Azure Arc doit être écrite. S’il est omis, la spécification est écrite dans la sortie standard.
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server list [--k8s-namespace -k] 
                            [--use-k8s]
```
### <a name="examples"></a>Exemples
Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc.
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--k8s-namespace -k`
Espace de noms Kubernetes où les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc sont déployés. Si aucun espace de noms n’est spécifié, l’espace de noms défini dans kubeconfig sera utilisé.
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
