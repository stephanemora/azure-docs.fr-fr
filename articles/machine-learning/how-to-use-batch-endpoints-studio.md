---
title: Comment utiliser des points de terminaison de traitement par lots dans Studio
titleSuffix: Azure Machine Learning
description: Cet article explique comment créer un point de terminaison de traitement par lots dans Azure Machine Learning Studio. Des points de terminaison de traitement par lots sont utilisés pour effectuer en continu un scoring par lots de données volumineuses.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
ms.date: 08/16/2021
ms.custom: how-to, studio, managed-batch-endpoints
ms.openlocfilehash: f5f5d71b22c505580ed4e6b339f5b9c30e19eb89
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773060"
---
# <a name="how-to-use-batch-endpoints-preview-in-azure-machine-learning-studio"></a>Comment utiliser des points de terminaison de traitement par lots (préversion) dans Azure Machine Learning Studio

Dans cet article, vous allez apprendre à utiliser des points de terminaison de traitement de lots (préversion) pour effectuer un scoring par lots dans [Azure Machine Learning Studio](https://ml.azure.com). Pour plus d’informations, consultez [Que sont les points de terminaison Azure Machine Learning (préversion) ?](concept-endpoints.md).

Cet article porte sur les points suivants :

> [!div class="checklist"]
> * Créer un point de terminaison de traitement de lots avec une expérience sans code pour le modèle MLflow
> * Vérifier les détails d’un point de terminaison de traitement de lots
> * Démarrer un travail de scoring par lots
> * Présentation des fonctionnalités de point de terminaison de traitement par lots dans Azure Machine Learning Studio

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure – Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* L’exemple de référentiel – Clonez l’[exemple de référentiel AzureML](https://github.com/Azure/azureml-examples). Cet article utilise les ressources figurant dans `/cli/endpoints/batch`.

* Une cible de calcul dans laquelle vous pouvez effectuer un scoring par lots des flux de travail (workflows). Pour plus d’informations sur la création d’une cible de calcul, consultez [Créer des cibles de calcul dans Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).

* Inscrivez un modèle Machine Learning.

## <a name="create-a-batch-endpoint"></a>Créer un point de terminaison de traitement de lots

Il existe deux façons de créer des points de terminaison de traitement par lots dans Azure Machine Learning Studio :

* Dans la page **Endpoints** (Points de terminaison), sélectionnez **Batch Endpoints** (Points de terminaison de traitement par lots), puis **+ Create** (Créer). 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-endpoints.png" alt-text="Capture d’écran montrant la page de création d’un point de terminaison de traitement par lots/déploiement à partir de points de terminaison":::

OR

* Dans la page **Modèles** (Modèles) , sélectionnez le modèle que vous souhaitez déployer, puis choisissez **Deploy to batch endpoint (preview)** (Déployer sur un point de terminaison de traitement par lots (préversion)).

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/models-page-deployment.png" alt-text="Capture d’écran montrant la page de création d’un point de terminaison de traitement par lots/déploiement à partir de modèles":::

> [!TIP]
> Si vous utilisez un modèle MLflow, vous pouvez utiliser la création d’un point de terminaison de traitement de lots sans code. Autrement dit, vous n’avez pas besoin de préparer un script et un environnement de scoring, les deux pouvant être générés automatiquement. Pour plus d’informations, consultez [Entraîner et suivre des modèles ML avec MLflow et Azure Machine Learning (préversion)](how-to-use-mlflow.md).
> 
> :::image type="content" source="media/how-to-use-batch-endpoints-studio/mlflow-model-wizard.png" alt-text="Capture d’écran montrant le déploiement d’un modèle MLflow":::

Suivez toutes les étapes de l’Assistant pour créer un point de terminaison de traitement par lots et un déploiement.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/review-batch-wizard.png" alt-text="Capture d’écran montrant la révision des points de terminaison de traitement par lots/du déploiement":::

## <a name="check-batch-endpoint-details"></a>Vérifier les détails d’un point de terminaison de traitement de lots

Une fois un point de terminaison de traitement par lots créé, sélectionnez-le dans la page **Endpoints** (Points de terminaison) pour afficher les détails.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/batch-endpoint-details.png" alt-text="Capture d’écran montrant la vérification des détails des points de terminaison de traitement par lots et du déploiement":::

## <a name="start-a-batch-scoring-job"></a>Démarrer un travail de scoring par lots

Une charge de travail de scoring par lots s’exécute en tant que travail hors connexion. Par défaut, le scoring par lots stocke les sorties de scoring dans le stockage Blob. Vous pouvez également configurer l’emplacement des sorties et remplacer certains paramètres pour obtenir les performances optimales.

1. Sélectionnez **+ Create job** (Créer un travail) :

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-job.png" alt-text="Capture d’écran montrant l’option de création d’un travail pour démarrer un scoring par lots":::

1. Vous pouvez mettre à jour le déploiement par défaut lors de l’envoi d’un travail à partir de la liste déroulante :

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/job-setting-batch-scoring.png" alt-text="Capture d’écran montrant l’utilisation du déploiement pour envoyer un travail de traitement par lots":::

### <a name="overwrite-settings"></a>Remplacer les paramètres

Vous pouvez modifier certains paramètres lorsque vous démarrez un travail de scoring par lots. Par exemple, vous pouvez modifier des paramètres pour mieux utiliser la ressource de calcul ou pour améliorer les performances. Pour modifier les paramètres, sélectionnez __Override deployment settings__ (Remplacer les paramètres de déploiement), puis fournissez les paramètres. Pour plus d’informations, consultez [Utiliser des points de terminaison de traitement de lots](how-to-use-batch-endpoint.md#overwrite-settings).

:::image type="content" source="media/how-to-use-batch-endpoints-studio/overwrite-setting.png" alt-text="Capture d’écran montrant la modification de paramètres lors du démarrage d’un traitement par lots":::

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>Démarrer un travail de scoring par lots avec différentes options d’entrée

Vous avez le choix entre deux options pour spécifier les entrées de données dans Azure Machine Learning Studio :

* Utilisez un **jeu de données inscrit** :

    > [!NOTE]
    > Pendant la préversion, seul FileDataset est pris en charge. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-dataset-for-job.png" alt-text="Capture d’écran montrant la sélection d’un jeu de données inscrit comme option d’entrée":::

OR

* Utilisez un **magasin de données** :

    Vous pouvez spécifier magasin de données inscrit AML ou, si vos données sont disponibles publiquement, spécifiez le chemin d’accès public.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-datastore-job.png" alt-text="Capture d’écran montrant la sélection d’un magasin de données comme option d’entrée":::

### <a name="configure-the-output-location"></a>Configurer l’emplacement de sortie

Par défaut, les résultats du scoring par lots sont stockés dans le magasin d’objets blob par défaut pour l’espace de travail. Les résultats se trouvent dans un dossier nommé d’après le nom du travail (GUID généré par le système).

Pour modifier l’emplacement de stockage des résultats, fournissez un magasin d’objets blob et un chemin de sortie lorsque vous démarrez un travail.

> [!IMPORTANT]
> Vous devez utiliser un emplacement de sortie unique. Si le fichier de sortie existe, le travail de scoring par lots échouera. 

:::image type="content" source="media/how-to-use-batch-endpoints-studio/configure-output-location.png" alt-text="Capture d’écran montrant la configuration facultative d’un emplacement de sortie":::

### <a name="summary-of-all-submitted-jobs"></a>Résumé de tous les travaux envoyés

Pour afficher un résumé de tous les travaux envoyés pour un point de terminaison, sélectionnez celui-ci, puis choisissez l’onglet **Runs** (Exécutions).

:::image type="content" source="media/how-to-use-batch-endpoints-studio/summary-jobs.png" alt-text="Capture d’écran montrant un résumé des travaux soumis à un point de terminaison de traitement par lots":::
## <a name="check-batch-scoring-results"></a>Vérifier les résultats du scoring par lots

Pour savoir comment afficher les résultats du scoring, consultez [Utiliser des points de terminaison de traitement de lots](how-to-use-batch-endpoint.md#check-batch-scoring-results).

## <a name="add-a-deployment-to-an-existing-batch-endpoint"></a>Ajouter un déploiement à un point de terminaison de traitement de lots

Azure Machine Learning Studio permet d’ajouter un déploiement à un point de terminaison de traitement par lots de deux façons :

* Dans la page **Endpoints** (Points de terminaison), sélectionnez le point de terminaison de traitement par lots auquel ajouter un nouveau déploiement. Sélectionnez **+ Add deployment** (+ Ajouter un déploiement), puis suivez l’Assistant pour ajouter un nouveau déploiement.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-option.png" alt-text="Capture d’écran montrant l’option d’ajout d’un nouveau déploiement":::

OR

* Dans la page **Models** (Modèles), sélectionnez le modèle que vous voulez déployer. Ensuite, dans la liste déroulante, sélectionnez l’option **Deploy to batch endpoint (preview)** (Déployer vers le point de terminaison de traitement par lots (préversion)). Dans l’Assistant, sur l’écran **Endpoint** (Point de terminaison), sélectionnez **Existing** (Existant). Suivez l’Assistant pour ajouter le nouveau déploiement.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-models-page.png" alt-text="Capture d’écran montrant la sélection d’un point de terminaison de traitement par lots existant pour ajouter un nouveau déploiement":::

## <a name="update-the-default-deployment"></a>Mettre à jour le déploiement par défaut

Si un point de terminaison a plusieurs déploiements, l’un d’eux est le déploiement *par défaut*. Le déploiement par défaut reçoit 100 % du trafic vers le point de terminaison. Pour modifier le déploiement par défaut, procédez comme suit :

1. Sélectionnez le point de terminaison dans la page **Endpoints** (Points de terminaison).
1. Sélectionnez **Update default deployment** (Mettre à jour le déploiement par défaut). Sous l’onglet **Details** (Détails), sélectionnez le déploiement que vous souhaitez définir comme déploiement par défaut, puis choisissez **Update** (Mettre à jour).
    :::image type="content" source="media/how-to-use-batch-endpoints-studio/update-default-deployment.png" alt-text="Capture d’écran montrant la mise à jour du déploiement par défaut":::

## <a name="delete-batch-endpoint-and-deployments"></a>Supprimer le point de terminaison de traitement par lots et les déploiements

Pour supprimer un **point de terminaison**, sélectionnez-le dans la page **Endpoints** (Points de terminaison), puis choisissez Delete (Supprimer).

> [!WARNING]
> La suppression d’un point de terminaison a également pour effet de supprimer tous les déploiements vers celui-ci.

Pour supprimer un **déploiement**, sélectionnez le point de terminaison dans la page **Endpoints** (Points de terminaison), sélectionnez le déploiement, puis choisissez Delete (Supprimer).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer et à appeler des points de terminaison de traitement par lots. Pour en savoir plus sur Azure Machine Learning, consultez les articles suivants :

* [Résolution des problèmes de points de terminaison de traitement de lots](how-to-troubleshoot-batch-endpoints.md)
* [Déployer et scorer un modèle de machine learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)
