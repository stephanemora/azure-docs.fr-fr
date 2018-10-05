---
title: Référence sur le langage Log Analytics Azure Monitor | Microsoft Docs
description: Informations de référence sur le langage Kusto utilisé par Log Analytics. Inclut des éléments supplémentaires spécifiques à Log Analytics et des éléments non pris en charge dans les requêtes Log Analytics.
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
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451066"
---
# <a name="log-analytics-query-language-reference"></a>Référence sur le langage de requête Log Analytics
Les [requêtes Log Analytics](../log-analytics-queries.md) utilisent les mêmes moteur et langage de requête que [l’Explorateur de données Azure](/azure/data-explorer/). Vous pouvez accéder à la référence de langage et à d’autres détails sur le langage à partir de l’emplacement suivant : [Overview](/azure/kusto/query) (Vue d’ensemble)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Éléments Kusto non pris en charge dans Log Analytics
Tandis que les requêtes Log Analytics utilisent une implémentation de Kusto, certains éléments Kusto ne sont pas pris en charge, comme décrit dans les sections suivantes.

### <a name="statements-not-supported-in-log-analytics"></a>Instructions non prises en charge dans Log Analytics

* [Alias](/kusto/query/aliasstatement)
* [Paramètres de requête](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Fonctions non prises en charge dans Log Analytics

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Opérateurs non pris en charge dans Log Analytics

* [Jointure entre plusieurs clusters](/azure/kusto/query/joincrosscluster)
* [opérateur externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Plug-ins non pris en charge dans Log Analytics

* [plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Opérateurs supplémentaires dans Log Analytics
Pour prendre en charge des fonctionnalités Log Analytics spécifiques, les opérateurs Kusto supplémentaires ci-après, non disponibles en dehors de Log Analytics, sont fournis. 

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Étapes suivantes

- Documentez-vous sur les requêtes dans [Log Analytics](../log-analytics-queries.md).
- Passez en revue une leçon sur l’écriture sur une [requête Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Accédez à la [documentation de référence de Kusto](/azure/kusto/query/) complète.