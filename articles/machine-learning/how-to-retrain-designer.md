---
title: Réentraîner des modèles à l’aide du concepteur Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment réentraîner des modèles avec des pipelines publiés dans le concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 12/15/2019
ms.openlocfilehash: d1382da739fd8ca56d4cc53c2c302331bdfbf1c3
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311867"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Réentraîner des modèles avec le concepteur Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ce guide pratique explique comment utiliser le concepteur Azure Machine Learning pour réentraîner un modèle Machine Learning. Découvrez comment utiliser des pipelines publiés pour automatiser les workflows de Machine Learning en vue d’un réentraînement.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Entraîner un modèle Machine Learning
> * Créer un paramètre de pipeline
> * Publier le pipeline d’entraînement
> * Réentraîner le modèle

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree).

* Un espace de travail Azure Machine Learning la référence SKU Entreprise.

Ce guide pratique suppose des connaissances de base relatives à la création de pipelines dans le concepteur. Pour une présentation guidée du concepteur, suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exemple de pipeline

Le pipeline utilisé dans cet article est une version modifiée de celui qui se trouve dans [Exemple 3 – Prédiction de revenus](how-to-designer-sample-classification-predict-income.md). Il se sert du module [Importer des données](algorithm-module-reference/import-data.md) au lieu de l’exemple de jeu de données pour vous montrer comment entraîner un modèle à l’aide de vos propres données.

![Capture d’écran montrant l’exemple de pipeline modifié avec une zone mettant en surbrillance le module Importer des données](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Entraîner un modèle Machine Learning

Pour réentraîner un modèle, il faut disposer d’un modèle initial. Cette section explique comment entraîner un modèle et à accéder au modèle enregistré avec le concepteur.

1. Sélectionnez le module **Importer des données**.
1. Dans le volet Propriétés, indiquez une source de données.

    ![Capture d’écran montrant un exemple de configuration du module Importer des données](./media/how-to-retrain-designer/import-data-settings.png)

    Dans cet exemple, les données sont stockées dans un [magasin de données Azure](how-to-access-data.md). Si vous ne disposez pas déjà d’un magasin de données, vous pouvez en créer un maintenant en sélectionnant **Nouveau magasin de données**.

1. Indiquez le chemin de vos données. Vous pouvez également sélectionner **Parcourir** pour naviguer visuellement dans votre magasin de données. 

1. Sélectionnez l’option **Exécuter** située en haut du canevas.
    
    > [!NOTE]
    > Si vous avez déjà défini un calcul par défaut pour ce brouillon de pipeline, le pipeline s’exécute automatiquement. Dans le cas contraire, vous pouvez suivre les invites du volet Paramètres qui s’affichent pour en définir un maintenant.

### <a name="locate-your-trained-model"></a>Localiser le modèle entraîné

Le concepteur enregistre toutes les sorties de pipeline, et notamment les modèles entraînés, dans le compte de stockage par défaut. Vous pouvez aussi accéder directement au modèle entraîné dans le concepteur :

1. Attendez la fin de l’exécution du pipeline.

1. Sélectionnez le module **Entraîner le modèle**.

1. Dans le volet Paramètres, sélectionnez **Sorties**.

1. Sélectionnez **Trained_model** pour télécharger le modèle.

![Capture d’écran montrant comment télécharger le modèle entraîné](./media/how-to-retrain-designer/download-model.png)

## <a name="create-a-pipeline-parameter"></a>Créer un paramètre de pipeline

Ajoutez des paramètres de pipeline pour définir dynamiquement des variables au moment du runtime. Pour ce pipeline, ajoutez un paramètre pour le chemin des données d’entraînement afin de pouvoir réentraîner votre modèle sur un nouveau jeu de données.

1. Sélectionnez le module **Importer des données**.
1. Dans le volet Paramètres, sélectionnez les points de suspension situés au-dessus du champ **Chemin**.
1. Sélectionnez **Ajouter au paramètre de pipeline**.
1. Indiquez un nom de paramètre et une valeur par défaut.

    > [!NOTE]
    > Vous pouvez examiner et modifier vos paramètres de pipeline en sélectionnant **l’icône d’engrenage Paramètres** à côté du titre de votre brouillon de pipeline. 

![Capture d’écran montrant comment créer un paramètre de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publier un pipeline d’entraînement

Le fait de publier un pipeline a pour effet de créer un point de terminaison de pipeline. Les points de terminaison de pipeline permettent de réutiliser et de gérer les pipelines à des fins de répétabilité et d’automatisation. Dans cet exemple, vous avez configuré votre pipeline pour le réentraînement.

1. Sélectionnez **Publier** au-dessus du canevas du concepteur.
1. Sélectionnez ou créez un point de terminaison de pipeline.

    > [!NOTE]
    > Vous pouvez publier plusieurs pipelines sur un même point de terminaison. Chacun reçoit un numéro de version, que vous pouvez spécifier lorsque vous appelez le point de terminaison de pipeline.

1. Sélectionnez **Publier**.

## <a name="retrain-your-model"></a>Réentraîner le modèle

Maintenant que vous disposez d’un pipeline d’entraînement publié, vous pouvez l’utiliser pour réentraîner votre modèle à l’aide de nouvelles données. Vous pouvez envoyer des exécutions à partir d’un point de terminaison de pipeline sur le portail ou programmatiquement.

### <a name="submit-runs-with-the-designer"></a>Envoyer des exécutions avec le concepteur

Suivez les étapes ci-dessous pour envoyer une exécution de point de terminaison de pipeline à partir du concepteur :

1. Accédez à la page **Points de terminaison**.

1. Sélectionnez l’onglet **Points de terminaison de pipeline**.

1. Sélectionnez votre point de terminaison de pipeline.

1. Sélectionnez l’onglet **Pipelines publiés**.

1. Sélectionnez le pipeline que vous souhaitez exécuter.

1. Sélectionnez **Exécuter**.

1. Dans la boîte de dialogue d’installation, vous pouvez indiquer une nouvelle valeur de chemin de données d’entrée, qui pointe vers votre nouveau jeu de données.

![Capture d’écran montrant comment configurer une exécution de pipeline paramétrable dans le concepteur](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Envoyer des exécutions avec du code

Il existe plusieurs façons d’accéder programmatiquement à un point de terminaison REST en fonction de l’environnement de développement. Vous trouverez des exemples de code montrant comment envoyer des exécutions de pipeline avec des paramètres sous l’onglet **Consommer** de votre pipeline.

## <a name="next-steps"></a>Étapes suivantes

Suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md) sur le concepteur pour entraîner et déployer un modèle de régression.
