---
title: 'Didacticiel : Déployer un modèle Machine Learning avec l’interface visuelle'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer une solution d’analyse prédictive dans l’interface visuelle d’Azure Machine Learning. Entraînez, évaluez et déployez un modèle Machine Learning à l’aide de modules de type glisser-déplacer. Ce tutoriel constitue la deuxième partie d’une série en deux volets sur la prédiction des prix de véhicules automobiles au moyen d’une régression linéaire.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 22d5c41e8b815fd99450962cb63d11e9560c787f
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997010"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Didacticiel : Déployer un modèle Machine Learning avec l’interface visuelle

Pour que d’autres personnes puissent utiliser le modèle prédictif développé dans la [première partie de ce tutoriel](ui-tutorial-automobile-price-train-score.md), vous pouvez le déployer en tant que service web Azure. Jusqu’à présent, vous avez expérimenté l’entraînement de votre modèle. À présent, il est temps de générer de nouvelles prédictions basées sur des entrées utilisateur. Dans cette partie du tutoriel, vous allez effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer un modèle pour le déploiement
> * Déployer un service web
> * Tester un service web
> * Gérer un service web
> * Consommer le services web

## <a name="prerequisites"></a>Prérequis

Effectuez la [première partie du tutoriel](ui-tutorial-automobile-price-train-score.md) pour découvrir comment entraîner et évaluer un modèle Machine Learning dans l’interface visuelle.

## <a name="prepare-for-deployment"></a>Préparation du déploiement

Avant de déployer votre expérience en tant que service web, vous devez d’abord convertir votre *expérience d’entraînement* en *expérience prédictive*.

1. Sélectionnez **Créer une expérience prédictive*** dans la partie inférieure du canevas d’expérience.

    ![Image GIF animée montrant la conversion automatique d’une expérience d’entraînement en expérience prédictive](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Quand vous sélectionnez **Créer une expérience prédictive**, plusieurs choses se produisent :
    
    * Le modèle entraîné est stocké sous la forme d’un module **Modèle entraîné** dans la palette de modules. Vous pouvez le trouver sous **Modèles entraînés**.
    * Les modules qui ont été utilisés pour l’apprentissage sont supprimés :
      * Entraîner le modèle
      * Fractionner les données
      * Évaluer le modèle
    * Le modèle formé enregistré est rajouté à l’expérience.
    * Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés. Ces modules identifient où les données utilisateur sont introduites dans le modèle, et où les données sont retournées.

    L’**expérience d’entraînement** est toujours enregistrée sous les nouveaux onglets en haut du canevas d’expérience.

1. **Exécutez** l’expérience.

1. Sélectionnez la sortie du module **Scorer le modèle** et sélectionnez **Afficher les résultats** pour vérifier que le modèle fonctionne toujours. Vous pouvez voir que les données d’origine sont affichées, ainsi que le prix prédit (« Étiquettes scorées »).

Votre expérience doit désormais ressembler à cela :  

![Capture d’écran montrant la configuration attendue de l’expérience après sa préparation pour le déploiement](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Déploiement du service web

1. Sélectionnez **Déployer un service web** sous la zone de dessin.

1. Sélectionnez la **cible de calcul** que vous souhaitez utiliser pour exécuter votre service web.

    Actuellement, l’interface visuelle prend uniquement en charge le déploiement vers les cibles de calcul Azure Kubernetes Service (AKS). Vous pouvez choisir des cibles de calcul AKS disponibles dans votre espace de travail de service de machine learning ou configurer un nouvel environnement AKS à l’aide de la procédure présentée dans la boîte de dialogue qui s’affiche.

    ![Capture d’écran montrant une configuration possible pour une nouvelle cible de calcul](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Sélectionnez **Déployer un service web**. Vous verrez la notification suivante une fois le déploiement terminé. Le déploiement peut prendre quelques minutes.

    ![Capture d’écran montrant le message de confirmation pour un déploiement réussi](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test du service web

Vous pouvez tester et gérer vos services web d’interface visuelle en accédant à l’onglet **Services web**.

1. Accédez à la section relative au service web. Vous verrez le service web que vous avez déployé avec le nom **Tutorial - Predict Automobile Price[Predictive Exp]** (Tutoriel - Prédire le prix des véhicules automobiles [Exp. prédictive]).

     ![Capture d’écran montrant l’onglet du service web avec le service web récemment créé mis en évidence](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Sélectionnez le nom du service web pour voir des détails supplémentaires.

1. Sélectionnez **Test**.

    [![Capture d’écran montrant la page de test du service web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Entrez des données de test ou utilisez les exemples de données automatiquement renseignés et sélectionnez **Test**.

    La demande de test est envoyée au service web et les résultats sont affichés sur la page. Même si une valeur de prix est générée pour les données d’entrée, celle-ci n’est pas utilisée pour générer la valeur de prédiction.

## <a name="consume-the-web-service"></a>Consommer le services web

Les utilisateurs peuvent désormais envoyer des requêtes d’API à votre service web Azure et recevoir des résultats afin de prédire le prix de leurs nouvelles automobiles.

**Demande/Réponse** : l’utilisateur envoie une ou plusieurs lignes de données automobiles au service à l’aide d’un protocole HTTP. Le service répond avec un ou plusieurs jeux de résultats.

Vous trouverez des exemples d’appels REST sous l’onglet **Consommer** de la page de détails du service web.

   ![Capture d’écran montrant un exemple d’appel REST que les utilisateurs peuvent trouver sous l’onglet Consommer](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Accédez à l’onglet **API Doc** pour plus de détails d’API.

## <a name="manage-models-and-deployments"></a>Gérer les modèles et les déploiements

Les modèles et les déploiements de services web que vous créez dans l’interface visuelle peuvent également être gérés à partir de l’espace de travail Azure Machine Learning.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  

1. Dans votre espace de travail, sélectionnez **Modèles**. Sélectionnez ensuite l’expérience que vous avez créée.

    ![Capture d’écran montrant comment accéder à des expériences dans le portail Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Dans cette page, vous verrez des détails supplémentaires sur le modèle.

1. Sélectionnez **Déploiements** pour lister tous les services web qui utilisent ce modèle. Sélectionnez le nom du service web pour accéder à la page de détails du service web. Dans cette page, vous pouvez obtenir des informations détaillées sur le service web.

    [![Capture d’écran d’un rapport d’exécution détaillé](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

Vous trouverez également ces modèles et déploiements dans les sections **Modèles** et **Points de terminaison** de la [page d’accueil de votre espace de travail (préversion)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les étapes clés de la création, du déploiement et de la consommation d’un modèle Machine Learning dans l’interface visuelle. Pour en savoir plus sur la façon dont vous pouvez utiliser l’interface visuelle pour résoudre d’autres types de problèmes, consultez nos autres exemples d’expériences.

> [!div class="nextstepaction"]
> [Exemple de classification de risque de crédit](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
