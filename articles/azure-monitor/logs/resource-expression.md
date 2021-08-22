---
title: Expression resource() dans une requête de journal Azure Monitor | Microsoft Docs
description: L’expression resource est utilisée dans une requête de journal Azure Monitor centrée sur les ressources pour récupérer des données de plusieurs ressources.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2021
ms.openlocfilehash: cf49ee6f0913721d3c71c0d42e5f11a2aeeb2025
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122527991"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Expression resource() dans une requête de journal Azure Monitor

L’expression `resource` est utilisée dans une requête Azure Monitor [limitée à une ressource](scope.md#query-scope) pour récupérer des données d’autres ressources. 


## <a name="syntax"></a>Syntaxe

`resource(`*Identificateur*`)`

## <a name="arguments"></a>Arguments

- *Identifier* : ID d’une ressource.

| Identificateur | Description | Exemple
|:---|:---|:---|
| Ressource | Inclut des données relatives à la ressource. | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Groupe de ressources ou abonnement | Inclut des données relatives à la ressource et à toutes les ressources qu’il contient.  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Notes

* Vous devez disposer d’un accès en lecture à la ressource.


## <a name="examples"></a>Exemples

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’étendue d’une requête, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](scope.md).
- Accédez à la documentation complète sur le [langage de requête Kusto](/azure/kusto/query/).
