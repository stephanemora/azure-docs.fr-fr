---
title: Créer et gérer les espaces de travail du service Azure Machine Learning
description: Découvrez comment créer, afficher et supprimer des espaces de travail du service Azure Machine Learning dans le Portail Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 1565c54779278b440cfe631951e964921cc85720
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51709880"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Créer et gérer les espaces de travail du service Azure Machine Learning

Dans cet article, vous créez, affichez et supprimez des [**espaces de travail du service Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) dans le Portail Azure pour le [service Azure Machine Learning](overview-what-is-azure-ml.md).  Vous pouvez également créer et supprimer des espaces de travail [à l’aide de l’interface CLI](reference-azure-machine-learning-cli.md) ou du [code Python](https://aka.ms/aml-sdk).

Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLfree) avant de commencer.

## <a name="create-a-workspace"></a>Créer un espace de travail 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Afficher un espace de travail

1. En haut à gauche du portail, sélectionnez **Tous les services**. 

1. Dans le champ de filtre **Tous les services**, tapez **Espace de travail du service Machine Learning**.  

   ![recherche d’un espace de travail du service Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. Dans les résultats du filtre, sélectionnez **Espace de travail du service Machine Learning** pour afficher une liste de vos espaces de travail. 

   ![recherche d’un espace de travail du service Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Examinez la liste des espaces de travail trouvés. Vous pouvez filtrer en fonction d’abonnements, de groupes de ressources et de localisations.  

   ![Liste des espaces de travail du service Microsoft Azure Machine Learning](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Sélectionnez l’espace de travail que vous venez de créer pour voir ses propriétés.

   ![png](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Supprimer un espace de travail

Utilisez le bouton Supprimer en haut de l’espace de travail que vous souhaitez supprimer.

  ![png](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Suivez le tutoriel complet pour apprendre à utiliser un espace de travail et créer, entraîner et déployer des modèles avec le service Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
