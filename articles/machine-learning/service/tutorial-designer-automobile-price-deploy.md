---
title: 'Didacticiel : Déployer un modèle Machine Learning avec le concepteur'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer une solution d’analytique prédictive dans le concepteur Azure Machine Learning (préversion). Entraînez, évaluez et déployez un modèle Machine Learning à l’aide de modules de type glisser-déplacer.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b5fa1557999ae851bccafbf8ee7c41f0b3614614
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715918"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Didacticiel : Déployer un modèle Machine Learning avec le concepteur (préversion)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Vous pouvez déployer le modèle prédictif développé dans la [première partie de ce tutoriel](tutorial-designer-automobile-price-train-score.md) pour donner l’occasion à d’autres personnes de l’utiliser. Dans la partie 1, vous avez entraîné votre modèle. À présent, il est temps de générer de nouvelles prédictions basées sur des entrées utilisateur. Dans cette partie du didacticiel, vous allez :

> [!div class="checklist"]
> * Créer un pipeline d’inférence en temps réel
> * Créer un cluster d’inférence
> * Déployer un point de terminaison en temps réel
> * Tester un point de terminaison en temps réel

## <a name="prerequisites"></a>Prérequis

Suivez la [première partie du tutoriel](tutorial-designer-automobile-price-train-score.md) pour découvrir comment entraîner et évaluer un modèle Machine Learning dans le concepteur.

## <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

Pour déployer votre pipeline, vous devez d’abord convertir le pipeline d’entraînement en pipeline d’inférence en temps réel. Ce processus supprime les modules d’entraînement et ajoute les entrées et sorties pour les requêtes d’inférence.

### <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

1. En haut du canevas de pipeline, sélectionnez **Create inference pipeline** (Créer un pipeline d’inférence) > **Real-time inference pipeline (Pipeline d’inférence en temps réel)**

    Votre pipeline doit maintenant se présenter comme suit :  

   ![Capture d’écran montrant la configuration attendue du pipeline après sa préparation pour le déploiement](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    Quand vous sélectionnez **Créer un pipeline d’inférence**, plusieurs choses se produisent :
    
    * Le modèle entraîné est stocké sous la forme d’un module **Jeux de données** dans la palette de modules. Vous pouvez le trouver sous **Mes modèles**.
    * Des modules d’entraînement, comme **Train Model** (Entraîner le modèle) et **Split Data** (Fractionner les données) sont supprimés.
    * Le modèle entraîné enregistré est rajouté au pipeline.
    * Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés. Ces modules montrent l’endroit où les données utilisateur sont entrées dans le modèle, et où les données sont retournées.

    > [!Note]
    > Le **pipeline d’entraînement** est enregistré sous le nouvel onglet situé en haut du canevas du pipeline. Vous le trouvez également en tant que pipeline publié dans le concepteur.
    >

1. Sélectionnez **Exécuter**, et utilisez la même cible de calcul et la même expérience que ce que vous avez utilisé dans la partie 1.

1. Sélectionnez le module **Scorer le modèle**.

1. Dans le volet Propriétés, sélectionnez **Sorties** > **Visualiser** pour vérifier que le modèle fonctionne toujours. Vous pouvez voir que les données d’origine sont affichées ainsi que le prix prédit (« Étiquettes scorées »).

1. Sélectionnez **Déployer**.

## <a name="create-an-inferencing-cluster"></a>Créer un cluster d’inférence

Dans la boîte de dialogue qui apparaît, vous pouvez sélectionner n’importe quel cluster Azure Kubernetes Service (AKS) existant dans lequel déployer votre modèle. Si vous n’avez pas de cluster AKS, utilisez les étapes suivantes pour en créer un.

1. Sélectionnez **Capacité de calcul** dans la boîte de dialogue qui s’affiche pour accéder à la page **Capacité de calcul**.

1. Dans le ruban de navigation, sélectionnez **Clusters d’inférence** >  **+ Nouveau**.

    ![Capture d’écran montrant comment accéder au volet Nouveau cluster d’inférence](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Dans le volet Cluster d’inférence, configurez un nouveau service Kubernetes.

1. Entrez « aks-compute » pour **Nom de la capacité de calcul**.
    
1. Sélectionnez une **région** proche disponible.

1. Sélectionnez **Create** (Créer).

    > [!Note]
    > La création d’un service AKS prend environ 15 minutes. Vous pouvez vérifier l’état de provisionnement dans la page **Clusters d’inférence**
    >

## <a name="deploy-the-real-time-endpoint"></a>Déployer le point de terminaison en temps réel

Une fois le provisionnement du service AKS terminé, revenez au pipeline d’inférence en temps réel pour terminer le déploiement.

1. Sélectionnez **Déployer** au-dessus du canevas.

1. Sélectionnez **Déployer un nouveau point de terminaison en temps réel**. 

1. Sélectionnez le cluster AKS que vous avez créé.

1. Sélectionnez **Déployer**.

    ![Capture d’écran montrant comment configurer un nouveau point de terminaison en temps réel](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Une notification de réussite apparaît au-dessus du canevas une fois le déploiement terminé, ce qui peut prendre quelques minutes.

## <a name="test-the-real-time-endpoint"></a>Tester le point de terminaison en temps réel

À l’issue du déploiement, vous pouvez tester votre point de terminaison en temps réel en accédant à la page **Points de terminaison**.

1. Dans la page **Points de terminaison**, sélectionnez le point de terminaison que vous avez déployé.

    ![Capture d’écran montrant l’onglet des points de terminaison en temps réel avec le point de terminaison créé récemment mis en surbrillance](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Sélectionnez **Test**.

1. Vous pouvez entrer des données de test manuellement ou utiliser les exemples de données automatiquement renseignés, puis sélectionnez **Test** (Tester).

    Le portail soumet une requête de test au point de terminaison et affiche les résultats. Même si une valeur de prix est générée pour les données d’entrée, celle-ci n’est pas utilisée pour générer la valeur de prédiction.

    ![Capture d’écran montrant comment tester le point de terminaison en temps réel avec l’étiquette scorée pour le prix mise en surbrillance](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les étapes clés de la création, du déploiement et de la consommation d’un modèle Machine Learning dans le concepteur. Pour plus d’informations sur la façon dont vous pouvez utiliser le concepteur pour résoudre d’autres types de problèmes, consultez nos autres exemples de pipelines.

> [!div class="nextstepaction"]
> [Exemple de classification de risque de crédit](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
