---
title: Réentraîner des modèles à l’aide du concepteur Azure Machine Learning (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment réentraîner des modèles avec des pipelines publiés dans le concepteur Azure Machine Learning (préversion).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: c8791e933882832dc7b0037c860a4c4e1e9a54c7
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389033"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Réentraîner des modèles à l’aide du concepteur Azure Machine Learning (préversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ce guide pratique explique comment utiliser le concepteur Azure Machine Learning pour réentraîner un modèle Machine Learning. Découvrez comment utiliser des pipelines publiés pour automatiser les workflows de Machine Learning en vue d’un réentraînement.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Entraîner un modèle Machine Learning
> * Créer un paramètre de pipeline
> * Publier le pipeline d’entraînement
> * Réentraîner le modèle

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree).
* Un espace de travail Azure Machine Learning la référence SKU Entreprise.

Cet article suppose des connaissances de base relatives à la création de pipelines dans le concepteur. Pour une présentation guidée du concepteur, suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exemple de pipeline

Le pipeline utilisé dans cet article est une version modifiée de celui qui se trouve dans [Exemple 3 : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md). Il se sert du module [Importer des données](algorithm-module-reference/import-data.md) au lieu de l’exemple de jeu de données pour vous montrer comment effectuer l’apprentissage d’un modèle à l’aide de vos propres données.

![Capture d’écran montrant l’exemple de pipeline modifié avec une zone mettant en surbrillance le module Importer des données](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Entraîner un modèle Machine Learning

Pour réentraîner un modèle, il faut disposer d’un modèle initial. Cette section explique comment effectuer l’apprentissage d’un modèle et accéder au modèle enregistré avec le concepteur.

1. Sélectionnez le module **Importer des données**.
1. Dans le volet Propriétés, indiquez une source de données.

   ![Capture d’écran montrant un exemple de configuration du module Importer des données](./media/how-to-retrain-designer/import-data-settings.png)

   Dans cet exemple, les données sont stockées dans un [magasin de données Azure](how-to-access-data.md). Si vous ne disposez pas déjà d’un magasin de données, vous pouvez en créer un maintenant en sélectionnant **Nouveau magasin de données**.

1. Indiquez le chemin de vos données. Vous pouvez également sélectionner **Parcourir** pour accéder à votre magasin de données. 
1. En haut du canevas, sélectionnez **Envoyer**.
    
   > [!NOTE]
   > Si vous avez déjà défini un calcul par défaut pour ce brouillon de pipeline, le pipeline s’exécute automatiquement. Dans le cas contraire, vous pouvez suivre les invites du volet Paramètres pour en définir un maintenant.

### <a name="find-your-trained-model"></a>Localiser le modèle formé

Le concepteur enregistre toutes les sorties de pipeline, et notamment les modèles entraînés, dans le compte de stockage par défaut. Toutefois, vous pouvez aussi accéder directement aux modèles formés dans le concepteur :

1. Attendez la fin de l’exécution du pipeline.
1. Sélectionnez le module **Entraîner le modèle**.
1. Dans le volet Paramètres, sélectionnez **Sorties+journaux**.
1. Sélectionnez l’icône **Afficher la sortie** et suivez les instructions de la fenêtre contextuelle pour localiser le modèle formé.

![Capture d’écran montrant comment télécharger le modèle formé](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Créer un paramètre de pipeline

Ajoutez des paramètres de pipeline pour définir dynamiquement des variables au moment du runtime. Pour ce pipeline, ajoutez un paramètre pour le chemin des données d’entraînement afin de pouvoir réentraîner votre modèle sur un nouveau jeu de données.

1. Sélectionnez le module **Importer des données**.
1. Dans le volet Paramètres, sélectionnez les points de suspension situés au-dessus du champ **Chemin**.
1. Sélectionnez **Ajouter au paramètre de pipeline**.
1. Indiquez un nom de paramètre et une valeur par défaut.

   > [!NOTE]
   > Vous pouvez examiner et modifier vos paramètres de pipeline en sélectionnant l’icône d’engrenage **Paramètres** à côté du titre de votre brouillon de pipeline. 

![Capture d’écran montrant comment créer un paramètre de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publier un pipeline d’entraînement

Le fait de publier un pipeline a pour effet de créer un point de terminaison de pipeline. Les points de terminaison de pipeline permettent de réutiliser et de gérer les pipelines à des fins de répétabilité et d’automatisation. Dans cet exemple, vous avez configuré votre pipeline pour le réentraînement.

1. Sélectionnez **Publier** au-dessus du canevas du concepteur.
1. Sélectionnez ou créez un point de terminaison de pipeline.

   > [!NOTE]
   > Vous pouvez publier plusieurs pipelines sur un même point de terminaison. Chacun reçoit un numéro de version, que vous pouvez spécifier lorsque vous appelez le point de terminaison de pipeline.

1. Sélectionnez **Publier**.

## <a name="retrain-your-model"></a>Réentraîner le modèle

Maintenant que vous disposez d’un pipeline de formation publié, vous pouvez l’utiliser pour réentraîner votre modèle à l’aide de nouvelles données. Vous pouvez envoyer des exécutions à partir d’un point de terminaison de pipeline sur le Portail Azure ou par programmation.

### <a name="submit-runs-by-using-the-designer"></a>Envoyer des exécutions à l’aide du concepteur

Suivez les étapes ci-dessous pour envoyer une exécution de point de terminaison de pipeline à partir du concepteur :

1. Accédez à la page **Points de terminaison**.
1. Sélectionnez l’onglet **Points de terminaison de pipeline**.
1. Sélectionnez votre point de terminaison de pipeline.
1. Sélectionnez l’onglet **Pipelines publiés**.
1. Sélectionnez le pipeline que vous souhaitez exécuter.
1. Sélectionnez **Envoyer**.
1. Dans la boîte de dialogue Installation, vous pouvez spécifier une nouvelle valeur pour la valeur du chemin d’accès aux données d’entrée. Cette valeur pointe vers votre nouveau jeu de données.

![Capture d’écran montrant comment configurer une exécution de pipeline paramétrable dans le concepteur](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Envoyer des exécutions à l’aide de code

Vous trouverez le point de terminaison REST d'un pipeline publié dans le panneau de vue d'ensemble. En appelant le point de terminaison, vous pouvez réentraîner le pipeline publié.

Pour effectuer un appel REST, vous devez disposer d’un en-tête d’authentification de type porteur OAuth 2.0. Pour plus d’informations concernant la configuration de l’authentification sur votre espace de travail et l’exécution d’un appel REST paramétrable, consultez [Créer un pipeline Azure Machine Learning pour le scoring par lots](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Étapes suivantes

Suivez le [tutoriel sur le concepteur](tutorial-designer-automobile-price-train-score.md) pour effectuer l’apprentissage d’un modèle de régression et le déployer.
