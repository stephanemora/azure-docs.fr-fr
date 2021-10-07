---
title: Métadonnées et traçabilité depuis Azure Synapse Analytics
description: Cet article explique comment connecter Azure Synapse Analytics et Azure Purview pour suivre la traçabilité des données.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 02ea61356157c7e2a421c736a1af5bd87a1bcb5c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212334"
---
# <a name="how-to-get-lineage-from-azure-synapse-analytics-into-azure-purview"></a>Comment obtenir la traçabilité des données depuis Azure Synapse Analytics dans Azure Purview

Ce document explique les étapes nécessaires pour connecter un espace de travail Azure Synapse à un compte Azure Purview afin de suivre la traçabilité des données. Il décrit également en détail l’étendue de la couverture et les fonctionnalités de traçabilité prises en charge.

## <a name="supported-azure-synapse-capabilities"></a>Fonctionnalités Azure Synapse prises en charge

Actuellement, Azure Purview capture la traçabilité d’exécution des activités de pipeline Azure Synapse suivantes :

- [Copier les données](../data-factory/copy-activity-overview.md?context=/azure/synapse-analytics/context/context)
- [Flux de données](../data-factory/concepts-data-flow-overview.md?context=/azure/synapse-analytics/context/context)

> [!IMPORTANT]
> Azure Purview supprime la traçabilité si la source ou la destination utilise un système de stockage de données non pris en charge.

[!INCLUDE[azure-synapse-supported-activity-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

## <a name="access-secured-azure-purview-account"></a>Accéder à un compte Azure Purview sécurisé
      
Si votre compte Purview est protégé par un pare-feu, découvrez comment autoriser Azure Synapse à [accéder à un compte Purview sécurisé](../synapse-analytics/catalog-and-governance/how-to-access-secured-purview-account.md) via des points de terminaison privés Purview.

## <a name="bring-azure-synapse-lineage-into-purview"></a>Intégrer la traçabilité Azure Synapse dans Purview

### <a name="step-1-connect-azure-synapse-workspace-to-your-purview-account"></a>Étape 1 : Connecter l’espace de travail Azure Synapse à votre compte Purview

Vous pouvez connecter un espace de travail Azure Synapse à Purview, et la connexion permet à Azure Synapse d’envoyer des informations de traçabilité à Purview. Suivez les étapes de la section [Connecter l'espace de travail Synapse à Azure Purview](../synapse-analytics/catalog-and-governance/quickstart-connect-azure-purview.md). Plusieurs espaces de travail Azure Synapse peuvent se connecter à un même compte Azure Purview pour un suivi de traçabilité complet.

### <a name="step-2-run-pipeline-in-azure-synapse-workspace"></a>Étape 2 : Exécuter le pipeline dans l’espace de travail Azure Synapse

Vous pouvez créer des pipelines avec l’activité de copie dans l’espace de travail Azure synapse. Vous n’avez pas besoin de configuration supplémentaire pour la capture de données de traçabilité. Les données de traçabilité sont automatiquement capturées lors de l’exécution des activités.

### <a name="step-3-monitor-lineage-reporting-status"></a>Étape 3 : Surveiller l’état des rapports de traçabilité

Après avoir exécuté le pipeline Azure Synapse, dans la vue de supervision du pipeline Synapse, vous pouvez vérifier l’état du rapport de traçabilité en sélectionnant le bouton **État de la traçabilité** suivant. Les mêmes informations sont également disponibles dans le JSON de la sortie de l’activité -> section `reportLineageToPurvew`.

:::image type="content" source="../data-factory/media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="Superviser l’état du rapport de traçabilité dans la vue de supervision du pipeline.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Étape 4 : Afficher les informations de traçabilité dans votre compte Purview

Dans votre compte Purview, vous pouvez parcourir les ressources et choisir le type « Azure Synapse Analytics ». Vous pouvez également effectuer une recherche dans le catalogue des données en utilisant des mots clés.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-assets.png" alt-text="Parcourir les ressources Azure Synapse dans Purview."

Sélectionnez le compte Synapse -> pipeline -> activité pour visualiser les informations de traçabilité.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png" alt-text="Parcourir la traçabilité du pipeline Azure Synapse dans Purview." lightbox="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png":::

## <a name="next-steps"></a>Étapes suivantes

[Guide de l’utilisateur sur la traçabilité Data Catalog](catalog-lineage-user-guide.md)

[Lien vers Azure Data Share pour la traçabilité](how-to-link-azure-data-share.md)
