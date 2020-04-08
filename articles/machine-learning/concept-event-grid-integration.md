---
title: Consommer des événements Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dans cet article, vous allez apprendre à utiliser Azure Event Grid pour vous abonner, réagir et annuler l’abonnement aux événements générés par Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139043"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Consommer des événements Azure Machine Learning (préversion)

Azure Machine Learning gère l’intégralité du cycle de vie du processus d’apprentissage automatique, y compris l’apprentissage du modèle, le déploiement du modèle et la surveillance. Les événements Azure Machine Learning permettent aux applications de réagir aux événements pendant le cycle de vie d’apprentissage automatique, comme lors de l’achèvement des exécutions d’apprentissage, de l’enregistrement et du déploiement de modèles et de la détection de la dérive de données, à l’aide d’architectures serverless modernes. 

Ces événements sont publiés via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). À l’aide du Portail Azure, de PowerShell ou de Azure CLI, les clients peuvent facilement s’abonner aux événements en [spécifiant un ou plusieurs types d’événements, et en filtrant des conditions](/azure/event-grid/event-filtering). Les clients ont également la possibilité de créer un large éventail de gestionnaires d’événements, tels que Azure Functions, Azure Logic Apps ou des webhooks génériques. Azure Machine Learning, ainsi que Azure Event Grid, fournissent une plateforme de remise d’événements haute disponibilité, fiable et tolérante aux pannes pour créer des applications pilotées par des événements.

Pour plus d’informations sur l’utilisation d’Azure Machine Learning avec Event Grid, consultez [Create Event Driven Machine Learning Workflows (Preview)](how-to-use-event-grid.md) (Créer des flux de travail Machine Learning pilotés par des événements [préversion]).

## <a name="the-event-model"></a>Le modèle d’événement 

Azure Event Grid lit les événements à partir de sources, comme Azure Machine Learning et d’autres services Azure. Ces événements sont ensuite envoyés à des gestionnaires d’événements tels qu’Azure Event Hubs, Azure Functions, Logic Apps et d’autres. Le diagramme suivant montre comment Event Grid connecte les sources et les descripteurs, mais ne constitue pas une liste complète des intégrations prises en charge.

![Modèle Azure Event Grid fonctionnel](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Pour plus d’informations sur les sources d’événements et les gestionnaires d’événements, consultez [Qu’est-ce qu’Event Grid ?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Types d’événements Azure Machine Learning

Azure Machine Learning fournit des événements tout au long du cycle de vie de l’apprentissage automatique : 

| Type d'événement | Description |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Déclenché lorsque l’exécution d’une expérience d’apprentissage automatique est terminée |
| `Microsoft.MachineLearningServices.ModelRegistered` | Déclenché lorsqu’un modèle d’apprentissage automatique est inscrit dans l’espace de travail |
| `Microsoft.MachineLearningServices.ModelDeployed` | Déclenché lorsqu’un déploiement du service d’inférence avec un ou plusieurs modèles est terminé |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Déclenché lorsqu’un travail de détection de dérive de données pour deux jeux de données est terminé |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Déclenché quand un état d’exécution changeait ; actuellement déclenché uniquement quand l’état d’exécution a la valeur « failed » |

## <a name="subscribe-to-machine-learning-events"></a>S’abonner à des événements Azure Machine Learning

Les abonnements pour les événements Azure Machine Learning sont protégés par le contrôle d’accès en fonction du rôle (RBAC). Seul le [contributeur ou propriétaire](how-to-assign-roles.md#default-roles) d’un espace de travail peut créer, mettre à jour et supprimer des abonnements aux événements.

Les abonnements aux événements peuvent être filtrés selon différentes conditions. Des filtres peuvent être appliqués aux abonnements aux événements pendant la [création](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) de l’abonnement aux événements ou [ultérieurement](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrer par type d’événement
Un abonnement aux événements peut spécifier un ou plusieurs types d’événements Azure Machine Learning.

### <a name="filter-by-event-subject"></a>Filtrer par sujet d’événement
Azure Event Grid prend en charge les filtres de sujet reposant sur les correspondances __commence par__ et __se termine par__, afin que les événements dont l’objet correspond soient remis à l’abonné. Des événements d’apprentissage automatique différents ont un format de sujet différent.

| Type d'événement | Format du sujet | Exemple de sujet |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Filtrage avancé

Azure Event Grid prend également en charge le filtrage avancé basé sur le schéma d’événement publié. D’avantage d’informations sur le schéma des événement Azure Machine Learning peuvent être trouvées dans [Schéma des événements Azure Event Grid pour Azure Machine Learning](../event-grid/event-schema-machine-learning.md).

Voici quelques exemples de filtres avancés que vous pouvez effectuer :

* Pour l’événement `Microsoft.MachineLearningServices.ModelRegistered`, afin de filtrer la valeur de balise du modèle :

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Pour en savoir plus sur l’application de filtres, consultez [Filtrer des événements pour Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Consommer des événements Machine Learning

Les applications qui gèrent des événements Machine Learning doivent suivre certaines pratiques recommandées :

> [!div class="checklist"]
> * Comme plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, il est important de ne pas considérer que les événements proviennent d’une source particulière, mais de vérifier le sujet du message pour vous assurer qu’il provient d’un espace de travail de Machine Learning que vous attendez.
> * De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
> * Les messages pouvant arriver en désordre et après un certain temps, utilisez les champs etag pour comprendre si vos informations sur les objets sont toujours à jour.  En outre, utilisez les champs de séquence pour comprendre l’ordre des événements sur un objet particulier.
> * Ignorez les champs que vous ne comprenez pas. Cette pratique vous aidera à prendre en charge les nouvelles fonctionnalités qui peuvent être ajoutées à l’avenir.
> * Les opérations Azure Machine Learning annulées ou ayant échoué ne déclenchent pas d’événement. Par exemple, en cas d’échec du déploiement d’un modèle, Microsoft.MachineLearningServices.ModelDeployed ne sera pas déclenché. Prenez en compte ce mode d’échec lors de la conception de vos applications. Vous pouvez toujours utiliser le Kit de développement logiciel (SDK), l’interface CLI ou le portail Azure Machine Learning pour vérifier l’état d’une opération et comprendre les raisons détaillées de l’échec.

Azure Event Grid permet aux clients de créer des gestionnaires de messages découplés, qui peuvent être déclenchés par des événements Azure Machine Learning. Voici quelques exemples notables de gestionnaires de messages :
* Azure Functions
* Azure Logic Apps
* Hubs d'événements Azure
* Pipeline Azure Data Factory
* Webhooks génériques, qui peuvent être hébergés sur la plateforme Azure ou ailleurs

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Event Grid et essayer les événements Azure Machine Learning :

- [À propos d’Event Grid](../event-grid/overview.md)
- [Schéma des événements Azure Event Grid pour Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Créer des flux de travail pilotés par les événements avec Azure Machine Learning](how-to-use-event-grid.md)
