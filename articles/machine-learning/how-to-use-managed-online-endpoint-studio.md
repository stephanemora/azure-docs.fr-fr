---
title: Utiliser des points de terminaison en ligne managés (préversion) dans le studio
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et utiliser des points de terminaison en ligne managés (préversion) avec le studio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: how-to, managed online endpoints
ms.author: ssambare
author: shivanissambare
ms.reviewer: peterlu
ms.date: 05/25/2021
ms.openlocfilehash: 96ec383d5110b3acf4ca3b83bd40526443df887a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382590"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio

Découvrez comment utiliser le studio pour créer et gérer vos points de terminaison en ligne managés (préversion) dans Azure Machine Learning. Utilisez des points de terminaison en ligne managés pour simplifier les déploiements en production. Pour plus d’informations sur les points de terminaison en ligne managés, consultez la [présentation des points de terminaison](concept-endpoints.md).

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un point de terminaison en ligne managé
> * Afficher des points de terminaison en ligne managés
> * Mettre à jour des points de terminaison en ligne managés
> * Supprimer des points de terminaison en ligne managés et des déploiements

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- Un [modèle inscrit dans votre espace de travail](how-to-deploy-and-where.md#registermodel).
- Un fichier de scoring pour votre modèle déployé. Pour obtenir un exemple pas à pas d’inscription de modèle et de création d’un fichier de scoring, consultez [Tutoriel : Classification d’images](tutorial-train-models-with-aml.md).
- Un environnement personnalisé inscrit dans votre espace de travail **ou** une image de registre de conteneurs Docker avec un environnement Python. Pour plus d’informations sur les environnements, consultez [Créer et utiliser des environnements logiciels dans Azure Machine Learning](how-to-use-environments.md).

## <a name="create-a-managed-online-endpoint-preview"></a>Créer un point de terminaison en ligne managé (préversion)

Utilisez le studio pour créer un point de terminaison en ligne managé (préversion) directement dans votre navigateur. Quand vous créez un point de terminaison en ligne managé dans le studio, vous devez définir un déploiement initial. Vous ne pouvez pas créer un point de terminaison managé en ligne vide.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Points de terminaison**.
1. Sélectionnez **+ Créer (préversion)** .

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="Créer un point de terminaison en ligne managé à partir de l’onglet Points de terminaison":::

Vous pouvez aussi créer un point de terminaison en ligne managé à partir de la page **Modèles** dans le studio. Il s’agit d’un moyen simple d’ajouter un modèle à un déploiement en ligne managé existant.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Modèles**.
1. Sélectionnez un modèle en cochant le cercle à côté de son nom.
1. Sélectionnez **Déployer** > **Déployer sur un point de terminaison (préversion)** .

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/models-create-managed-online-endpoint.png" alt-text="Créer un point de terminaison en ligne managé à partir de l’onglet Modèles":::

Suivez l’Assistant Installation pour configurer votre point de terminaison en ligne managé.

## <a name="view-managed-online-endpoints-preview"></a>Afficher des points de terminaison en ligne managés (préversion)

Vous pouvez afficher vos points de terminaison en ligne managés (préversion) dans la page **Points de terminaison**. Utilisez la page des détails du point de terminaison pour trouver des informations critiques, comme l’URI du point de terminaison, son état, les outils de test, les moniteurs d’activité, les journaux de déploiement et l’exemple de code de consommation :

1. Dans la barre de navigation gauche, sélectionnez **Points de terminaison**.
1. (Facultatif) Créez un **filtre** sur le **type de calcul** pour afficher uniquement les types de calcul **managés**.
1. Sélectionnez un nom de point de terminaison pour en afficher la page des détails.

### <a name="test"></a>Test

Utilisez l’onglet **Test** de la page des détails du point de terminaison pour tester votre déploiement en ligne managé. Entrez l’exemple d’entrée et affichez les résultats.

1. Sélectionnez l’onglet **Test** dans la page des détails du point de terminaison.
1. Utilisez la liste déroulante pour sélectionner le déploiement à tester.
1. Entrez l’exemple d’entrée.
1. Sélectionnez **Test**.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="Tester un déploiement en fournissant des exemples de données, directement dans votre navigateur":::

### <a name="monitoring"></a>Surveillance

Utilisez l’onglet **Supervision** pour afficher les graphes généraux du moniteur d’activité pour votre point de terminaison en ligne managé.

Pour utiliser l’onglet Supervision, vous devez sélectionner **Activer la collecte de données et de diagnostics Application Insights** quand vous créez votre point de terminaison.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="Superviser les métriques au niveau du point de terminaison dans le studio":::

Pour plus d’informations sur la manière d’afficher d’autres moniteurs et alertes, consultez [Guide pratique pour superviser des points de terminaison en ligne managés](how-to-monitor-online-endpoints.md).

## <a name="update-managed-online-endpoints-preview"></a>Mettre à jour des points de terminaison en ligne managés (préversion)

Apprenez à mettre à jour vos points de terminaison en ligne managés (préversion) pour ajouter d’autres déploiements et ajuster l’allocation du trafic.

### <a name="add-a-managed-online-deployment"></a>Ajouter un déploiement en ligne managé

Utilisez les instructions suivantes pour ajouter un déploiement à un point de terminaison en ligne managé existant :

1. Sélectionnez le bouton **+ Ajouter un déploiement** dans la [page des détails du point de terminaison](#view-managed-online-endpoints-preview).
2. Suivez les instructions pour terminer le déploiement.

Vous pouvez aussi utiliser la page **Modèles** pour ajouter un déploiement :

1. Dans la barre de navigation gauche, sélectionnez la page **Modèles**.
1. Sélectionnez un modèle en cochant le cercle à côté de son nom.
1. Sélectionnez **Déployer** > **Déployer sur un point de terminaison (préversion)** .
1. Choisissez de déployer sur un point de terminaison en ligne managé existant.

> [!NOTE]
> Vous pouvez ajuster l’équilibre du trafic entre les déploiements dans un point de terminaison lors de l’ajout d’un nouveau déploiement.
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="Utiliser des curseurs pour contrôler la distribution du trafic entre plusieurs déploiements":::

### <a name="update-deployment-traffic-allocation"></a>Mettre à jour l’allocation du trafic de déploiement

Utilisez l’**allocation du trafic de déploiement** pour contrôler le pourcentage de requêtes entrantes qui parviennent à chaque déploiement dans un point de terminaison.

1. Dans la page des détails du point de terminaison, sélectionnez **Mettre à jour le trafic**.
2. Ajustez votre trafic et sélectionnez **Mettre à jour**.

> [!TIP]
> Le **pourcentage de trafic total** doit être égal à 0 % (pour désactiver le trafic) ou à 100 % (pour activer le trafic).

### <a name="update-deployment-instance-count"></a>Mettre à jour le nombre d’instances de déploiement

Utilisez les instructions suivantes pour effectuer un scale-up ou un scale-down d’un déploiement individuel en ajustant le nombre d’instances :

1. Dans la page des détails du point de terminaison. Recherchez la carte du déploiement à mettre à jour.
1. Sélectionnez l’**icône de modification** dans la carte détaillée du déploiement.
1. Mettez à jour le nombre d’instances.
1. Sélectionnez **Mettre à jour**.


## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>Supprimer des points de terminaison en ligne managés et des déploiements (préversion)

Découvrez comment supprimer un point de terminaison en ligne managé dans son ensemble (préversion) et ses déploiements associés (préversion). Ou supprimez un déploiement individuel d’un point de terminaison en ligne managé.

### <a name="delete-a-managed-online-endpoint"></a>Supprimer un point de terminaison en ligne managé

La suppression d’un point de terminaison en ligne managé supprime également tous ses déploiements associés.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Points de terminaison**.
1. Sélectionnez un point de terminaison en cochant le cercle à côté de son nom.
1. Sélectionnez **Supprimer**.

Vous pouvez aussi supprimer un point de terminaison en ligne managé directement dans la [page des détails du point de terminaison](#view-managed-online-endpoints-preview). 


### <a name="delete-an-individual-deployment"></a>Supprimer un déploiement individuel

Effectuez les étapes suivantes pour supprimer un déploiement individuel d’un point de terminaison en ligne managé. Cette suppression affecte les autres déploiements inclus dans le point de terminaison en ligne managé :

> [!NOTE]
> Vous ne pouvez pas supprimer un déploiement qui a du trafic alloué. Vous devez d’abord [définir l’allocation du trafic](#update-deployment-traffic-allocation) de ce déploiement à 0 % pour pouvoir le supprimer.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Points de terminaison**.
1. Sélectionnez votre point de terminaison en ligne managé.
1. Dans la page des détails du point de terminaison, recherchez le déploiement à supprimer.
1. Sélectionnez l’**icône de suppression**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser des points de terminaison en ligne managés Azure Machine Learning. Consultez ces prochaines étapes :

- [Que sont les points de terminaison ?](concept-endpoints.md)
- [Guide pratique pour déployer des points de terminaison en ligne managés avec Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Guide pratique pour superviser des points de terminaison en ligne managés](how-to-monitor-online-endpoints.md)
