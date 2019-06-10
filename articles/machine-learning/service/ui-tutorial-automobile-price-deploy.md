---
title: 'Didacticiel : Déployer un modèle Machine Learning avec l’interface visuelle'
titleSuffix: Azure Machine Learning service
description: Découvrez comment créer une solution d’analyse prédictive dans l’interface visuelle du service Azure Machine Learning. Entraînez, évaluez et déployez un modèle Machine Learning à l’aide de modules de type glisser-déplacer. Ce tutoriel constitue la deuxième partie d’une série en deux volets sur la prédiction des prix de véhicules automobiles au moyen d’une régression linéaire.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 8512ca2fe01c772d7e4c21a5cb09303b9804899c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389217"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Didacticiel : Déployer un modèle Machine Learning avec l’interface visuelle

Dans ce tutoriel, vous étudiez de manière approfondie le développement d’une solution prédictive dans l’interface visuelle du service Azure Machine Learning. Ce tutoriel est le **deuxième d’une série de deux**. Dans la [première partie de ce tutoriel](ui-tutorial-automobile-price-train-score.md), vous avez entraîné, scoré et évalué un modèle de prédiction de prix de voitures. Dans cette partie du tutoriel, vous allez effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer un modèle pour le déploiement
> * Déployer un service web
> * Tester un service web
> * Gérer un service web
> * Consommer le services web

## <a name="prerequisites"></a>Prérequis

Effectuez la [première partie du tutoriel](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Préparation du déploiement

Pour que d’autres personnes puissent utiliser le modèle prédictif développé dans ce tutoriel, vous pouvez le déployer en tant que service web Azure.

Jusqu’à présent, vous avez expérimenté l’entraînement de votre modèle. À présent, il est temps de générer de nouvelles prédictions basées sur des entrées utilisateur.

La préparation du déploiement est un processus en deux étapes :  

1. Conversion de l’*expérience d’entraînement* que vous avez créée en *expérience prédictive*
1. Déploiement de l’expérience prédictive sous la forme d’un service web

Vous pouvez d'abord copier l'expérience en sélectionnant **Enregistrer sous** dans la partie inférieure de la zone de dessin.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l'expérience de formation en expérience prédictive

Pour préparer ce modèle pour le déploiement, convertissez cette expérience d’entraînement en expérience prédictive. En règle générale, cela implique trois étapes :

1. Enregistrer le modèle que vous avez entraîné et remplacer vos modules d’entraînement
1. Réduire l’expérience en supprimant les modules uniquement nécessaires à l’apprentissage
1. Définir où le service web doit accepter les données d’entrée et où il doit générer la sortie

Vous pouvez effectuer ces étapes manuellement ou vous pouvez sélectionner **Configurer le service web** en bas de la zone de dessin pour qu’elles s’effectuent automatiquement.

![Image GIF animée montrant la conversion automatique d’une expérience d’entraînement en expérience prédictive](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Lorsque vous sélectionnez **Configurer le service web**, plusieurs choses se produisent :

* Le modèle entraîné est converti en un module **Modèle entraîné** unique. Il est stocké dans la palette des modules, à gauche de la zone de dessin. Vous pouvez le trouver sous **Modèles entraînés**.
* Les modules qui ont été utilisés pour l’apprentissage sont supprimés :
  * Entraîner le modèle
  * Fractionner les données
  * Évaluer le modèle
* Le modèle formé enregistré est rajouté à l’expérience.
* Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés. Ces modules identifient où les données de l’utilisateur sont introduites dans le modèle, et où les données sont retournées.

Vous constatez que l’expérience est enregistrée en deux parties, sous les nouveaux onglets en haut de la zone de dessin. L’expérience de formation d’origine se trouve sous l’onglet **Expérience de formation**, tandis que l’expérience prédictive tout juste créée est sous **Expérience prédictive**. L’expérience prédictive est celle que vous déployez sous la forme d’un service web.

Votre expérience doit désormais ressembler à cela :  

![Capture d’écran montrant la configuration attendue de l’expérience après sa préparation pour le déploiement](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Exécutez une dernière fois l’expérience (sélectionnez **Exécuter**). Dans la boîte de dialogue contextuelle, choisissez la cible de calcul sur laquelle vous voulez que l’expérience s’exécute. Pour vérifier que le modèle fonctionne toujours, sélectionnez la sortie du module Scorer le modèle et sélectionnez **Afficher les résultats**. Vous pouvez voir que les données d’origine sont affichées, ainsi que le prix prédit (« Étiquettes scorées »).

## <a name="deploy-the-web-service"></a>Déploiement du service web

Pour déployer un nouveau service web dérivé de votre expérience :

1. Sélectionnez **Déployer un service web** sous la zone de dessin.
1. Sélectionnez la **cible de calcul** que vous souhaitez utiliser pour exécuter votre service web.

    Actuellement, l’interface visuelle prend uniquement en charge le déploiement vers les cibles de calcul Azure Kubernetes Service (AKS). Vous pouvez choisir des cibles de calcul AKS disponibles dans votre espace de travail de service de machine learning ou configurer un nouvel environnement AKS à l’aide de la procédure présentée dans la boîte de dialogue qui s’affiche.

    ![Capture d’écran montrant une configuration possible pour une nouvelle cible de calcul](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Sélectionnez **Déployer un service web**. Vous verrez la notification suivante une fois le déploiement terminé. Le déploiement peut prendre quelques minutes.

    ![Capture d’écran montrant le message de confirmation pour un déploiement réussi](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Test du service web

Les données d’entrée utilisateur sont introduites dans votre modèle déployé via le module **Entrée du service web**. L’entrée est ensuite scorée dans le module **Scorer le modèle**. Selon la manière dont vous avez configuré l’expérience prédictive, le modèle attend des données du même format que le jeu de données initial des prix des véhicules automobiles. Enfin, les résultats sont retournés à l’utilisateur via le module **Sortie du service web**.

Vous pouvez tester un service web sous l’onglet du service web, dans l’interface visuelle.

1. Accédez à la section relative au service web. Vous verrez le service web que vous avez déployé avec le nom **Tutorial - Predict Automobile Price[Predictive Exp]** (Tutoriel - Prédire le prix des véhicules automobiles [Exp. prédictive]).

     ![Capture d’écran montrant l’onglet du service web avec le service web récemment créé mis en évidence](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Sélectionnez le nom du service web pour voir des détails supplémentaires.

     ![Capture d’écran montrant les détails supplémentaires disponibles dans la vue du service web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Sélectionnez **Test**.

    ![Capture d’écran montrant la page de test du service web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Entrez des données de test ou utilisez les exemples de données automatiquement renseignés et sélectionnez **Test** dans la partie inférieure. La demande de test est envoyée au service web et les résultats sont affichés sur la page. Même si une valeur de prix est générée pour les données d’entrée, celle-ci n’est pas utilisée pour générer la valeur de prédiction.

## <a name="manage-the-web-service"></a>Gérer le service web

Une fois que vous avez déployé votre service web, vous pouvez le gérer à partir de l’onglet **Services web** dans l’interface visuelle.

Vous pouvez supprimer un service web en sélectionnant **Supprimer** dans la page de détails du service web.

   ![Capture d’écran montrant l’emplacement du bouton Supprimer le service web en bas de la fenêtre](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Consommer le services web

Dans les étapes précédentes de ce tutoriel, vous avez déployé un modèle de prédiction automobile en tant que service web Azure. Désormais, les utilisateurs peuvent lui envoyer des données et recevoir des résultats via l’API REST.

**Demande/Réponse** : l’utilisateur envoie une ou plusieurs lignes de données automobiles au service à l’aide d’un protocole HTTP. Le service répond avec un ou plusieurs jeux de résultats.

Vous trouverez des exemples d’appels REST sous l’onglet **Consommer** de la page de détails du service web.

   ![Capture d’écran montrant un exemple d’appel REST que les utilisateurs peuvent trouver sous l’onglet Consommer](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Accédez à l’onglet **API Doc** pour plus de détails d’API.

  ![Capture d’écran montrant les détails supplémentaires d’API que les utilisateurs peuvent trouver sous l’onglet API Doc](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Gérer les modèles et les déploiements dans l’espace de travail du service Azure Machine Learning

Les modèles et les déploiements du service web que vous créez dans l’interface visuelle peuvent être gérés à partir de l’espace de travail du service Azure Machine Learning.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  

1. Dans votre espace de travail, sélectionnez **Modèles**. Sélectionnez ensuite l’expérience que vous avez créée.

    ![Capture d’écran montrant comment accéder à des expériences dans le portail Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Dans cette page, vous verrez des détails supplémentaires sur le modèle.

    ![Capture d’écran montrant une vue d’ensemble des statistiques d’expérience dans le portail Azure](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Sélectionnez **Déploiements** pour lister tous les services web qui utilisent ce modèle. Sélectionnez le nom du service web pour accéder à la page de détails du service web. Dans cette page, vous pouvez obtenir des informations détaillées sur le service web.

    ![Capture d’écran d’un rapport d’exécution détaillé](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les étapes clés de la création, du déploiement et de la consommation d’un modèle Machine Learning dans l’interface visuelle. Pour en savoir plus sur la façon dont vous pouvez utiliser l’interface visuelle pour résoudre d’autres types de problèmes, consultez les exemples d’expériences.

> [!div class="nextstepaction"]
> [Exemple de classification de risque de crédit](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
