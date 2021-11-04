---
title: 'Démarrage rapide : Utiliser Synapse Studio pour surveiller vos pools Data Explorer (préversion)'
description: Cet article fournit des informations sur la surveillance des pools Data Explorer.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: d127abb6e8b493e0db3a281083d9ca17802cf529
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097520"
---
# <a name="quickstart-use-synapse-studio-to-monitor-your-data-explorer-pools-preview"></a>Démarrage rapide : Utiliser Synapse Studio pour surveiller vos pools Data Explorer (préversion)

Avec Azure Synapse Analytics, vous pouvez utiliser Data Explorer pour exécuter des requêtes, des tableaux de bord et plus d’informations sur les pools Data Explorer dans votre espace de travail.

Cet article explique comment surveiller vos pools Data Explorer, ce qui vous permet de garder un œil sur leur état, notamment le nombre de vCores utilisés par les différents utilisateurs de l’espace de travail.

## <a name="access-data-explorer-pools-list"></a>Accéder à la liste des pools Data Explorer

Pour afficher la liste des pools Data Explorer dans votre espace de travail, commencez par [ouvrir Synapse Studio](https://web.azuresynapse.net/), puis sélectionnez votre espace de travail.

![Se connecter à l’espace de travail](../monitoring/media/common/login-workspace.png)

Une fois que vous avez ouvert votre espace de travail, sélectionnez la section **Monitor** sur la gauche.

![Sélectionner un hub Monitor](../monitoring/media/common/left-nav.png)

Sélectionnez **Pools Data Explorer** pour afficher la liste des pools Data Explorer.

![Sélectionner des pools Data Explorer](media/monitor-data-explorer-pools/monitor-hub-nav-data-explorer-pools.png)

## <a name="filter-your-data-explorer-pools"></a>Filtrer vos pools Data Explorer

Vous pouvez filtrer la liste des pools Data Explorer pour retenir ceux qui vous intéressent. Les filtres en haut de l’écran vous permettent de spécifier un champ sur lequel filtrer.

Par exemple, vous pouvez filtrer l’affichage pour voir uniquement les pools Data Explorer contenant le nom « test » :

![Exemple de filtre](media/monitor-data-explorer-pools/filter-example.png)

## <a name="view-details-about-a-specific-data-explorer-pool"></a>Afficher les détails d’un pool Data Explorer spécifique

Pour afficher les détails relatifs à l’un de vos pools Data Explorer, sélectionnez le pool Data Explorer pour afficher les détails.

![Détails du pool Data Explorer](media/monitor-data-explorer-pools/data-explorer-pool-details.png)
