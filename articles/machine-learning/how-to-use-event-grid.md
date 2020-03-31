---
title: Créer des flux de travail Machine Learning basés sur les événements
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Event Grid avec Azure Machine Learning pour activer des solutions basées sur les événements.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129734"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Créer des flux de travail Machine Learning basés sur les événements (préversion)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) prend en charge les événements Azure Machine Learning. Vous pouvez vous abonner et utiliser des événements tels que la modification de l’état d’exécution, la fin d’une exécution, l’inscription de modèles, le déploiement de modèles et la détection d’une dérive de données dans un espace de travail.

Pour plus d’informations sur les types d’événements, consultez [Intégration d’Azure Machine Learning avec Event Grid](concept-event-grid-integration.md) et [Schéma Event Grid pour Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Utilisez Event Grid pour activer des scénarios courants tels que :

* Envoyer des e-mails à la fin d’une exécution et en cas d’échec d’exécution
* Utiliser une fonction Azure après l’inscription d’un modèle
* Événements de diffusion en continu d’Azure Machine Learning sur divers points de terminaison
* Déclencher un pipeline ML quand une dérive est détectée

> [!NOTE] 
> Actuellement, les événements runStatusChanged se déclenchent uniquement lorsque l’état d’exécution est **échec**
>

## <a name="prerequisites"></a>Conditions préalables requises
* Accès du contributeur ou du propriétaire à l’espace de travail Azure Machine Learning pour lequel vous allez créer des événements.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Configurer EventGrid à l’aide du portail Azure

1. Ouvrez le [Portail Azure](https://portal.azure.com) et accédez à votre espace de travail Azure Machine Learning.

1. Dans la barre de gauche, sélectionnez __Événements__ puis sélectionnez **Abonnements aux événements**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Sélectionnez le type d’événement à consommer. Par exemple, la capture d’écran suivante a sélectionné __Modèle inscrit__, __Modèle déployé__, __Exécution terminée__ et __Dérive de jeu de données détectée__ :

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Sélectionnez le point de terminaison sur lequel publier l’événement. Dans la capture d’écran suivante, __Event Hub__ est le point de terminaison sélectionné :

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Une fois que vous avez confirmé votre sélection, cliquez sur __Créer__. Après la configuration, ces événements seront envoyés à votre point de terminaison.


### <a name="configure-eventgrid-using-the-cli"></a>Configurer EventGrid à l’aide de l’interface CLI

Vous pouvez installer la dernière version d’[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou utiliser Azure Cloud Shell fourni dans le cadre de votre abonnement Azure.

Pour installer l’extension Event Grid, utilisez la commande suivante à partir de l’interface CLI :

```azurecli-interactive
az add extension --name eventgrid
```

L’exemple suivant montre comment sélectionner un abonnement Azure et créer un abonnement aux événements pour Azure Machine Learning :

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>Filtrer les événements

Lors de la configuration de vos événements, vous pouvez appliquer des filtres de manière à ce qu’ils ne se déclenchent que sur des données d’événements spécifiques. Dans l’exemple ci-dessous, pour les événements de modification de l’état d’exécution, vous pouvez filtrer par type d’exécution. L’événement se déclenche uniquement lorsque les critères sont satisfaits. Pour en savoir plus sur les données d’événement que vous pouvez filtrer, reportez-vous au [schéma Event Grid pour Azure Machine Learning](/azure/event-grid/event-schema-machine-learning). 

1. Accédez au Portail Azure, sélectionnez un nouvel abonnement ou un abonnement existant. 

1. Sélectionnez l’onglet Filtres et faites défiler jusqu’à Filtres avancés. Dans les champs **Clé** et **Valeur**, fournissez les types de propriété par lesquels vous souhaitez filtrer. Ici, vous pouvez voir que l’événement se déclenche uniquement lorsque le type d’exécution est une exécution de pipeline ou une exécution d’étape de pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtrer les événements":::

## <a name="sample-scenarios"></a>Exemples de scénarios

### <a name="use-a-logic-app-to-send-email-alerts"></a>Utiliser une application logique pour envoyer des alertes par e-mail

Tirez parti de [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) pour configurer des e-mails pour tous vos événements. Personnalisez avec des conditions et spécifiez des destinataires pour permettre la collaboration et la sensibilisation entre les équipes travaillant ensemble.

1. Dans la Portail Azure, accédez à votre espace de travail Azure Machine Learning et sélectionnez l’onglet Événements dans la barre de gauche. À partir de là, sélectionnez __Applications logiques__. 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Connectez-vous à l’interface utilisateur de l’application logique et sélectionnez le service Machine Learning comme type de rubrique. 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. Sélectionnez le ou les événements pour lesquels vous souhaitez recevoir des alertes. Par exemple, la capture d’écran suivante __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Vous pouvez utiliser la méthode de filtrage de la section précédente ou ajouter des filtres pour déclencher uniquement l’application logique sur un sous-ensemble de types d’événements. Dans la capture d’écran suivante, un __filtre de préfixe__ de __/datadriftID/runs/__ est utilisé.

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Ensuite, ajoutez une étape pour utiliser cet événement et rechercher un e-mail. Il existe plusieurs comptes de messagerie différents que vous pouvez utiliser pour recevoir des événements. Vous pouvez également configurer des conditions sur le moment où envoyer une alerte par e-mail.

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. Sélectionnez __Envoyer un e-mail__ et renseignez les paramètres. Dans l’objet, vous pouvez inclure le __Type d’événement__ et la __Rubrique__ pour aider à filtrer les événements. Vous pouvez également inclure un lien vers la page de l’espace de travail pour les exécutions dans le corps du message. 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. Pour enregistrer cette action, sélectionnez **Enregistrer sous** dans le coin gauche de la page. Dans la barre de droite qui s’affiche, confirmez la création de cette action.

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Utiliser une application logique pour déclencher des flux de travail de reformation lorsque la dérive de données se produit

Les modèles deviennent obsolètes au fil du temps et ne restent pas fonctionnels dans le contexte dans lequel ils s’exécutent. Pour savoir s’il est temps de reformer le modèle, vous pouvez détecter la dérive des données. 

Cet exemple montre comment utiliser Event Grid avec une application logique Azure pour déclencher la reformation. L’exemple déclenche un pipeline Azure Data Factory lorsqu’une dérive de données se produit entre les jeux de données de formation et de service d’un modèle.

Avant de commencer, effectuez les étapes suivantes :

* Configurer une analyse de jeu de données pour [détecter la dérive de données]( https://aka.ms/datadrift) dans un espace de travail
* Créer un [pipeline Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) publié.

Dans cet exemple, un pipeline Data Factory simple est utilisé pour copier des fichiers dans un magasin d’objets blob et exécuter un pipeline Machine Learning publié. Pour plus d’informations sur ce scénario, découvrez comment configurer une [étape de Machine Learning dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Commencez par créer l’application logique. Accédez au [Portail Azure](https://portal.azure.com), recherchez Applications logiques, puis sélectionnez Créer.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Entrez les informations demandées. Pour simplifier l’expérience, utilisez le même abonnement et le même groupe de ressources que votre pipeline Azure Data Factory et votre espace de travail Azure Machine Learning.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Une fois que vous avez créé l’application logique, sélectionnez __Quand un événement lié à une ressource Event Grid se produit__. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Connectez-vous et renseignez les détails de l’événement. Définissez le __Nom de la ressource__ sur le nom de l’espace de travail. Définissez le __type d’événement__ sur __DatasetDriftDetected__.

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Ajoutez une nouvelle étape, et recherchez __Azure Data Factory__. Sélectionnez __Créer une exécution du pipeline__. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Connectez-vous et spécifiez le pipeline Azure Data Factory publié à exécuter.

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Enregistrez et créez l’application logique à l’aide du bouton **Enregistrer** en haut à gauche de la page. Pour afficher votre application, accédez à votre espace de travail dans le [Portail Azure](https://portal.azure.com) et cliquez sur **Événements**.

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Désormais, le pipeline Data Factory est déclenché lorsque la dérive se produit. Affichez les détails de l’exécution de la dérive de données et du pipeline d’apprentissage automatique sur le [nouveau portail d’espace de travail](https://ml.azure.com). 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Utiliser Azure Functions pour déployer un modèle basé sur des balises

Un objet de modèle Azure Machine Learning contient des paramètres sur lesquels vous pouvez baser les déploiements, tels que le nom du modèle, la version, la balise et la propriété. L’événement d’inscription du modèle peut déclencher un point de terminaison et vous pouvez utiliser une fonction Azure pour déployer un modèle en fonction de la valeur de ces paramètres.

Pour obtenir un exemple, consultez le référentiel [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) et suivez les étapes dans le fichier **readme**.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les événements disponibles, consultez le [schéma d’événement Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
