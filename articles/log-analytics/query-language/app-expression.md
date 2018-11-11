---
title: Expression app() dans une requête Azure Log Analytics | Microsoft Docs
description: L’expression app est utilisée dans une requête Log Analytics dans le but de récupérer des données à partir d’une application Application Insights spécifique du même groupe de ressources, d’un autre groupe de ressources ou d’un autre abonnement.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: dda137ecba91e3ac59be635eed22d18e5bcadcce
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277305"
---
# <a name="app-expression-in-log-analytics-query"></a>Expression app() dans une requête Log Analytics

L’expression `app` est utilisée dans une requête Log Analytics dans le but de récupérer des données à partir d’une application Application Insights spécifique du même groupe de ressources, d’un autre groupe de ressources ou d’un autre abonnement. Elle est particulièrement utile pour inclure des données d’application dans une requête Log Analytics et pour interroger des données de plusieurs applications dans une requête Application Insights.



## <a name="syntax"></a>Syntaxe

`app(`*Identificateur*`)`


## <a name="arguments"></a>Arguments

- L’*identificateur* permet d’identifier l’application à l’aide de l’un des formats du tableau ci-dessous.

| Identificateur | Description | Exemples
|:---|:---|:---|
| Nom de la ressource | Nom lisible de l’application (également appelé « nom du composant ») | app("fabrikamapp") |
| Nom qualifié | Nom complet de l’application au format : « nom_abonnement/groupe_ressources/nom_composant » | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | GUID de l’application | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID de la ressource Azure | Identificateur de la ressource Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notes

* Vous devez disposer d’un accès en lecture à l’application.
* L’identification d’une application par son nom suppose que celui-ci soit unique parmi tous les abonnements accessibles. Si plusieurs applications portent le nom spécifié, la requête échoue en raison de l’ambiguïté. Dans ce cas, vous devez utiliser l’un des autres identificateurs.
* Utilisez l’expression associée [workspace](workspace-expression.md) pour interroger plusieurs espaces de travail Log Analytics.

## <a name="examples"></a>Exemples

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’[expression workspace](workspace-expression.md) pour référence à l’espace de travail Log Analytics.
- En savoir plus sur le stockage des [données Log Analytics](../../log-analytics/log-analytics-queries.md).