---
title: Créer un service lié avec des espaces de travail Synapse et Azure Machine Learning (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment lier des espaces de travail Azure Synapse et Azure Machine Learning pour une expérience de data wrangling unifiée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: d1c4defc53c4af0fb481a57c0a455e987fdd480a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519994"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Lier des espaces de travail Azure Synapse Analytics et Azure Machine Learning (préversion)

Dans cet article, vous allez découvrir comment créer un service lié qui relie votre espace de travail [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) et votre [espace de travail Azure Machine Learning](concept-workspace.md).

Une fois l’espace de travail Azure Machine Learning lié à l’espace de travail Azure Synapse, vous pouvez attacher un pool Apache Spark en tant que calcul dédié pour un data wrangling à grande échelle et effectuer un entraînement de modèle à partir du même notebook.

Vous pouvez lier votre espace de travail ML et votre espace de travail Synapse par le biais du [SDK Python](#link-sdk) ou d’[Azure Machine Learning Studio](#link-studio).

Vous pouvez aussi lier des espaces de travail et attacher un pool Synapse Spark avec un seul [modèle Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> L’intégration d’Azure Machine Learning et d’Azure Synapse est disponible en préversion. Les fonctionnalités du package `azureml-synapse` qui sont présentées sont des fonctionnalités [expérimentales](/python/api/overview/azure/ml/#stable-vs-experimental) en préversion et peuvent changer à tout moment.

## <a name="prerequisites"></a>Prérequis

* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Créez un espace de travail Synapse dans le portail Azure](/synapse-analytics/quickstart-create-workspace.md).

* [Créez un pool Apache Spark à partir du portail Azure, d’outils web ou de Synapse Studio](/synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

* Installez le [SDK Python d’Azure Machine Learning](/python/api/overview/azure/ml/intro).

* Accédez au [studio Azure Machine Learning](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Lier des espaces de travail avec le SDK Python

> [!IMPORTANT]
> Pour établir correctement une liaison à l’espace de travail Synapse, vous devez disposer du rôle **Propriétaire** dans cet espace. Vérifiez votre accès dans le [portail Azure](https://ms.portal.azure.com/).
>
> Si vous n’êtes pas **Propriétaire** de l’espace de travail Synapse, mais seulement **Contributeur**, vous ne pouvez utiliser que les services liés existants. Découvrez comment [récupérer et utiliser un service lié existant](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

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
    Champ| Description    
    ---|---
    Nom| Fournissez un nom pour votre service lié. Ce nom sera utilisé pour référencer ce service lié particulier.
    Nom d’abonnement | Sélectionnez le nom de votre abonnement associé à votre espace de travail de Machine Learning. 
    Espace de travail Synapse | Sélectionnez l’espace de travail Synapse vers lequel vous souhaitez établir un lien.
1. Sélectionnez **Suivant** pour ouvrir le formulaire **Sélectionner des pools Spark (facultatif)** . Dans ce formulaire, vous sélectionnez le pool Synapse Spark à attacher à votre espace de travail.

1. Sélectionnez **Suivant** pour ouvrir le formulaire **Vérifier** et vérifier vos sélections.
1. Sélectionnez **Créer** pour achever le processus de création du service lié.

## <a name="next-steps"></a>Étapes suivantes

* [Attacher des pools Synapse Spark pour la préparation des données avec Azure Synapse (préversion)](how-to-data-prep-synapse-spark-pool.md).
* [Guide pratique pour utiliser Apache Spark dans votre pipeline de Machine Learning avec Azure Synapse (préversion)](how-to-use-synapsesparkstep.md)
* [Effectuer l’apprentissage d’un modèle](how-to-set-up-training-targets.md).
