---
title: Exporter ou supprimer les données d’un espace de travail
titleSuffix: Azure Machine Learning
description: Apprenez à exporter ou supprimer votre espace de travail avec Azure Machine Learning Studio, l’interface CLI, le kit SDK et les API REST authentifiées.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 657f6dd32c18b5b0745883da02563e9f5257d92d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87307214"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exporter ou supprimer vos données d’espace de travail Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans Azure Machine Learning, vous pouvez exporter ou supprimer des données de votre espace de travail avec l’API REST authentifiée. Cet article vous explique comment procéder.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Contrôler les données de votre espace de travail

Les données intégrées au produit stockées par Azure Machine Learning sont disponibles pour l’exportation et la suppression par le biais d’Azure Machine Learning Studio, de l’interface CLI, du kit SDK et des API REST authentifiées. Les données de télémétrie sont accessibles via le portail de confidentialité Azure. 

Dans Azure Machine Learning, les données personnelles comprennent les informations utilisateur des documents sur l’historique des exécutions et les enregistrements de télémétrie de certaines interactions de l’utilisateur avec le service.

## <a name="delete-workspace-data-with-the-rest-api"></a>Supprimer des données de l’espace de travail avec l’API REST

Pour supprimer des données, vous pouvez effectuer les appels d’API suivants avec le verbe HTTP DELETE. Ils sont autorisés, car la demande contient un en-tête `Authorization: Bearer <arm-token>`, où `<arm-token>` est le jeton d’accès AAD du point de terminaison `https://management.core.windows.net/`.  

Pour savoir comment obtenir ce jeton et appeler des points de terminaison Azure, consultez [Utiliser REST pour gérer des ressources ML](how-to-manage-rest.md) et la [documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/azure/).  

Dans les exemples suivants, remplacez le texte de {} par les noms d’instance qui déterminent la ressource associée.

### <a name="delete-an-entire-workspace"></a>Supprimer tout un espace de travail

Cet appel permet de supprimer tout un espace de travail.  
> [!WARNING]
> Toutes les informations seront supprimées et l’espace de travail ne sera plus utilisable.

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="delete-models"></a>Supprimer des modèles

Cet appel permet d’obtenir la liste des modèles et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Vous pouvez supprimer les modèles individuels avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="delete-assets"></a>Supprimer des ressources

Cet appel permet d’obtenir la liste des ressources et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Vous pouvez supprimer les ressources individuelles avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="delete-images"></a>Supprimer des images

Cet appel permet d’obtenir la liste des images et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Vous pouvez supprimer les images individuelles avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="delete-services"></a>Supprimer des services

Cet appel permet d’obtenir la liste des services et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Vous pouvez supprimer les services individuels avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

## <a name="export-service-data-with-the-rest-api"></a>Exportation des données de service avec l’API REST

Pour exporter des données, vous pouvez effectuer les appels d’API suivants avec le verbe HTTP GET. Ils sont autorisés car la requête contient un en-tête `Authorization: Bearer <arm-token>`, où `<arm-token>` est le jeton d’accès AAD du point de terminaison `https://management.core.windows.net/`.  

Pour savoir comment obtenir ce jeton et appeler des points de terminaison Azure, consultez [Utiliser REST pour gérer des ressources ML](how-to-manage-rest.md) et la [documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/azure/).   

Dans les exemples suivants, remplacez le texte de {} par les noms d’instance qui déterminent la ressource associée.

### <a name="export-workspace-information"></a>Exporter les informations de l’espace de travail

Cet appel permet d’obtenir la liste de tous les espaces de travail :

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01`

Vous pouvez obtenir des informations sur un espace de travail individuel avec :

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="export-compute-information"></a>Exporter les informations de calcul

Vous pouvez obtenir toutes les cibles de calcul attachées à un espace de travail avec :

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01`

Vous pouvez obtenir des informations sur une seule cible de calcul avec :

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01`

### <a name="export-run-history-data"></a>Exportation des données d’historique d’exécution

Cet appel permet d’obtenir la liste de toutes les expérimentations et leurs informations :

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments`

Vous pouvez obtenir toutes les exécutions d’une expérimentation spécifique avec :

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs`

Vous pouvez obtenir les éléments de l’historique des exécutions avec :

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId}`

Vous pouvez obtenir toutes les métriques d’exécution pour une expérimentation avec :

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics`

Vous pouvez obtenir une seule métrique d’exécution avec :

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}`

### <a name="export-artifacts"></a>Exportation des artefacts

Cet appel permet d’obtenir la liste des artefacts et leurs noms :

`https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}`

### <a name="export-notifications"></a>Exportation des notifications

Cet appel permet d’obtenir la liste des tâches stockées :

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks`

Vous pouvez obtenir des notifications pour une seule tâche avec :

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}`

### <a name="export-data-stores"></a>Exporter des banques de données

Cet appel permet d’obtenir la liste des banques de données :

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores`

Vous pouvez obtenir des banques de données individuelles avec :

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}`

### <a name="export-models"></a>Exporter des modèles

Cet appel permet d’obtenir la liste des modèles et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Vous pouvez obtenir les modèles individuels avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="export-assets"></a>Exporter des ressources

Cet appel permet d’obtenir la liste des ressources et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Vous pouvez obtenir des ressources individuelles avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="export-images"></a>Exporter des images

Cet appel permet d’obtenir la liste des images et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

Vous pouvez obtenir des images individuelles avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="export-services"></a>Exporter des services

Cet appel permet d’obtenir la liste des services et leurs ID :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Vous pouvez obtenir les manifestes individuels avec :

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

### <a name="export-pipeline-experiments"></a>Exporter des expérimentations de pipeline

Vous pouvez obtenir des expérimentations individuelles avec :

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}`

### <a name="export-pipeline-graphs"></a>Exporter des graphes de pipeline

Vous pouvez obtenir des graphes individuels avec :

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}`

### <a name="export-pipeline-modules"></a>Exporter des modules de pipeline

Vous pouvez obtenir des modules avec :

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}`

### <a name="export-pipeline-templates"></a>Exporter des modèles de pipeline

Vous pouvez obtenir des modèles avec :

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}`

### <a name="export-pipeline-data-sources"></a>Exporter des sources de données de pipeline

Vous pouvez obtenir des sources de données avec :

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}`

## <a name="delete-assets-in-the-designer"></a>Supprimer des ressources dans le concepteur

Dans le concepteur où vous avez créé votre expérimentation, supprimez des ressources individuelles :

1. Accéder au concepteur

    ![Supprimer des ressources](./media/how-to-export-delete-data/delete-experiment.png)

1. Dans la liste, sélectionnez le brouillon de pipeline individuel à supprimer.

1. Sélectionnez **Supprimer**.

### <a name="delete-datasets-in-the-designer"></a>Supprimer des jeux de données dans le concepteur

Pour supprimer des jeux de données dans le concepteur, utilisez le portail Azure ou l’Explorateur Stockage pour accéder aux comptes de stockage connectés et y supprimer les jeux de données. La désinscription de jeux de données dans le concepteur supprime seulement le point de référence dans le stockage.

