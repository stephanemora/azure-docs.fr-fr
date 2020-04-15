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
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810349"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Réentraîner des modèles à l’aide du concepteur Azure Machine Learning (préversion)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ce guide pratique explique comment utiliser le concepteur Azure Machine Learning pour réentraîner un modèle Machine Learning. Vous allez utiliser des pipelines publiés pour automatiser votre flux de travail et définir des paramètres pour effectuer l’apprentissage de votre modèle sur de nouvelles données. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Effectuer l'apprentissage un modèle Machine Learning
> * Créer un paramètre de pipeline
> * Publier le pipeline d’apprentissage
> * Ré-effectuer l’apprentissage de votre modèle avec de nouveaux paramètres.

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning la référence SKU Entreprise.
* Jeu de données accessible au concepteur. Il peut s'agir de l'une des ressources suivantes :
   * Jeu de données Azure Machine Learning inscrit.
    
     **-ou-**
   * Fichier de données stocké dans un magasin de données Azure Machine Learning.
   
Pour plus d’informations sur l’accès à des données à l’aide du concepteur, consultez [Comment importer des données dans le concepteur](how-to-designer-import-data.md).

Cet article présuppose également que vous disposez d’une connaissance de base de la création de pipelines dans le concepteur. Pour une présentation guidée, suivez le [tutoriel](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Exemple de pipeline

Le pipeline utilisé dans cet article est une version modifiée de l’[Exemple 3 : Prédiction des revenus](samples-designer.md#classification-samples). Le pipeline se sert du module [Importer des données](algorithm-module-reference/import-data.md) plutôt que de l’exemple de jeu de données pour vous montrer comment effectuer l’apprentissage d’un modèle à l’aide de vos propres données.

![Capture d’écran montrant l’exemple de pipeline modifié avec une zone mettant en surbrillance le module Importer des données](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Créer un paramètre de pipeline

Créez des paramètres de pipeline pour définir des variables de façon dynamique lors du runtime. Pour cet exemple, vous allez convertir le chemin d’accès aux données d’apprentissage d’une valeur fixe en un paramètre, afin de pouvoir ré-effectuer l’apprentissage de votre modèle sur des données différentes.

1. Sélectionnez le module **Importer des données**.

    > [!NOTE]
    > Cet exemple utilise le module Importer des données pour accéder aux données d’un magasin de données inscrit. Vous cependant pouvez suivre une procédure similaire si vous utilisez d’autres modèles d’accès aux données.

1. Dans le volet des détails du module à droite du canevas, sélectionnez votre source de données.

1. Entrez le chemin de vos données. Vous pouvez également sélectionner **Parcourir le chemin** pour accéder à votre arborescence de fichiers. 

1. Pointez la souris sur le champ **Chemin d’accès** et sélectionnez les points de suspension situés au-dessus du champ **Chemin d’accès** qui s’affiche.

    ![Capture d’écran montrant comment créer un paramètre de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Sélectionnez **Ajouter au paramètre de pipeline**.

1. Indiquez un nom de paramètre et une valeur par défaut.

   > [!NOTE]
   > Vous pouvez examiner et modifier vos paramètres de pipeline en sélectionnant l’icône d’engrenage **Paramètres** à côté du titre de votre brouillon de pipeline. 

1. Sélectionnez **Enregistrer**.

1. Soumettez l’exécution du pipeline.

## <a name="find-a-trained-model"></a>Rechercher un modèle formé

Le concepteur enregistre toute la sortie du pipeline, dont des modèles formés, dans le compte de stockage d’espace de travail par défaut. Vous pouvez cependant aussi accéder directement aux modèles formés dans le concepteur :

1. Attendez la fin de l’exécution du pipeline.
1. Sélectionnez le module **Entraîner le modèle**.
1. Dans le volet des détails des modules à droite du canevas, sélectionnez **Sorties + journaux**.
1. Vous pouvez trouver votre modèle dans **Autres sorties** ainsi que des journaux d’exécution.
1. Vous pouvez également sélectionner l’icône **Afficher la sortie**. À partir de là, vous pouvez suivre l’instruction de la boîte de dialogue en accédant directement à votre magasin de données. 

![Capture d’écran montrant comment télécharger le modèle formé](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publier un pipeline d’entraînement

Publiez un pipeline sur un point de terminaison de pipeline pour réutiliser facilement vos pipelines à l’avenir. Un point de terminaison de pipeline crée un point de terminaison REST pour appeler le pipeline à l’avenir. Dans cet exemple, votre point de terminaison de pipeline vous permet de réutiliser votre pipeline pour ré-effectuer l’apprentissage d’un modèle sur des données différentes.

1. Sélectionnez **Publier** au-dessus du canevas du concepteur.
1. Sélectionnez ou créez un point de terminaison de pipeline.

   > [!NOTE]
   > Vous pouvez publier plusieurs pipelines sur un même point de terminaison. Chaque pipeline dans un point de terminaison reçoit un numéro de version que vous pouvez spécifier lorsque vous appelez le point de terminaison du pipeline.

1. Sélectionnez **Publier**.

## <a name="retrain-your-model"></a>Réentraîner le modèle

À présente que vous disposez d’un pipeline d’apprentissage publié, vous pouvez l’utiliser pour ré-effectuer l’apprentissage de votre modèle sur de nouvelles données. Vous pouvez envoyer des exécutions à partir d’un point de terminaison de pipeline de l’espace de travail Studio ou par programmation.

### <a name="submit-runs-by-using-the-designer"></a>Envoyer des exécutions à l’aide du concepteur

Suivez les étapes suivantes pour envoyer une exécution de point de terminaison de pipeline paramétré à partir du concepteur :

1. Accédez à la page **Points de terminaison** de votre espace de travail Studio.
1. Sélectionnez l’onglet **Points de terminaison de pipeline**. Sélectionnez votre point de terminaison de pipeline.
1. Sélectionnez l’onglet **Pipelines publiés**. Ensuite, sélectionnez la version de pipeline que vous souhaitez exécuter.
1. Sélectionnez **Envoyer**.
1. Dans la boîte de dialogue de configuration, vous pouvez spécifier les valeurs des paramètres pour l’exécution. Pour cet exemple, mettez à jour le chemin d’accès des données pour effectuer l’apprentissage de votre modèle à l’aide d’un jeu de données non US.

![Capture d’écran montrant comment configurer une exécution de pipeline paramétrable dans le concepteur](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Envoyer des exécutions à l’aide de code

Vous trouverez le point de terminaison REST d'un pipeline publié dans le panneau de vue d'ensemble. En appelant le point de terminaison, vous pouvez réentraîner le pipeline publié.

Pour effectuer un appel REST, vous devez disposer d’un en-tête d’authentification de type porteur OAuth 2.0. Pour plus d’informations concernant la configuration de l’authentification sur votre espace de travail et l’exécution d’un appel REST paramétrable, consultez [Créer un pipeline Azure Machine Learning pour le scoring par lots](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un point de terminaison de pipeline d’apprentissage paramétré à l’aide du concepteur.

Pour une procédure pas à pas complète de déploiement d’un modèle destiné à effectuer des prédictions, consultez le [tutoriel du concepteur](tutorial-designer-automobile-price-train-score.md) pour former et déployer un modèle de régression.
