---
title: Exporter ou supprimer les données d’un espace de travail
titleSuffix: Azure Machine Learning service
description: Découvrez comment exporter ou supprimer votre espace de travail avec le portail Azure, l’interface CLI, le kit SDK et les API REST authentifiées.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 3f40606d5fae3b3784ac7f1fdcf4977b7fd9eb1f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251773"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exporter ou supprimer vos données d’espace de travail Machine Learning 

Dans Azure Machine Learning, vous pouvez exporter ou supprimer des données de votre espace de travail avec l’API REST authentifiée. Cet article vous explique comment procéder.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Contrôler les données de votre espace de travail
Les données intégrées au produit stockées par Azure Machine Learning Services sont disponibles pour l’exportation et la suppression via le portail Azure, l’interface CLI, le kit SDK et les API REST authentifiées. Les données de télémétrie sont accessibles via le portail de confidentialité Azure. 

Dans Azure Machine Learning Services, les données personnelles comprennent les informations utilisateur des documents sur l’historique des exécutions et les enregistrements de télémétrie de certaines interactions de l’utilisateur avec le service.

## <a name="delete-workspace-data-with-the-rest-api"></a>Supprimer des données de l’espace de travail avec l’API REST 

Pour supprimer des données, vous pouvez effectuer les appels d’API suivants avec le verbe HTTP DELETE. Ils sont autorisés, car la demande contient un en-tête `Authorization: Bearer <arm-token>`, où `<arm-token>` est le jeton d’accès AAD du point de terminaison `https://management.core.windows.net/`.  

Pour savoir comment obtenir ce jeton et appeler des points de terminaison Azure, consultez la [documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/azure/).  

Dans les exemples suivants, remplacez le texte de {} par les noms d’instance qui déterminent la ressource associée.

### <a name="delete-an-entire-workspace"></a>Supprimer tout un espace de travail

Cet appel permet de supprimer tout un espace de travail.  
> [!WARNING]
> Toutes les informations seront supprimées et l’espace de travail ne sera plus utilisable.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Supprimer des modèles

Cet appel permet d’obtenir la liste des modèles et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Vous pouvez supprimer les modèles individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Supprimer des ressources

Cet appel permet d’obtenir la liste des ressources et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Vous pouvez supprimer les ressources individuelles avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Supprimer des images

Cet appel permet d’obtenir la liste des images et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Vous pouvez supprimer les images individuelles avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Supprimer des services

Cet appel permet d’obtenir la liste des services et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Vous pouvez supprimer les services individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportation des données de service avec l’API REST

Pour exporter des données, vous pouvez effectuer les appels d’API suivants avec le verbe HTTP GET. Ils sont autorisés car la requête contient un en-tête `Authorization: Bearer <arm-token>`, où `<arm-token>` est le jeton d’accès AAD du point de terminaison `https://management.core.windows.net/`.  

Pour savoir comment obtenir ce jeton et appeler des points de terminaison Azure, consultez la [documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/azure/).   

Dans les exemples suivants, remplacez le texte de {} par les noms d’instance qui déterminent la ressource associée.

### <a name="export-workspace-information"></a>Exporter les informations de l’espace de travail

Cet appel permet d’obtenir la liste de tous les espaces de travail :

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Vous pouvez obtenir des informations sur un espace de travail individuel avec :

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exporter les informations de calcul

Vous pouvez obtenir toutes les cibles de calcul attachées à un espace de travail avec :

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Vous pouvez obtenir des informations sur une seule cible de calcul avec :

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportation des données d’historique d’exécution
Cet appel permet d’obtenir la liste de toutes les expérimentations et leurs informations :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Vous pouvez obtenir toutes les exécutions d’une expérimentation spécifique avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Vous pouvez obtenir les éléments de l’historique des exécutions avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Vous pouvez obtenir toutes les métriques d’exécution pour une expérimentation avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Vous pouvez obtenir une seule métrique d’exécution avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportation des artefacts

Cet appel permet d’obtenir la liste des artefacts et leurs noms :

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportation des notifications

Cet appel permet d’obtenir la liste des tâches stockées :     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Vous pouvez obtenir des notifications pour une seule tâche avec :

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exporter des banques de données

Cet appel permet d’obtenir la liste des banques de données :

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Vous pouvez obtenir des banques de données individuelles avec :

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exporter des modèles

Cet appel permet d’obtenir la liste des modèles et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Vous pouvez obtenir les modèles individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exporter des ressources

Cet appel permet d’obtenir la liste des ressources et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Vous pouvez obtenir des ressources individuelles avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exporter des images

Cet appel permet d’obtenir la liste des images et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Vous pouvez obtenir des images individuelles avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exporter des services

Cet appel permet d’obtenir la liste des services et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Vous pouvez obtenir les manifestes individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exporter des expérimentations de pipeline

Vous pouvez obtenir des expérimentations individuelles avec :

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exporter des graphes de pipeline

Vous pouvez obtenir des graphes individuels avec :

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exporter des modules de pipeline

Vous pouvez obtenir des modules avec :

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exporter des modèles de pipeline

Vous pouvez obtenir des modèles avec :

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exporter des sources de données de pipeline

Vous pouvez obtenir des sources de données avec :

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}
