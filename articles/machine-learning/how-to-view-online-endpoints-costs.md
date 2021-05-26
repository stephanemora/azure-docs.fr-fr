---
title: Afficher les coûts des points de terminaison en ligne managés (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment afficher les coûts d’un point de terminaison en ligne managé dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 49b8aa9b89ab56dc5968c72f2ebe22bf1224fd68
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382571"
---
# <a name="view-costs-for-an-azure-machine-learning-managed-online-endpoint-preview"></a>Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)

Découvrez comment afficher les coûts d’un point de terminaison en ligne managé (préversion). Les coûts de vos points de terminaison s’accumulent dans l’espace de travail associé. Vous pouvez voir les coûts d’un point de terminaison spécifique en utilisant des étiquettes.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!IMPORTANT]
> Cet article s’applique uniquement à l’affichage des coûts des points de terminaison en ligne managés Azure Machine Learning (préversion). Les points de terminaison en ligne managés diffèrent des autres ressources, car ils doivent utiliser des étiquettes pour suivre les coûts. Pour plus d’informations sur l’affichage des coûts des autres ressources Azure, consultez [Démarrage rapide : Explorer et analyser les coûts avec une analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md).

## <a name="prerequisites"></a>Prérequis

- Déployer un point de terminaison en ligne managé Azure Machine Learning (préversion)
- Avoir au moins un accès [Lecteur de facturation](../role-based-access-control/role-assignments-portal.md) sur l’abonnement sur lequel le point de terminaison est déployé

## <a name="view-costs"></a>Afficher les coûts

Accédez à la page **Analyse des coûts** de votre abonnement :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Analyse des coûts** pour votre abonnement.

    [![Analyse des coûts du point de terminaison en ligne managé : capture d’écran d’un abonnement dans le portail Azure montrant un cadre rouge autour du bouton « Analyse des coûts » sur le côté gauche.](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png#lightbox)

Créez un filtre pour définir l’étendue des données à votre ressource d’espace de travail Azure Machine Learning :

1. Dans la barre de navigation supérieure, sélectionnez **Ajouter un filtre**.

1. Dans la première liste déroulante du filtre, sélectionnez **Ressource** comme type de filtre.

1. Dans la deuxième liste déroulante du filtre, sélectionnez votre espace de travail Azure Machine Learning.

    [![Analyse des coûts du point de terminaison en ligne managé : capture d’écran de la vue Analyse des coûts montrant un cadre rouge autour du bouton « Ajouter un filtre » en haut à droite.](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png#lightbox)

Créez un filtre d’étiquette pour afficher votre point de terminaison en ligne managé et/ou votre déploiement en ligne managé :
1. Sélectionnez **Ajouter un filtre** > **Étiquette** > **azuremlendpoint**: "\<your endpoint name>" 
1. Sélectionnez **Ajouter un filtre** > **Étiquette** > **azuremldeployment**: "\<your deployment name>"

    > [!NOTE]
    > Les valeurs en dollars figurant dans cette image sont fictives et ne reflètent pas les coûts réels.

    [![Analyse des coûts du point de terminaison en ligne managé : capture d’écran de la vue Analyse des coûts montrant un cadre rouge autour du bouton « Étiquette » en haut à droite.](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes
- [Que sont les points de terminaison ?](concept-endpoints.md)
- Découvrez comment [superviser votre point de terminaison en ligne managé](./how-to-monitor-online-endpoints.md).
- [Guide pratique pour déployer des points de terminaison en ligne managés avec Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Guide pratique pour déployer des points de terminaison en ligne managés avec le studio](how-to-use-managed-online-endpoint-studio.md)