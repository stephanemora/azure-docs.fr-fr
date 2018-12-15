---
title: Fonctions dans Azure Log Analytics | Microsoft Docs
description: Cet article décrit comment utiliser des fonctions pour appeler une requête à partir d’une autre requête dans Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884791"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Utilisation de fonctions dans Azure Monitor Log Analytics

> [!NOTE]
> Vous devez suivre [Bien démarrer avec le portail Analytics](get-started-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Pour utiliser une requête Log Analytics avec une autre requête, vous pouvez l’enregistrer en tant que fonction. Cela vous permet de simplifier les requêtes complexes en les divisant en plusieurs parties et de réutiliser le code commun avec plusieurs requêtes.

## <a name="create-a-function"></a>Créer une fonction

Créez une fonction dans le portail Azure en cliquant sur **Enregistrer**, puis en fournissant les informations indiquées dans le tableau suivant.

| Paramètre | Description |
|:---|:---|
| NOM           | Nom d’affichage de la requête dans l’**Explorateur de requêtes**. |
| Enregistrer sous        | Fonction |
| Alias de fonction | Nom court pour utiliser la fonction dans d’autres requêtes. Ne peut pas contenir d’espaces et doit être unique. |
| Catégorie       | Catégorie pour organiser les fonctions et les requêtes enregistrées dans l’**Explorateur de requêtes**. |

> [!NOTE]
> Une fonction dans Log Analytics ne peut pas contenir une autre fonction.

> [!NOTE]
> L’enregistrement d’une fonction est possible dans les requêtes Log Analytics, mais pas dans les requêtes Application Insights pour l’instant.



## <a name="use-a-function"></a>Utiliser une fonction
Utilisez une fonction en incluant son alias dans une autre requête. Elle peut être utilisée comme toute autre table.

## <a name="example"></a>Exemples
L’exemple de requête suivante retourne toutes les mises à jour de sécurité manquantes signalées le dernier jour. Enregistrez cette requête en tant que fonction avec l’alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Créez une autre requête et référencez la fonction _security_updates_last_day_ pour rechercher les mises à jour de sécurité nécessaires concernant SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres leçons pour l’utilisation du langage de requête Log Analytics :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Jointures](joins.md)
- [Graphiques](charts.md)
