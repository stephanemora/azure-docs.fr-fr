---
title: Créer un service lié avec des espaces de travail Synapse et Azure Machine Learning (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment lier des espaces de travail Azure Synapse et Azure Machine Learning, et attacher des pools Apache Spark pour une expérience de data wrangling unifiée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 558610a23098a64f0f36ccb5c04ee71e8a7343cb
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408458"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-and-attach-apache-spark-poolspreview"></a>Lier des espaces de travail Azure Synapse Analytics et Azure Machine Learning et attacher des pools Apache Spark (préversion)

Dans cet article, vous allez découvrir comment créer un service lié qui relie votre espace de travail [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) et votre [espace de travail Azure Machine Learning](concept-workspace.md). 

Une fois l’espace de travail Azure Machine Learning lié à votre espace de travail Azure Synapse, vous pouvez attacher un pool Apache Spark, à technologie Azure Synapse Analytics, en tant que calcul dédié pour un data wrangling à grande échelle ou effectuer un entraînement de modèle, le tout à partir du même notebook Python.

Vous pouvez lier votre espace de travail ML et votre espace de travail Synapse par le biais du [SDK Python](#link-sdk) ou d’[Azure Machine Learning Studio](#link-studio).

Vous pouvez aussi lier des espaces de travail et attacher un pool Synapse Spark avec un seul [modèle Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> L’intégration d’Azure Machine Learning et d’Azure Synapse est disponible en préversion. Les fonctionnalités du package `azureml-synapse` qui sont présentées sont des fonctionnalités [expérimentales](/python/api/overview/azure/ml/#stable-vs-experimental) en préversion et peuvent changer à tout moment.

## <a name="prerequisites"></a>Prérequis

* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Créez un espace de travail Synapse dans le portail Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Créez un pool Apache Spark à l’aide du portail Azure, d’outils web ou de Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md).

* Installez le [SDK Python d’Azure Machine Learning](/python/api/overview/azure/ml/intro).

* Accédez au [studio Azure Machine Learning](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Lier des espaces de travail avec le SDK Python

> [!IMPORTANT]
> Pour établir correctement une liaison à l’espace de travail Synapse, vous devez disposer du rôle **Propriétaire** dans cet espace. Vérifiez votre accès dans le [portail Azure](https://ms.portal.azure.com/).
>
> Si vous n’êtes pas **Propriétaire** de l’espace de travail Synapse, mais seulement **Contributeur**, vous ne pouvez utiliser que les services liés existants. Découvrez comment [récupérer et utiliser un service lié existant](#get-an-existing-linked-service).

Le code suivant utilise les classes [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) et [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) pour :

* Lier votre espace de travail de Machine Learning `ws` à votre espace de travail Azure Synapse.
* Inscrire votre espace de travail Synapse auprès d’Azure Machine Learning en tant que service lié.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Une identité managée, `system_assigned_identity_principal_id`, est créée pour chaque service lié. Cette identité managée doit se voir accorder le rôle **Administrateur Synapse Apache Spark** de l’espace de travail Synapse avant que vous ne démarriez votre session Synapse. [Affectez le rôle Administrateur Synapse Apache Spark à l’identité managée dans Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Pour rechercher le `system_assigned_identity_principal_id` d’un service lié spécifique, utilisez `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')`.

### <a name="manage-linked-services"></a>Gérer des services liés

Affichez tous les services liés associés à votre espace de travail de Machine Learning.

```python
LinkedService.list(ws)
```

Pour dissocier vos espaces de travail, utilisez la méthode `unregister()`.

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Lier des espaces de travail par le biais de Studio

Liez votre espace de travail de Machine Learning et votre espace de travail Synapse par le biais d’Azure Machine Learning Studio en procédant comme suit : 

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Services liés** dans la section **Gérer** du volet gauche.
1. Sélectionnez **Ajouter une intégration**.
1. Renseignez les champs du formulaire **Lier l’espace de travail**.

    |Champ| Description    
    |---|---
    |Nom| Fournissez un nom pour votre service lié. Ce nom sera utilisé pour référencer ce service lié particulier.
    |Nom d’abonnement | Sélectionnez le nom de votre abonnement associé à votre espace de travail de Machine Learning. 
    |Espace de travail Synapse | Sélectionnez l’espace de travail Synapse vers lequel vous souhaitez établir un lien.
    
1. Sélectionnez **Suivant** pour ouvrir le formulaire **Sélectionner des pools Spark (facultatif)** . Dans ce formulaire, vous sélectionnez le pool Synapse Spark à attacher à votre espace de travail.

1. Sélectionnez **Suivant** pour ouvrir le formulaire **Vérifier** et vérifier vos sélections.
1. Sélectionnez **Créer** pour achever le processus de création du service lié.

## <a name="get-an-existing-linked-service"></a>Obtenir un service lié existant

Avant de pouvoir attacher un calcul dédié au data wrangling, vous devez disposer d’un espace de travail ML lié à un espace de travail Azure Synapse Analytics, ce que l’on appelle un service lié. 

Pour récupérer et utiliser un service lié existant, vous devez disposer des autorisations **Utilisateur ou Collaborateur** sur l’**espace de travail Azure Synapse Analytics**.

Cet exemple récupère un service lié existant, `synapselink1`, à partir de l’espace de travail, `ws`, avec la méthode [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-).
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```

## <a name="attach-synapse-spark-pool-as-a-compute"></a>Attacher un pool Synapse Spark en tant que calcul

Après avoir récupéré le service lié, attachez un pool Synapse Apache Spark en tant que ressource de calcul dédiée à vos tâches de data wrangling. 

Vous pouvez attacher des pools Apache Spark en utilisant :
* Azure Machine Learning Studio
* [Modèles Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Le kit SDK Python d’Azure Machine Learning 

### <a name="attach-a-pool-via-the-studio"></a>Attacher un pool par le biais du studio
Suivez les étapes ci-dessous : 

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).
1. Sélectionnez **Services liés** dans la section **Gérer** du volet gauche.
1. Sélectionnez votre espace de travail Synapse.
1. Sélectionnez **Pools Spark attachés** en haut à gauche. 
1. Sélectionnez **Attacher**. 
1. Sélectionnez votre pool Apache Spark dans la liste et indiquez un nom.  
    1. Cette liste identifie les pools Synapse Spark disponibles qui peuvent être attachés à votre calcul. 
    1. Pour créer un pool Synapse Spark, consultez [Créer un pool Apache Spark avec Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
1. Sélectionnez **Attacher l’élément sélectionné**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Attacher un pool à l’aide du kit SDK Python

Vous pouvez également utiliser le **SDK Python** pour attacher un pool Apache Spark. 

Le code suivant, 
1. Configure le [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) avec,

   1. Le [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice), `linked_service` que vous avez créé ou récupéré à l’étape précédente. 
   1. Le type de cible de calcul que vous souhaitez attacher, `SynapseSpark`.
   1. Le nom du pool Apache Spark. Celui-ci doit correspondre à un pool Apache Spark existant qui se trouve dans votre espace de travail Azure Synapse Analytics.
   
1. Crée un [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) de Machine Learning en passant, 
   1. L’espace de travail de Machine Learning que vous souhaitez utiliser, `ws`
   1. Le nom que vous souhaitez pour faire référence au calcul dans l’espace de travail Azure Machine Learning. 
   1. Le paramètre attach_configuration que vous avez spécifié lors de la configuration de votre calcul Synapse.
       1. L’appel à ComputeTarget.attach() étant asynchrone, l’exemple se bloque jusqu’à la fin de l’appel.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Vérifiez que le pool Apache Spark est attaché.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour effectuer un data wrangling avec Azure Synapse (préversion)](how-to-data-prep-synapse-spark-pool.md).
* [Guide pratique pour utiliser Apache Spark dans votre pipeline de Machine Learning avec Azure Synapse (préversion)](how-to-use-synapsesparkstep.md)
* [Effectuer l’apprentissage d’un modèle](how-to-set-up-training-targets.md).