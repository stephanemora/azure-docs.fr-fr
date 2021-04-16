---
title: 'ML Studio (classique) : Migrer vers Azure Machine Learning – Reconstruire un service web'
description: Reconstruire des services web Studio (classiques) en tant que points de terminaison de pipeline dans Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 35ee5bf22aa88c18bade0ebdcd961b7687d24e7f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311819"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Reconstruire un service web Studio (classique) dans Azure Machine Learning

Dans cet article, vous allez apprendre à reconstruire un service web Studio (classique) en tant que **point de terminaison** dans Azure Machine Learning.

Utilisez les points de terminaison du pipeline Azure Machine Learning pour faire des prédictions, effectuer à nouveau l’apprentissage des modèles ou exécuter un pipeline générique. Le point de terminaison REST vous permet d’exécuter des pipelines à partir de n’importe quelle plateforme. 

Cet article fait partie de la série sur la migration de Studio (classique) vers Azure Machine Learning. Pour plus d’informations sur la migration vers Azure Machine Learning, consultez [l’article de présentation de la migration](migrate-overview.md).

> [!NOTE]
> Cette série sur la migration est axée sur le concepteur de glisser-déplacer. Pour plus d’informations sur le déploiement de modèles par programmation, consultez [Déployer des modèles Machine Learning dans Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un espace de travail Azure Machine Learning. [Créez un espace de travail Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Un pipeline de formation Azure Machine Learning. Pour plus d’informations, consultez [Reconstruire une expérience Studio (classique) dans Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Point de terminaison en temps réel vs point de terminaison de pipeline

Les services web Studio (classique) ont été remplacés par des **points de terminaison** dans Azure Machine Learning. Utilisez le tableau suivant pour choisir le type de point de terminaison à utiliser :

|Service web Studio (classique)| Remplacement Azure Machine Learning
|---|---|
|Service web REQUEST/RESPOND (prédiction en temps réel)|Points de terminaison en temps réel|
|Service web Batch (prédiction par lots)|Point de terminaison de pipeline|
|Service web de reformation (reformation)|Point de terminaison de pipeline| 


## <a name="deploy-a-real-time-endpoint"></a>Déployer un point de terminaison en temps réel

Dans Studio (classique), vous avez utilisé un **service web REQUEST/RESPOND** afin de déployer un modèle pour les prédictions en temps réel. Dans Azure Machine Learning, vous utilisez un **point de terminaison en temps réel**.

Il existe plusieurs façons de déployer un modèle dans Azure Machine Learning. L’une des méthodes les plus simples consiste à utiliser le concepteur pour automatiser le processus de déploiement. Pour déployer un modèle en tant que point de terminaison en temps réel, procédez comme suit :

1. Exécutez votre pipeline de formation terminé au moins une fois.
1. Une fois l’exécution terminée, en haut du canevas, sélectionnez **Créer un pipeline d’inférence** > **Pipeline d’inférence en temps réel**.

    ![créer un pipeline d’inférence en temps réel](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Le concepteur convertit le pipeline de formation en pipeline d’inférence en temps réel. Une conversion similaire se produit également dans Studio (classique).

    Dans le concepteur, l’étape de conversion [inscrit également le modèle formé dans votre espace de travail Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Sélectionnez **Envoyer** pour exécuter le pipeline d’inférence en temps réel et vérifier qu’il s’exécute correctement.

1. Après avoir vérifié le pipeline d’inférence, sélectionnez **Déployer**.

1. Entrez un nom pour votre point de terminaison et un type de calcul.

    Le tableau suivant décrit les options de calcul de votre déploiement dans le concepteur :

    | Cible de calcul | Utilisé pour | Description | Création |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Inférence en temps réel|Déploiements de production à grande échelle. Temps de réponse rapide et mise à l’échelle automatique des services.| Créée par l’utilisateur. Pour plus d’informations, consultez [Créer des cibles de calcul](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Azure Container Instances](../how-to-deploy-azure-container-instance.md)|Test ou développement | Charges de travail à petite échelle, basées sur l’UC et nécessitant moins de 48 Go de RAM.| Créée automatiquement par Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Tester le point de terminaison en temps réel

Une fois le déploiement terminé, vous pouvez afficher plus de détails et tester votre point de terminaison :

1. Allez dans l’onglet **Points de terminaison**.
1. Sélectionnez un point de terminaison.
1. Sélectionnez l’onglet **Test**.
    
    ![Capture d’écran montrant l’onglet Points de terminaison avec le bouton Tester le point de terminaison](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Publier un point de terminaison de pipeline pour la prédiction par lots ou la reformation

Vous pouvez également utiliser votre pipeline de formation pour créer un **point de terminaison de pipeline** au lieu d’un point de terminaison en temps réel. Utilisez des **points de terminaison de pipeline** pour effectuer une prédiction par lots ou une reformation.

Les points de terminaison de pipeline remplacent les **points de terminaison d’exécution par lots** et les **services web de reformation** de Studio (classique).

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Publier un point de terminaison de pipeline pour la prédiction par lots

La publication d’un point de terminaison de prédiction par lots est similaire au point de terminaison en temps réel.

Pour publier un point de terminaison de pipeline pour la prédiction par lots, procédez comme suit :

1. Exécutez votre pipeline de formation terminé au moins une fois.

1. Une fois l’exécution terminée, en haut du canevas, sélectionnez **Créer un pipeline d’inférence** > **Pipeline d’inférence par lots**.

    ![Capture d’écran montrant le bouton Créer un pipeline d’inférence sur un pipeline de formation](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Le concepteur convertit le pipeline de formation en un pipeline d’inférence par lots. Une conversion similaire se produit également dans Studio (classique).

    Dans le concepteur, cette étape [inscrit également le modèle formé dans votre espace de travail Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Sélectionnez **Envoyer** pour exécuter le pipeline d’inférence par lots et vérifier qu’il se termine correctement.

1. Après avoir vérifié le pipeline d’inférence, sélectionnez **Publier**.
 
1. Créez un point de terminaison de pipeline ou sélectionnez-en un existant.
    
    Un nouveau point de terminaison de pipeline crée un nouveau point de terminaison REST pour votre pipeline. 

    Si vous sélectionnez un point de terminaison de pipeline existant, vous ne remplacez pas le pipeline existant. Au lieu de cela, Azure Machine Learning contrôle les versions de chaque pipeline dans le point de terminaison. Vous pouvez spécifier la version à exécuter dans votre appel REST. Vous devez également définir un pipeline par défaut si l’appel REST ne spécifie pas de version.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Publier un point de terminaison de pipeline pour la reformation

Pour publier un point de terminaison de pipeline pour la reformation, vous devez déjà disposer d’un brouillon de pipeline qui effectue l’apprentissage d’un modèle. Pour plus d’informations sur la création d’un pipeline de formation, consultez [Reconstruire une expérience Studio (classique)](migrate-rebuild-experiment.md).

Pour réutiliser votre point de terminaison de pipeline pour la reformation, vous devez créer un **paramètre de pipeline** pour votre jeu de données d’entrée. Cela vous permet de définir dynamiquement votre jeu de données d’apprentissage, afin que vous puissiez reformer votre modèle.

Pour publier un point de terminaison de pipeline de reformation, procédez comme suit :

1. Exécutez votre pipeline de formation au moins une fois.
1. Une fois l’exécution terminée, sélectionnez le module du jeu de données.
1. Dans le volet d’informations du module, sélectionnez **Définir en tant que paramètre de pipeline**.
1. Donnez un nom descriptif comme « InputDataset ».    

    ![Capture d’écran mettant en évidence la façon de créer un paramètre de pipeline](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Cela crée un paramètre de pipeline pour votre jeu de données d’entrée. Quand vous appelez votre point de terminaison de pipeline pour l’apprentissage, vous pouvez spécifier un nouveau jeu de données pour reformer le modèle.

1. Sélectionnez **Publier**.

    ![Capture d’écran mettant en évidence le bouton Publier sur un pipeline de formation](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Appeler votre point de terminaison de pipeline à partir de Studio

Une fois que vous avez créé votre point de terminaison d’inférence par lots ou de pipeline de reformation, vous pouvez appeler votre point de terminaison directement à partir de votre navigateur.

1. Accédez à l’onglet **Pipelines**, puis sélectionnez **Points de terminaison de pipeline**.
1. Sélectionnez le point de terminaison de pipeline que vous souhaitez exécuter.
1. Sélectionnez **Envoyer**.

    Vous pouvez spécifier n’importe quel paramètre de pipeline après avoir sélectionné **Envoyer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à reconstruire un service web Studio (classique) dans Azure Machine Learning. L’étape suivante consiste à [intégrer votre service web à des applications clientes](migrate-rebuild-integrate-with-client-app.md).


Consultez les autres articles de la série sur la migration Studio (classique) :

1. [Vue d’ensemble de la migration](migrate-overview.md).
1. [Migrer un jeu de données](migrate-register-dataset.md).
1. [Reconstruire un pipeline de formation Studio (classique)](migrate-rebuild-experiment.md).
1. **Reconstruire un service web Studio (classique)**.
1. [Intégrer un service web Azure Machine Learning à des applications clientes](migrate-rebuild-integrate-with-client-app.md).
1. [Migrer Exécuter un script R](migrate-execute-r-script.md).
