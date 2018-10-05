---
title: Exporter ou supprimer des données d’expérimentation ou de gestion des modèles - Azure Machine Learning | Documents Microsoft
description: Dans Azure Machine Learning, vous pouvez exporter ou supprimer les données de votre compte associées à l’expérimentation ou à la gestion des modèles avec le portail Azure, la CLI, le SDK et les API REST authentifiées. Cet article vous montre comment procéder.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 8c5b20bf837491e8b15f7bc8baa018ead584616b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159174"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Exporter ou supprimer des données d’expérimentation ou de gestion des modèles dans Machine Learning

>[!Note]
>**Cet article est déprécié.** La prise en charge des versions antérieures de ce service va prendre fin progressivement. [Afficher la chronologie de la prise en charge](../service/overview-what-happened-to-workbench.md#timeline). Lisez [l’article le plus récent](../service/how-to-export-delete-data.md) à ce sujet.


Dans Azure Machine Learning, vous pouvez exporter ou supprimer les données de votre compte associées à l’expérimentation ou à la gestion des modèles avec l’API REST authentifiée. Cet article vous explique comment procéder.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Contrôle des données de votre compte
Les données intégrées au produit stockées par les comptes d’expérimentation et de gestion des modèles d’Azure Machine Learning sont disponibles pour l’exportation et la suppression via le portail Azure, la CLI, le SDK et les API REST authentifiées. Les données de télémétrie sont accessibles via le portail de confidentialité Azure. 

Dans Azure Machine Learning, les données personnelles comprennent les informations utilisateur des documents sur l’historique des exécutions et les enregistrements de télémétrie de certaines interactions de l’utilisateur avec le service.

## <a name="delete-account-data-with-the-rest-api"></a>Suppression des données d’un compte avec l’API REST 

Pour supprimer des données, vous pouvez effectuer les appels d’API suivants avec le verbe HTTP DELETE. Ils sont autorisés car la requête contient un en-tête `Authorization: Bearer <arm-token>`, où `<arm-token>` est le jeton d’accès AAD du point de terminaison `https://management.core.windows.net/`.  

Pour savoir comment obtenir ce jeton et appeler des points de terminaison Azure, consultez la [documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/azure/).  

Dans les exemples suivants, remplacez le texte de {} par les noms d’instance qui déterminent la ressource associée.

## <a name="delete-from-a-hosting-account"></a>Suppression d’un compte d’hébergement

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Suppression à partir du service de gestion des modèles

### <a name="model-document"></a>Document des modèles
Cet appel permet d’obtenir la liste des modèles et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Vous pouvez supprimer les modèles individuels avec :  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Document des manifestes
Cet appel permet d’obtenir la liste des manifestes et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Vous pouvez supprimer les manifestes individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Documents des services
Cet appel permet d’obtenir la liste des services et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Vous pouvez supprimer les services individuels avec :    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Document des images
Cet appel permet d’obtenir la liste des images et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Vous pouvez supprimer les images individuelles avec :  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Suppression des données d’historique des exécutions, d’artefact et de notification
Les données d’historique des exécutions, d’artefact et de notification d’un projet sont toutes supprimées après la suppression du document de projet correspondant :

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Suppression à partir du fournisseur de ressources du compte d’expérimentation
Vous pouvez supprimer les documents de projet avec :

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Vous pouvez supprimer les documents d’espace de travail avec :

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Vous pouvez supprimer intégralement le compte d’expérimentation avec :
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Exportation des données de service avec l’API REST
Pour exporter des données, vous pouvez effectuer les appels d’API suivants avec le verbe HTTP GET. Ils sont autorisés car la requête contient un en-tête `Authorization: Bearer <arm-token>`, où `<arm-token>` est le jeton d’accès AAD du point de terminaison `https://management.core.windows.net/`.  

Pour savoir comment obtenir ce jeton et appeler des points de terminaison Azure, consultez la [documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/azure/).   

Dans les exemples suivants, remplacez le texte de {} par les noms d’instance qui déterminent la ressource associée.

## <a name="export-hosting-account-data"></a>Exportation des données du compte d’hébergement

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Exportation des données du service de gestion des modèles
### <a name="model-document"></a>Document des modèles

Cet appel permet d’obtenir la liste des modèles et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Vous pouvez obtenir les modèles individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifestes
Cet appel permet d’obtenir la liste des manifestes et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Vous pouvez obtenir les manifestes individuels avec :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Services
Cet appel permet d’obtenir la liste des services et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Vous pouvez obtenir les manifestes individuels avec : 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Images
Cet appel permet d’obtenir la liste des images et leurs ID :

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Vous pouvez obtenir les manifestes individuels avec : 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Exportation des données de calcul
### <a name="compute-clusters"></a>Clusters de calcul
Cet appel permet d’obtenir la liste des clusters de calcul et leurs noms :

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Vous pouvez obtenir les clusters individuels avec :

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Clusters d’opérationnalisation
Cet appel permet d’obtenir la liste des clusters et leurs noms :

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Vous pouvez obtenir les clusters individuels avec :

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Exportation des données d’historique d’exécution
Cet appel permet d’obtenir la liste des exécutions et leurs ID :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Cet appel permet d’obtenir la liste des essais et leurs ID :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Vous pouvez obtenir les éléments de l’historique des exécutions avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Vous pouvez obtenir les éléments de mesure des exécutions avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Vous pouvez obtenir les essais d’exécution avec :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Artefacts de l’historique des exécutions :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

URI des artefacts de l’historique des exécutions :

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Exportation des artefacts
Cet appel permet d’obtenir la liste des ressources et leurs noms :

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Cet appel permet d’obtenir la liste des artefacts et leurs noms :

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Contenu des artefacts

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Documents des artefacts

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Éléments multimédias

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Exportation des notifications

1. Accédez à la [section Utilisateurs du portail Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/), puis sélectionnez un utilisateur dans la colonne **Nom**. 
2. Notez **l’ID de l’objet**et utilisez-le dans l’appel suivant :     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Exportation des informations du compte d’expérimentation
### <a name="experimentation-account-information"></a>Informations sur le compte d’expérimentation

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Informations sur l’espace de travail

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Informations sur le projet

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
