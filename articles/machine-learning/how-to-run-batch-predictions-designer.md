---
title: Exécuter des prédictions par lots à l’aide du concepteur Azure Machine Learning (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner un modèle et configurer un pipeline de prédiction par lots à l’aide du concepteur. Déployez le pipeline en tant que service web paramétrable, pouvant être déclenché à partir de n’importe quelle bibliothèque HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: 8d80282044adfa723940aa6f68efc1e719e713c0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75532052"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Exécuter des prédictions par lots à l’aide du concepteur Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cette procédure, vous allez apprendre à utiliser le concepteur pour entraîner un modèle, puis configurer un pipeline de prédiction par lots et un service web. La prédiction par lots permet un scoring continu et à la demande des modèles entraînés sur des jeux de données volumineux. Il est possible de la configurer comme un service web pouvant être déclenché à partir de n’importe quelle bibliothèque HTTP. 

Pour configurer les services de scoring par lots à l’aide du SDK, consultez la [procédure](how-to-run-batch-predictions.md) associée.

Dans cette procédure, vous allez apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer une expérience Machine Learning de base dans un pipeline
> * Créer un pipeline d’inférence par lots paramétrable
> * Gérer et exécuter des pipelines manuellement ou à partir d’un point de terminaison REST

## <a name="prerequisites"></a>Conditions préalables requises

1. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

1. Créez un [espace de travail](tutorial-1st-experiment-sdk-setup.md).

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/).

Ce guide suppose des connaissances de base relatives à la création d’un pipeline simple dans le concepteur. Pour une présentation guidée du concepteur, suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Créer un pipeline

Pour créer un pipeline d’inférence par lots, vous avez d’abord besoin d’une expérience Machine Learning. Pour en créer un, accédez à l’onglet **Designer** (Concepteur) dans votre espace de travail, puis créez un pipeline en sélectionnant l’option **Easy-to-use prebuilt modules** (Modules prégénérés faciles à utiliser).

![Page d’accueil du concepteur](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

Nous allons utiliser ce modèle de Machine Learning simple à des fins de démonstration. Les données proviennent d’un jeu de données inscrit, créé à partir des données sur le diabète provenant d’Azure Open Datasets. Pour savoir comment inscrire des jeux de données provenant d’Azure Open Datasets, consultez la [procédure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets). Ces données sont réparties entre des jeux de données servant à l’entraînement et d’autres servant à la validation. En outre, un arbre de décision optimisé est entraîné et son score est calculé. Le pipeline doit être exécuté au moins une fois pour créer un pipeline d’inférence. Cliquez sur le bouton **Run** (Exécuter) pour exécuter le pipeline.

![Créer une expérience simple](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Créer un pipeline d’inférence par lots

Maintenant que le pipeline a été exécuté, une nouvelle option nommée **Create inference pipeline** (Créer un pipeline d’inférence) est disponible en regard des options **Run** (Exécuter) et **Publish** (Publier). Cliquez sur la liste déroulante et sélectionnez **Batch inference pipeline** (Pipeline d’inférence par lots).

![Créer un pipeline d’inférence par lots](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

Le résultat est un pipeline d’inférence par lots par défaut. Celui-ci comprend un nœud pour la configuration d’expérience de pipeline d’origine, un nœud pour les données brutes destinées au scoring et un nœud pour calculer le score des données brutes par rapport au pipeline d’origine.

![Pipeline d’inférence par lots par défaut](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

Vous pouvez ajouter d’autres nœuds pour modifier le comportement du processus d’inférence par lots. Dans cet exemple, vous allez ajouter un nœud pour l’échantillonnage aléatoire à partir des données entrées avant le scoring. Créez un nœud **Partition and Sample** (Partition et exemple) et placez-le entre le nœud des données brutes et le nœud du scoring. Ensuite, cliquez sur le nœud **Partition and Sample** (Partition et exemple) pour accéder aux paramètres.

![Nouveau nœud](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

Le paramètre *Rate of sampling* (Taux d’échantillonnage) détermine le pourcentage du jeu de données d’origine qui doit être extrait pour l’échantillon aléatoire. Il s’agit d’un paramètre qu’il sera utile d’ajuster fréquemment. Vous devez donc l’activer en tant que paramètre de pipeline. Les paramètres de pipeline peuvent être modifiés au moment de l’exécution et peuvent être spécifiés dans un objet de charge utile lors de la réexécution du pipeline à partir d’un point de terminaison REST. 

Pour activer ce champ en tant que paramètre de pipeline, cliquez sur les trois points situés au-dessus du champ, puis cliquez sur **Add to pipeline parameter** (Ajouter au paramètre de pipeline). 

![Paramètres de l’exemple](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Ensuite, attribuez un nom et une valeur par défaut au paramètre. Le nom sera utilisé pour identifier le paramètre et le spécifier dans un appel REST.

![Paramètre de pipeline](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Déployer un pipeline d’inférence par lots

Vous êtes maintenant prêt à déployer le pipeline. Cliquez sur le bouton **Deploy** (Déployer) pour ouvrir l’interface permettant de configurer un point de terminaison. Cliquez sur la liste déroulante, puis sélectionnez **New PipelineEndpoint** (Nouveau point de terminaison de pipeline).

![Déploiement du pipeline](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Attribuez un nom et une description (facultative) au point de terminaison. Dans la partie inférieure, vous voyez le paramètre `sample-rate` que vous avez configuré avec une valeur par défaut de 0,8. Quand vous êtes prêt, cliquez sur **Deploy** (Déployer).

![Configurer le point de terminaison](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Gérer les points de terminaison 

Une fois le déploiement terminé, accédez à l’onglet **Endpoints** (Points de terminaison), puis cliquez sur le nom du point de terminaison que vous venez de créer.

![Lien du point de terminaison](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

Cet écran affiche tous les pipelines publiés sous le point de terminaison. Cliquez sur votre pipeline d’inférence.

![Pipeline d’inférence](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

La page Pipeline Details (Détails du pipeline) affiche l’historique détaillé des exécutions, ainsi que des informations sur les chaînes de connexion de votre pipeline. Cliquez sur le bouton **Run** (Exécuter) pour lancer une exécution manuelle du pipeline.

![Détails du pipeline](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

Dans la configuration de l’exécution, vous pouvez fournir une description de l’exécution et modifier la valeur de tous les paramètres de pipeline. Cette fois-ci, réexécutez le pipeline d’inférence avec un taux d’échantillonnage de 0,9. Cliquez sur **Run** pour exécuter le pipeline.

![Exécution du pipeline](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

L’onglet **Consume** (Consommation) contient le point de terminaison REST pour la réexécution de votre pipeline. Pour effectuer un appel REST, vous aurez besoin d’un en-tête d’authentification de type porteur OAuth 2.0. Pour plus d’informations sur la configuration de l’authentification dans votre espace de travail et sur l’exécution d’un appel REST paramétrable, consultez [ce tutoriel](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Étapes suivantes

Suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md) sur le concepteur pour entraîner et déployer un modèle de régression.
