---
title: Analyse des performances des requêtes dans Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit la fonctionnalité Analyse des performances des requêtes dans Azure Database pour PostgreSQL - Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d45b79e2ca3b3d478102bebdcff3c8892bef2cb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067554"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**S’applique à :** Azure Database pour PostgreSQL - Serveur unique 9.6 et 10

L’analyse des performances des requêtes vous aide à identifier rapidement vos requêtes les plus longues, comment elles évoluent au fil du temps et les attentes qui les affectent.

## <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour voir le texte des requêtes dans l’analyse des performances des requêtes. Un **Lecteur** peut voir les graphiques et les tableaux, mais pas le texte des requêtes.

## <a name="prerequisites"></a>Prérequis
Pour que l’analyse des performances des requêtes puisse fonctionner, des données doivent exister dans le [Magasin des requêtes](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Affichage d’une analyse des performances
La vue [Analyse des performances des requêtes](concepts-query-performance-insight.md) du portail Azure montre des visualisations sur les informations importantes du magasin des requêtes. 

Sur la page du portail de votre serveur Azure Database pour PostgreSQL, sélectionnez **Analyse des performances des requêtes** sous la section **Performances intelligentes** de la barre de menus.

![Requêtes longues d’analyse des performances des requêtes](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

L’onglet **Requêtes longues** montre les 5 requêtes dont la durée moyenne d’exécution est la plus longue, agrégées par intervalles de 15 minutes. Vous pouvez afficher plus de requêtes en faisant une sélection dans la liste déroulante **Nombre de requêtes**. Quand vous faites cela, les couleurs du graphique peuvent changer pour un ID de requête spécifique.

Vous pouvez cliquer et faire glisser dans le graphique pour réduire l’affichage à une fenêtre de temps spécifique. Vous pouvez également utiliser les icônes Zoom avant et Zoom arrière pour afficher respectivement une période plus courte ou plus longue.

Le tableau sous le graphique fournit plus d’informations sur les requêtes longues dans cette fenêtre de temps.

Sélectionnez l’onglet **Statistiques sur les attentes** pour voir les visualisations correspondantes sur les temps d’attente dans le serveur.

![Statistiques sur les attentes de l’analyse des performances des requêtes](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database pour PostgreSQL.


