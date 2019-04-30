---
title: Créer et gérer des espaces de travail
titleSuffix: Azure Machine Learning service
description: Découvrez comment créer, afficher et supprimer des espaces de travail du service Azure Machine Learning dans le Portail Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819630"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Créer et gérer les espaces de travail du service Azure Machine Learning

Dans cet article, vous créez, affichez et supprimez des [**espaces de travail du service Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) dans le Portail Azure pour le [service Azure Machine Learning](overview-what-is-azure-ml.md).  Vous pouvez également créer et supprimer des espaces de travail [à l’aide de l’interface CLI](reference-azure-machine-learning-cli.md) ou du [code Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Créer un espace de travail 

Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Afficher un espace de travail

1. En haut à gauche du portail, sélectionnez **Tous les services**. 

1. Dans le **tous les services** champ de filtre, tapez **d’apprentissage service**.  

1. Sélectionnez **espaces de travail Machine Learning service**.

   ![recherche de l’espace de travail de service Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Examinez la liste des espaces de travail trouvés. Vous pouvez filtrer en fonction d’abonnements, de groupes de ressources et de localisations.  

1. Sélectionnez un espace de travail pour afficher ses propriétés.
   ![Propriétés de l’espace de travail](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Supprimer un espace de travail

Utilisez le bouton Supprimer en haut de l’espace de travail que vous souhaitez supprimer.

  ![Bouton Supprimer](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Supprimer des ressources 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Suivez le tutoriel complet pour apprendre à utiliser un espace de travail et créer, entraîner et déployer des modèles avec le service Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
