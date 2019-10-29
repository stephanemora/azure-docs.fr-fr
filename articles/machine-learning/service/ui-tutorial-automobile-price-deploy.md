---
title: 'Didacticiel : Déployer un modèle Machine Learning avec l’interface visuelle'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer une solution d’analytique prédictive dans l’interface visuelle d’Azure Machine Learning. Entraînez, évaluez et déployez un modèle Machine Learning à l’aide de modules de type glisser-déplacer.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792672"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Didacticiel : Déployer un modèle Machine Learning avec l’interface visuelle

Pour que d’autres personnes puissent utiliser le modèle prédictif développé dans la [première partie de ce tutoriel](ui-tutorial-automobile-price-train-score.md), vous pouvez le déployer en tant que point de terminaison en temps réel. Dans la partie 1, vous avez entraîné votre modèle. À présent, il est temps de générer de nouvelles prédictions basées sur des entrées utilisateur. Dans cette partie du tutoriel, vous allez effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer un point de terminaison en temps réel
> * Créer un cluster d’inférence
> * Tester un point de terminaison en temps réel

## <a name="prerequisites"></a>Prérequis

Effectuez la [première partie du tutoriel](ui-tutorial-automobile-price-train-score.md) pour découvrir comment entraîner et évaluer un modèle Machine Learning dans l’interface visuelle.

## <a name="deploy-a-real-time-endpoint"></a>Déployer un point de terminaison en temps réel

Pour déployer votre pipeline, vous devez :

1. Convertir le pipeline d’entraînement en un pipeline d’inférence en temps réel, qui supprime les modules d’entraînement, et ajoute des entrées et des sorties pour les demandes d’inférence.
1. Déployer le pipeline d’inférence.

### <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

1. En haut du canevas de pipeline, sélectionnez **Créer un pipeline d’inférence** > **Pipeline d’inférence en temps réel**

    Quand vous sélectionnez **Créer un pipeline d’inférence**, plusieurs choses se produisent :
    
    * Le modèle entraîné est stocké sous la forme d’un module **Jeux de données** dans la palette de modules. Vous pouvez le trouver sous **Mes modèles**.
    * Des modules, comme **Entraîner le modèle** et **Fractionner les données**, qui ont été utilisés pour l’entraînement sont supprimés.
    * Le modèle entraîné enregistré est rajouté au pipeline.
    * Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés. Ces modules identifient l’endroit où les données utilisateur sont entrées dans le modèle et où les données sont retournées.

    > [!Note]
    > Le **pipeline d’entraînement** est enregistré sous le nouvel onglet situé en haut du canevas du pipeline. Vous le trouvez également en tant que pipeline publié dans l’interface visuelle.
    >

    Votre pipeline doit maintenant se présenter comme suit :  

   ![Capture d’écran montrant la configuration attendue du pipeline après sa préparation pour le déploiement](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. Sélectionnez **Exécuter**, et utilisez la même cible de calcul et la même expérience que ce que vous avez utilisé dans la partie 1.

1. Sélectionnez le module **Scorer le modèle**.

1. Dans le volet Propriétés, sélectionnez **Sorties** > **Visualiser** pour vérifier que le modèle fonctionne toujours. Vous pouvez voir que les données d’origine sont affichées ainsi que le prix prédit (« Étiquettes scorées »).

1. Sélectionnez **Déployer**.

### <a name="create-an-inferencing-cluster"></a>Créer un cluster d’inférence

Dans la boîte de dialogue qui apparaît, vous pouvez sélectionner parmi les clusters Azure Kubernetes Service (AKS) existants dans votre espace de travail pour déployer votre modèle. Si vous n’avez pas de cluster AKS, utilisez les étapes suivantes pour en créer un.

1. Sélectionnez **Calcul** dans la boîte de dialogue pour accéder à la page **Calcul**.

1. Dans le ruban de navigation, sélectionnez **Clusters d’inférence** >  **+ Nouveau**.

    ![Capture d’écran montrant comment accéder au volet Nouveau cluster d’inférence](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Dans le volet Cluster d’inférence, configurez un nouveau service Kubernetes.

1. Entrez « aks-compute » pour **Nom de la capacité de calcul**.
    
1. Sélectionnez une **région** proche disponible.

1. Sélectionnez **Create** (Créer).

    > [!Note]
    > La création d’un service AKS prend environ 15 minutes. Vous pouvez vérifier l’état de provisionnement dans la page **Clusters d’inférence**
    >

### <a name="deploy-the-real-time-endpoint"></a>Déployer le point de terminaison en temps réel

Une fois le provisionnement du service AKS terminé, revenez au pipeline d’inférence en temps réel pour terminer le déploiement.

1. Sélectionnez **Déployer** au-dessus du canevas.

1. Sélectionnez **Déployer un nouveau point de terminaison en temps réel**. 

1. Sélectionnez le cluster AKS que vous avez créé.

1. Sélectionnez **Déployer**.

    ![Capture d’écran montrant comment configurer un nouveau point de terminaison en temps réel](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Une notification de réussite apparaît au-dessus du canevas une fois le déploiement terminé, ce qui peut prendre quelques minutes.

## <a name="test-the-real-time-endpoint"></a>Tester le point de terminaison en temps réel

Vous pouvez tester votre point de terminaison en temps réel en accédant à la page **Points de terminaison** dans le volet de navigation de l’espace de travail sur la gauche.

1. Dans la page **Points de terminaison**, sélectionnez le point de terminaison que vous avez déployé.

    ![Capture d’écran montrant l’onglet des points de terminaison en temps réel avec le point de terminaison créé récemment mis en surbrillance](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Sélectionnez **Test**.

1. Entrez des données de test ou utilisez les exemples de données automatiquement renseignés et sélectionnez **Test**.

    La demande de test est envoyée au point de terminaison et les résultats sont affichés sur la page. Même si une valeur de prix est générée pour les données d’entrée, celle-ci n’est pas utilisée pour générer la valeur de prédiction.

    ![Capture d’écran montrant comment tester le point de terminaison en temps réel avec l’étiquette scorée pour le prix mise en surbrillance](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les étapes clés de la création, du déploiement et de la consommation d’un modèle Machine Learning dans l’interface visuelle. Pour plus d’informations sur la façon dont vous pouvez utiliser l’interface visuelle pour résoudre d’autres types de problèmes, consultez nos autres exemples de pipelines.

> [!div class="nextstepaction"]
> [Exemple de classification de risque de crédit](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
