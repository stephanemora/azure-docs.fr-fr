---
title: Migrer vers la mise à jour de l’API Azure Batch AI | Microsoft Docs
description: Guide pratique pour mettre à jour le code Azure Batch AI et les scripts afin d’utiliser l’espace de travail et tester les ressources
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: b59173259aa86a429b9f926a8e5ffbfd046451a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294875"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrer vers la mise à jour de l’API Azure Batch AI

Dans la prochaine version de l’API REST Batch AI 2018-05-01 ainsi que les kits de développement logiciel (SDK) et outils Batch AI connexes, des modifications importantes et de nouvelles fonctionnalités ont été intégrées.

Si vous avez utilisé une version précédente de l’API Batch AI, cet article explique comment modifier votre code et les scripts pour la nouvelle API. Apportez ces modifications uniquement une fois que la nouvelle API est disponible.

## <a name="whats-changing"></a>Ce qui a changé

En réponse aux commentaires des clients, nous supprimons les limites du nombre de travaux et facilitons la gestion des ressources Batch AI. La nouvelle API introduit deux nouvelles ressources, *espace de travail* et *expérience*. Collectez les travaux de formation connexes d’une expérience et organisez toutes les ressources Batch AI connexes (clusters, serveurs de fichiers, expériences, travaux) sous un espace de travail.  

* **Espace de travail** : collection de niveau supérieur de tous les types de ressources Batch AI. Les clusters et serveurs de fichiers sont contenus dans un espace de travail. Les espaces de travail distinguent généralement le travail appartenant à différents groupes ou projets. Par exemple, vous pouvez avoir un espace de travail de développement et un espace de travail de test. Vous n’avez probablement besoin que d’un nombre limité d’espaces de travail par abonnement. 

* **Expérience** : collection de travaux connexes qui peuvent être interrogés et gérés ensemble. Par exemple, utilisez une expérience pour regrouper tous les travaux effectués dans le cadre d’un balayage de réglage hyperparamétrique. 

L’image suivante montre un exemple de hiérarchie de ressources. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Surveiller et gérer des ressources existantes
Dans chaque groupe de ressources dans lequel vous avez déjà créé des clusters Batch AI, des travaux ou des serveurs de fichiers, le service Batch AI créera un espace de travail nommé `migrated-<region>` (par exemple, `migrated-eastus`) et une expérience nommée `migrated`. Pour accéder aux travaux, clusters ou serveurs de fichiers créés précédemment, vous devez utiliser l’espace de travail et l’expérience migrés. 

### <a name="portal"></a>Portail 
Pour accéder aux travaux, clusters ou serveurs de fichiers créés précédemment à l’aide du portail, sélectionnez d’abord l’espace de travail `migrated-<region>`. Après avoir sélectionné l’espace de travail, effectuez des opérations comme redimensionner, supprimer un cluster et afficher le statut d’un travail et les sorties. 

### <a name="sdks"></a>Kits de développement logiciel (SDK) 
Pour accéder aux travaux, clusters ou serveurs de fichiers précédemment créés via les kits de développement logiciel (SDK) Batch AI, indiquez les paramètres de nom de l’espace de travail et de l’expérience. 

Si vous utilisez le Kit de développement logiciel (SDK) Python, les modifications appropriées sont affichées dans les exemples suivants. Ces modifications sont similaires dans les autres kits de développement logiciel (SDK) Batch AI. 


#### <a name="get-old-cluster"></a>Obtenir un ancien cluster 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Supprimer un ancien cluster 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Obtenir un ancien serveur de fichiers 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Supprimer un ancien serveur de fichiers  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Obtenir un ancien travail 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Supprimer un ancien travail

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Lorsque vous utilisez l’interface de ligne de commande Azure CLI pour des travaux, des clusters ou des serveurs de fichiers créés précédemment, utilisez les paramètres `-w` et `-e` pour fournir les noms de l’espace de travail et de l’expérience. 


#### <a name="get-old-cluster"></a>Obtenir un ancien cluster

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Supprimer un ancien cluster 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Obtenir un ancien serveur de fichiers

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Supprimer un ancien serveur de fichiers 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Obtenir un ancien travail

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Supprimer un ancien travail 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Créer les ressources Batch AI 
 
Si vous utilisez un des kits de développement logiciel (SDK) Batch AI existants, vous pouvez continuer à l’utiliser pour créer des ressources Batch AI (travaux, clusters ou serveurs de fichiers) sans modifier le code. Toutefois, après avoir effectué une mise à niveau avec le nouveau SDK, vous devez apporter les modifications suivantes.
 
### <a name="create-workspace"></a>Créer un espace de travail 
Selon votre scénario, vous pouvez créer manuellement un espace de travail à usage unique via le portail ou l’interface CLI. Si vous utilisez l’interface CLI, créez un espace de travail à l’aide de la commande suivante : 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Créer une expérience 


Selon votre scénario, vous pouvez créer manuellement une expérience à usage unique via le portail ou l’interface CLI. Si vous utilisez l’interface CLI, créez une expérience à l’aide de la commande suivante : 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Créer des clusters, des serveurs de fichiers et des travaux
 
Si vous utilisez le portail pour créer des travaux, des clusters ou des serveurs de fichiers, le portail vous guide au cours de cette opération de création pour fournir les paramètres de nom de l’espace de travail et de l’expérience.

Pour créer des travaux, des clusters ou des serveurs de fichiers via le Kit de développement logiciel (SDK) mis à jour, indiquez le paramètre de nom de l’espace de travail. Si vous utilisez le Kit de développement logiciel (SDK) Python, les modifications appropriées sont affichées dans les exemples suivants. Remplacez *workspace_name* et *experiment_name* par l’espace de travail et l’expérience que vous avez créés précédemment. Ces modifications sont similaires dans les autres kits de développement logiciel (SDK) Batch AI. 


#### <a name="create-cluster"></a>Créer un cluster 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Créer un serveur de fichiers 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Créer un travail 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Étapes suivantes

* Consultez la référence de l’API Batch AI : [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai) et [REST](/rest/api/batchai)