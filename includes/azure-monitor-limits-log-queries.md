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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405671"
---
| Limite | Description |
|:---|:---|
| Langage de requête | Azure Monitor utilise le même [langage de requête Kusto](/azure/kusto/query/) qu’Azure Data Explorer. Consultez [Différences propres au langage de requête de journal d’Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) pour les éléments du langage KQL non pris en charge dans Azure Monitor. |
| Régions Azure | Les requêtes sur les journaux peuvent connaître une surcharge excessive quand des données concernent des espaces de travail Log Analytics dans plusieurs régions Azure. Pour plus d’informations, consultez [Limites des requêtes](../articles/azure-monitor/log-query/scope.md#query-limits). |
