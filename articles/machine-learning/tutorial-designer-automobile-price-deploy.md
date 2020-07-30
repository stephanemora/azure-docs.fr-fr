---
title: 'Tutoriel : Déployer des modèles Machine Learning avec le concepteur (préversion)'
titleSuffix: Azure Machine Learning
description: Ce tutoriel vous montre comment créer une solution d’analyse prédictive dans le concepteur Azure Machine Learning (préversion). Entraînez, évaluez à l’aide d’un score et déployez un modèle Machine Learning à l’aide de modules fonctionnant par glisser-déposer.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: 453971d776a0953a344d147bca387a81f65ac73c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287980"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Tutoriel : Déployer un modèle Machine Learning avec le concepteur (préversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Vous pouvez déployer le modèle prédictif développé dans la [première partie de ce tutoriel](tutorial-designer-automobile-price-train-score.md) pour donner l’occasion à d’autres personnes de l’utiliser. Dans la première partie, vous avez entraîné votre modèle. À présent, il est temps de générer de nouvelles prédictions basées sur des entrées utilisateur. Dans cette partie du didacticiel, vous allez :

> [!div class="checklist"]
> * Créez un pipeline d’inférence en temps réel.
> * Créez un cluster d’inférence.
> * Déployez le point de terminaison en temps réel.
> * Testez le point de terminaison en temps réel.

## <a name="prerequisites"></a>Prérequis

Suivez la [première partie du tutoriel](tutorial-designer-automobile-price-train-score.md) pour découvrir comment entraîner et évaluer un modèle Machine Learning dans le concepteur.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

Pour déployer votre pipeline, vous devez d’abord convertir le pipeline d’entraînement en pipeline d’inférence en temps réel. Ce processus supprime les modules d’entraînement et ajoute les entrées et sorties de service web pour gérer les demandes.

### <a name="create-a-real-time-inference-pipeline"></a>Créer un pipeline d’inférence en temps réel

1. Au-dessus du canevas de pipeline, sélectionnez **Create inference pipeline** > **Real-time inference pipeline** (Créer un pipeline d’inférence > Pipeline d’inférence en temps réel).

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Capture d’écran montrant l’emplacement du bouton pour créer un pipeline":::

    Votre pipeline doit maintenant se présenter comme suit : 

   ![Capture d’écran montrant la configuration attendue du pipeline après sa préparation pour le déploiement](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quand vous sélectionnez **Créer un pipeline d’inférence**, plusieurs choses se produisent :
    
    * Le modèle entraîné est stocké sous la forme d’un module **Jeux de données** dans la palette de modules. Vous pouvez le trouver sous **Mes modèles**.
    * Des modules d’entraînement, comme **Train Model** (Entraîner le modèle) et **Split Data** (Fractionner les données) sont supprimés.
    * Le modèle entraîné enregistré est rajouté au pipeline.
    * Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés. Ces modules montrent l’emplacement où les données utilisateur sont entrées dans le pipeline ainsi que l’emplacement où ces données sont retournées.

    > [!NOTE]
    > Par défaut, l’**entrée de service web** attend le même schéma de données que les données d’entraînement utilisées pour créer le pipeline prédictif. Dans ce scénario, le prix est inclus dans le schéma. Toutefois, le prix n’est pas utilisé comme facteur lors de la prédiction.
    >

1. Sélectionnez **Envoyer**, puis utilisez la même cible de calcul et la même expérience que durant la première partie.

    La première fois, l’exécution de votre pipeline peut prendre jusqu’à 20 minutes. Les paramètres de calcul par défaut ont une taille de nœud minimale de 0, ce qui signifie que le concepteur doit allouer des ressources après une période d’inactivité. Les exécutions de pipeline répétées prennent moins de temps dans la mesure où les ressources de calcul sont déjà allouées. Par ailleurs, le concepteur utilise les résultats mis en cache pour chaque module afin d’améliorer l’efficacité.

1. Sélectionnez **Déployer**.

## <a name="create-an-inferencing-cluster"></a>Créer un cluster d’inférence

Dans la boîte de dialogue qui s’affiche, vous pouvez sélectionner n’importe quel cluster AKS (Azure Kubernetes Service) existant sur lequel déployer votre modèle. Si vous n’avez pas de cluster AKS, utilisez les étapes suivantes pour en créer un.

1. Dans la boîte de dialogue qui s’affiche, sélectionnez **Calculer** pour accéder à la page **Calculer**.

1. Dans le ruban de navigation, sélectionnez **Clusters d’inférence** >  **+ Nouveau**.

    ![Capture d’écran montrant comment accéder au volet Nouveau cluster d’inférence](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Dans le volet Cluster d’inférence, configurez un nouveau service Kubernetes.

1. Entrez *aks-compute* pour **Nom du calcul**.
    
1. Sélectionnez une région proche disponible pour **Région**.

1. Sélectionnez **Create** (Créer).

    > [!NOTE]
    > La création d’un service AKS prend environ 15 minutes. Vous pouvez vérifier l’état de provisionnement dans la page **Clusters d’inférence**.
    >

## <a name="deploy-the-real-time-endpoint"></a>Déployer le point de terminaison en temps réel

Une fois le provisionnement du service AKS terminé, revenez au pipeline d’inférence en temps réel pour terminer le déploiement.

1. Sélectionnez **Déployer** au-dessus du canevas.

1. Sélectionnez **Déployer un nouveau point de terminaison en temps réel**. 

1. Sélectionnez le cluster AKS que vous avez créé.

1. Sélectionnez **Déployer**.
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Capture d’écran montrant comment configurer un nouveau point de terminaison en temps réel":::

    Une notification de réussite s’affiche au-dessus du canevas, une fois le déploiement effectué. Cela peut prendre quelques minutes.

## <a name="test-the-real-time-endpoint"></a>Tester le point de terminaison en temps réel

Une fois le déploiement effectué, vous pouvez tester votre point de terminaison en temps réel en accédant à la page **Points de terminaison**.

1. Dans la page **Points de terminaison**, sélectionnez le point de terminaison que vous avez déployé.

    ![Capture d’écran montrant l’onglet des points de terminaison en temps réel avec le point de terminaison créé récemment mis en surbrillance](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Sélectionnez **Test**.

1. Vous pouvez entrer manuellement des données de test ou utiliser les exemples de données automatiquement renseignés, puis sélectionner **Test** (Tester).

    Le portail soumet une requête de test au point de terminaison et affiche les résultats. Bien qu’une valeur de prix soit générée pour les données d’entrée, elle n’est pas utilisée pour générer la valeur de prédiction.

    ![Capture d’écran montrant comment tester le point de terminaison en temps réel avec l’étiquette scorée pour le prix mise en surbrillance](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les étapes clés de la création, du déploiement et de la consommation d’un modèle Machine Learning dans le concepteur. Pour en savoir plus sur l’utilisation du concepteur pour résoudre d’autres types de problème, consultez nos autres exemples de pipelines.

> [!div class="nextstepaction"]
> [Exemples de concepteur](samples-designer.md)
