---
title: Informations de référence sur les commandes az arcdata dc
titleSuffix: Azure Arc-enabled data services
description: Article de référence sur les commandes az arcdata dc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f5b804ca5bf0c38165d848f75de7a3cd428fb2f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524839"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>Commandes
| Commande | Description|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | Créez un contrôleur de données.
[az arcdata dc delete](#az-arcdata-dc-delete) | Supprimez un contrôleur de données.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | Commandes relatives aux points de terminaison.
[az arcdata dc status](reference-az-arcdata-dc-status.md) | Commandes relatives à l’état.
[az arcdata dc config](reference-az-arcdata-dc-config.md) | Commandes de configuration.
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | Déboguez un contrôleur de données.
[az arcdata dc export](#az-arcdata-dc-export) | Exportez les métriques, les journaux ou l’utilisation.
[az arcdata dc upload](#az-arcdata-dc-upload) | Téléchargez le fichier de données exporté.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
Créez un contrôleur de données. La configuration Kubernetes est requise sur votre système avec les variables d’environnement suivantes ['AZDATA_USERNAME', 'AZDATA_PASSWORD'].
```bash
az arcdata dc create --k8s-namespace -k 
                     --name -n  
                     
--connectivity-mode  
                     
--resource-group -g  
                     
--location -l  
                     
[--profile-name]  
                     
[--path -p]  
                     
[--storage-class]  
                     
[--infrastructure]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-annotations]  
                     
[--service-labels]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]
```
### <a name="examples"></a>Exemples
Déploiement d’un contrôleur de données.
```bash
az arcdata dc create --name name --k8s-namespace namespace  --connectivity-mode indirect --resource-group group  --location location, --subscription subscription
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--k8s-namespace -k`
Espace de noms Kubernetes dans lequel déployer le contrôleur de données. S’il existe déjà, il sera utilisé. S’il n’existe pas, le système tentera d’abord de le créer.
#### `--name -n`
Nom du contrôleur de données.
#### `--connectivity-mode`
Connectivité à Azure (indirecte ou directe) dans laquelle le contrôleur de données doit fonctionner.
#### `--resource-group -g`
Groupe de ressources Azure dans lequel la ressource de contrôleur de données doit être ajoutée.
#### `--location -l`
Emplacement Azure dans lequel les métadonnées du contrôleur de données seront stockées (par exemple, Eastus).
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--profile-name`
Nom d’un profil de configuration existant. Exécutez `az arcdata dc config list` pour voir les options disponibles. L’une des options suivantes : « azure-arc-gke », « azure-arc-eks », « azure-arc-kubeadm », « azure-arc-aks-default-storage », « azure-arc-azure-openshift », « azure-arc-ake », « azure-arc-openshift », « azure-arc-aks-hci », « azure-arc-aks-premium-storage ».
#### `--path -p`
Chemin d’accès à un répertoire contenant un profil de configuration personnalisé à utiliser. Exécutez `az arcdata dc config init` pour créer un profil de configuration personnalisé.
#### `--storage-class`
Classe de stockage à utiliser pour les volumes persistants de l’ensemble des données et journaux pour tous les pods du contrôleur de données qui en ont besoin.
#### `--infrastructure`
L’infrastructure sur laquelle le contrôleur de données s’exécutera. Valeurs autorisées : « aws », « gcp », « azure », « alibaba », « onpremises », « other », « auto ».
#### `--labels`
Liste d’étiquettes séparées par des virgules à appliquer à toutes les ressources du contrôleur de données.
#### `--annotations`
Liste d’annotations séparées par des virgules à appliquer à toutes les ressources du contrôleur de données.
#### `--service-annotations`
Liste d’annotations séparées par des virgules à appliquer à tous les services externes du contrôleur de données.
#### `--service-labels`
Liste d’étiquettes séparées par des virgules à appliquer à tous les services externes du contrôleur de données.
#### `--storage-labels`
Liste d’étiquettes séparées par des virgules à appliquer à toutes les PVC créées par le contrôleur de données.
#### `--storage-annotations`
Liste d’annotations séparées par des virgules à appliquer à toutes les PVC créées par le contrôleur de données.
#### `--use-k8s`
Créez un contrôleur de données à l’aide d’API Kubernetes locales.
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
Supprimez le contrôleur de données. La configuration de Kube est requise sur votre système.
```bash
az arcdata dc delete --name -n 
                     --k8s-namespace -k  
                     
[--force -f]  
                     
[--yes -y]
```
### <a name="examples"></a>Exemples
Déploiement d’un contrôleur de données.
```bash
az arcdata dc delete --name name --k8s-namespace namespace
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--name -n`
Nom du contrôleur de données.
#### `--k8s-namespace -k`
Espace de noms Kubernetes dans lequel le contrôleur de données existe.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--force -f`
Forcez la suppression du contrôleur de données et de tous ses services de données.
#### `--yes -y`
Supprimez le contrôleur de données sans invite de confirmation.
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
Exportez les métriques, les journaux ou l’utilisation dans un fichier.
```bash
az arcdata dc export --type -t 
                     --path -p  
                     
--k8s-namespace -k  
                     
[--force -f]  
                     
[--use-k8s]
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--type -t`
Type de données à exporter. Options : journaux, métriques et utilisation.
#### `--path -p`
Chemin d’accès complet ou relatif comprenant le nom du fichier à exporter.
#### `--k8s-namespace -k`
Espace de noms Kubernetes dans lequel le contrôleur de données existe.
### <a name="optional-parameters"></a>Paramètres facultatifs
#### `--force -f`
Forcez la création du fichier de sortie. Remplace tout fichier existant au même chemin d’accès.
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
Téléchargez le fichier de données exporté à partir d’un contrôleur de données vers Azure.
```bash
az arcdata dc upload --path -p 
                     
```
### <a name="required-parameters"></a>Paramètres obligatoires
#### `--path -p`
Chemin d’accès complet ou relatif comprenant le nom du fichier à télécharger.
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
