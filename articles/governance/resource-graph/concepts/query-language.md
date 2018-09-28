---
title: Présentation du langage de requête Azure Resource Graph
description: Décrit le fonctionnement du langage de requête pour Azure Resource Graph.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951948"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Présentation du langage de requête Azure Resource Graph

Le langage de requête pour Azure Resource Graph prend en charge un certain nombre d’opérateurs et de fonctions. Ils fonctionnent et opèrent de manière similaire au langage de requête Kusto (KQL). Cependant, il est important de comprendre qu’en dépit des similitudes qui existent entre le langage de requête Resource Graph et [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators), ils ne sont pas identiques.

> [!NOTE]
> Les liens permettant d’accéder à la documentation KQL peuvent nécessiter une authentification.

Le meilleur moyen d’appréhender le langage de requête employé par Resource Graph est de commencer par consulter la documentation KQL. Vous pourrez ainsi comprendre la façon dont le langage est structuré et comment les divers opérateurs et fonctions pris en charge fonctionnent ensemble.

## <a name="supported-tabular-operators"></a>Opérateurs tabulaires pris en charge

Voici la liste des opérateurs tabulaires pris en charge dans Resource Graph :

- [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [extend](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [limit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [order by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [project](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [project-away](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [sample](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [sample-distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [sort by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [take](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [top](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [top-nested](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [top-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>Fonctions prises en charge

Voici la liste des fonctions prises en charge dans Resource Graph :

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [tostring()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>Étapes suivantes

- Voir l’utilisation du langage dans les [requêtes de démarrage](../samples/starter.md)
- Voir les utilisations avancées dans les [Requêtes avancées](../samples/advanced.md)
- Apprendre à [explorer les ressources](explore-resources.md)