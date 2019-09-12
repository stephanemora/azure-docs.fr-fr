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
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "69657771"
---
| Limite | Description |
|:---|:---|
| Langage de requête | Azure Monitor utilise le même [langage de requête Kusto](/azure/kusto/query/) qu’Azure Data Explorer. Consultez [Différences propres au langage de requête de journal d’Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) pour les éléments du langage KQL non pris en charge dans Azure Monitor. |
| Régions Azure | Les requêtes sur les journaux peuvent connaître une surcharge excessive quand des données concernent des espaces de travail Log Analytics dans plusieurs régions Azure. Pour plus d’informations, consultez [Limites des requêtes](../articles/azure-monitor/log-query/scope.md#query-limits). |
| Requêtes inter-ressources | Nombre maximal de ressources Application Insights et d’espaces de travail Log Analytics dans une requête unique limitée à 100.<br>Les requêtes inter-ressources ne sont pas prises en charge dans le Concepteur de vue.<br>Les requêtes inter-ressources des alertes de journal sont prises en charge par la nouvelle API scheduledQueryRules.<br>Pour plus de détails, voir [Limites de requête inter-ressources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits). |