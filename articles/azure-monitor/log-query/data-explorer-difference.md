---
title: Référence sur le langage Log Analytics Azure Monitor | Microsoft Docs
description: Informations de référence sur le langage de requête de l’Explorateur de données utilisé par Log Analytics. Inclut des éléments supplémentaires spécifiques à Log Analytics et des éléments non pris en charge dans les requêtes Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 645750ec40f0aba2ef58c096a72125fad2947719
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186257"
---
# <a name="log-analytics-query-language-differences"></a>Différences dans le langage de requête Log Analytics

Bien que [Log Analytics](log-query-overview.md) repose sur [l’Explorateur de données Azure](/azure/data-explorer) et utilise le [même langage de requête](/azure/kusto/query), la version du langage contient des différences. Cet article identifie les éléments qui diffèrent entre la version du langage utilisée pour l’Explorateur de données et la version utilisée pour les requêtes Log Analytics.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Éléments de l’Explorateur de données non pris en charge dans Log Analytics
Les sections suivantes décrivent les éléments du langage de requête de l’Explorateur de données qui ne sont pas pris en charge par Log Analytics.

### <a name="statements-not-supported-in-log-analytics"></a>Instructions non prises en charge dans Log Analytics

* [Alias](/azure/kusto/query/aliasstatement)
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
Les opérateurs suivants prennent en charge des fonctionnalités Log Analytics spécifiques et ne sont pas disponibles en dehors de Log Analytics.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des références aux différentes [ressources pour l’écriture de requêtes Log Analytics](query-language.md).
- Accédez à la [documentation de référence sur le langage de requête de l’Explorateur de données](/azure/kusto/query/) complète.
