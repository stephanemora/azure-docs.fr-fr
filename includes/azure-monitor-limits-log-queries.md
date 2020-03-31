---
title: Fichier Include
description: Fichier Include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795795"
---
| Limite | Description |
|:---|:---|
| Langage de requête | Azure Monitor utilise le même [langage de requête Kusto](/azure/kusto/query/) qu’Azure Data Explorer. Consultez [Différences propres au langage de requête de journal d’Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) pour les éléments du langage KQL non pris en charge dans Azure Monitor. |
| Régions Azure | Les requêtes sur les journaux peuvent connaître une surcharge excessive quand des données concernent des espaces de travail Log Analytics dans plusieurs régions Azure. Pour plus d’informations, consultez [Limites des requêtes](../articles/azure-monitor/log-query/scope.md#query-limits). |
| Requêtes inter-ressources | Nombre maximal de ressources Application Insights et d’espaces de travail Log Analytics dans une requête unique limitée à 100.<br>Les requêtes inter-ressources ne sont pas prises en charge dans le Concepteur de vue.<br>Les requêtes inter-ressources des alertes de journal sont prises en charge par la nouvelle API scheduledQueryRules.<br>Pour plus de détails, voir [Limites de requête inter-ressources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits). |
| Limitation des requêtes | L’utilisateur est limité à 200 requêtes par tranche de 30 secondes, sur un nombre illimité d’espaces de travail. Cette limite s’applique aux requêtes de programmation ou aux requêtes lancées par des composants de visualisation, tels que les tableaux de bord Azure ou la page récapitulative de l’espace de travail Log Analytics. |
