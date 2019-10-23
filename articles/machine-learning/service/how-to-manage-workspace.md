---
title: Créer des espaces de travail Azure ML dans le portail
titleSuffix: Azure Machine Learning
description: Découvrez comment créer, afficher et supprimer des espaces de travail Azure Machine Learning dans le portail Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 511c737e160c0f0753e570314c9b29346972cb04
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71269263"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Créer et gérer des espaces de travail Azure Machine Learning dans le Portail Azure

Dans cet article, vous allez créer, afficher et supprimer des [**espaces de travail Azure Machine Learning**](concept-workspace.md) dans le portail Azure pour [Azure Machine Learning](overview-what-is-azure-ml.md).  Le portail est le moyen le plus simple de prendre en main les espaces de travail, mais à mesure que vos besoins évoluent ou que les besoins de l’automatisation augmentent, vous pouvez également créer et supprimer des espaces de travail [à l’aide de l’interface CLI](reference-azure-machine-learning-cli.md), [à l’aide de code Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [via l’extension VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Créer un espace de travail

Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Télécharger un fichier de configuration

1. Si vous prévoyez de créer une [machine virtuelle Notebook](tutorial-1st-experiment-sdk-setup.md#azure), ignorez cette étape.

1. Si vous prévoyez d’utiliser du code sur votre environnement local qui référence cet espace de travail, sélectionnez **Télécharger config.json** dans la section **Vue d’ensemble** de l’espace de travail.  

   ![Télécharger config.json](./media/how-to-manage-workspace/configure.png)
   
   Placez le fichier dans la structure de répertoires avec vos scripts Python ou vos notebooks Jupyter. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé *.azureml* ou dans un répertoire parent. Quand vous créez une machine virtuelle Notebook, ce fichier est automatiquement ajouté au répertoire approprié sur la machine virtuelle.


## <a name="view"></a>Afficher un espace de travail

1. En haut à gauche du portail, sélectionnez **Tous les services**.

1. Dans le champ de filtre **Tous les services**, entrez **service machine learning**.  

1. Sélectionnez **Espaces de travail de service Machine Learning**.

   ![Rechercher l’espace de travail Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Examinez la liste des espaces de travail trouvés. Vous pouvez filtrer en fonction d’abonnements, de groupes de ressources et de localisations.  

1. Sélectionnez un espace de travail pour afficher ses propriétés.
   ![Propriétés de l’espace de travail](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Supprimer un espace de travail

Utilisez le bouton Supprimer en haut de l’espace de travail que vous souhaitez supprimer.

  ![Bouton Supprimer](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Suivez le didacticiel complet pour apprendre à utiliser un espace de travail et créer, faire l’apprentissage et déployer des modèles avec Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
