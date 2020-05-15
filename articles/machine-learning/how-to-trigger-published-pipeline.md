---
title: Déclencher un pipeline ML pour de nouvelles données
titleSuffix: Azure Machine Learning
description: Découvrez comment déclencher l’exécution d’un pipeline ML à l’aide d’Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: contperfq4
ms.openlocfilehash: 32b3e153a98ca7de91e0dd05258414780b39dec4
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857962"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Déclencher une exécution d’un pipeline Machine Learning à partir d’une application logique

Déclenchez l’exécution de votre pipeline Azure Machine Learning quand de nouvelles données s’affichent. Par exemple, vous pouvez vouloir déclencher le pipeline pour entraîner un nouveau modèle quand de nouvelles données apparaissent dans le compte de stockage Blob. Configurez le déclencheur avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le point de terminaison REST d’un pipeline Machine Learning publié. [Créez et publiez votre pipeline](how-to-create-your-first-pipeline.md). Recherchez ensuite le point de terminaison REST de votre PublishedPipeline à l’aide de l’ID de pipeline :
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* Le [Stockage Blob Azure](../storage/blobs/storage-blobs-overview.md) pour stocker vos données.
* [Un magasin de données](how-to-access-data.md) dans votre espace de travail qui contient les détails de votre compte de stockage Blob.

## <a name="create-a-logic-app"></a>Créer une application logique

Créez maintenant une instance [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Si vous le souhaitez, [utilisez un environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) et [configurez une clé gérée par le client](../logic-apps/customer-managed-keys-integration-service-environment.md) destinée à être utilisée par votre application logique.

Une fois que votre application logique a été provisionnée, effectuez les étapes suivantes afin de configurer un déclencheur pour votre pipeline :

1. [Créez une identité managée affectée par le système](../logic-apps/create-managed-service-identity.md) pour permettre à l’application d’accéder à votre espace de travail Azure Machine Learning.

1. Accédez à la vue Concepteur d’application logique, puis sélectionnez le modèle Application logique vide. 
    > [!div class="mx-imgBorder"]
    > ![Modèle vide](media/how-to-trigger-published-pipeline/blank-template.png)

1. Dans le concepteur, recherchez **blob**. Sélectionnez le déclencheur **Quand un blob est ajouté ou modifié (propriétés uniquement)** , puis ajoutez ce déclencheur à votre application logique.
    > [!div class="mx-imgBorder"]
    > ![Ajouter un déclencheur](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Renseignez les informations de connexion pour le compte de stockage Blob que vous voulez superviser pour les ajouts ou modifications d’objets blob. Sélectionnez le conteneur à superviser. 
 
    Choisissez l’**Intervalle** et la **Fréquence** pour interroger les mises à jour qui vous conviennent.  

    > [!NOTE]
    > Ce déclencheur supervisera le conteneur sélectionné, mais pas les sous-dossiers.

1. Ajoutez une action HTTP qui s’exécutera quand un objet blob nouveau ou modifié est détecté. Sélectionnez **+ Nouvelle étape**, puis recherchez et sélectionnez l’action HTTP.

  > [!div class="mx-imgBorder"]
  > ![Rechercher une action HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Utilisez les paramètres suivants pour configurer votre action :

  | Paramètre | Valeur | 
  |---|---|
  | Action HTTP | POST |
  | URI |Le point de terminaison du pipeline publié que vous avez trouvé comme [condition préalable](#prerequisites) |
  | Mode d'authentification | Identité managée |

1. Configurez votre planification pour définir la valeur de tout [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que vous pourriez avoir :

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Utilisez le `DataStoreName` que vous avez ajouté à votre espace de travail en tant que [condition préalable](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Paramètres HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Sélectionnez **Enregistrer**. Votre planification est maintenant prête.
