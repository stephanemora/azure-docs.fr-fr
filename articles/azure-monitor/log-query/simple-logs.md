---
title: Expérience Journaux simples dans Azure Monitor (préversion) | Microsoft Docs
description: L’expérience Journaux simples vous permet de créer des requêtes de base dans Azure Monitor sans interagir directement avec KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92929270"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Expérience Journaux simples dans Azure Monitor (préversion)
Azure Monitor offre une [expérience riche](get-started-portal.md) pour la création de [requêtes de journal](log-query-overview.md) à l’aide du langage KQL. Toutefois, vous pouvez ne pas avoir besoin de toute la puissance de KQL et préférer une expérience simplifiée pour des exigences de requête de base. L’expérience Journaux simples vous permet de créer des requêtes de base sans interagir directement avec KQL. Vous pouvez également utiliser Journaux simples comme outil d’apprentissage pour KQL, car vous avez besoin de requêtes plus sophistiquées.

> [!NOTE]
> La fonctionnalité Journaux simples est actuellement implémentée en tant que test uniquement pour Cosmos DB et Key Vault. Partagez votre expérience avec Microsoft via [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) pour nous aider à déterminer si nous allons développer et publier cette fonctionnalité.


## <a name="scope"></a>Étendue
L’expérience Journaux simples récupère des données des tables *AzureDiagnostics* , *AzureMetrics* et *AzureActivity* pour la ressource sélectionnée. 

## <a name="using-simple-logs"></a>Utilisation de Journaux simples
Accédez à Cosmos DB ou Key Vault dans votre abonnement Azure avec les [paramètres de diagnostic configurés pour collecter les journaux dans un espace de travail Log Analytics](../platform/resource-logs.md#send-to-azure-storage). Cliquez sur **Journaux** dans le menu **Monitoring** (Supervision) pour ouvrir l’expérience Journaux simples.

![Capture d’écran montrant le menu Supervision avec l’option Journaux sélectionnée.](media/simple-logs/menu.png)

Sélectionnez un **champ** et un **opérateur** , puis spécifiez une **valeur** pour la comparaison. Cliquez sur **+** et spécifiez **Et/Ou** pour ajouter des critères supplémentaires.

![Capture d’écran montrant la Recherche dans le volet des journaux avec l’option Journaux simples sélectionnée.](media/simple-logs/criteria.png)

Cliquez sur **Exécuter** pour afficher les résultats de la requête.

## <a name="view-and-edit-kql"></a>Afficher et modifier KQL
Sélectionnez **Éditeur de requête** pour ouvrir le KQL généré par la requête Journaux simples dans l’expérience Log Analytics complète. 

![Éditeur de requête](media/simple-logs/query-editor.png)

Vous pouvez modifier directement le KQL et utiliser d’autres fonctionnalités de Log Analytics telles que des filtres pour affiner vos résultats.

![Modifier KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Étapes suivantes

- Suivre un tutoriel sur l’[utilisation de Log Analytics dans le portail Azure](get-started-portal.md).
- Suivre un tutoriel sur l’[écriture de requêtes de journal](get-started-portal.md).
