---
title: Analyse des performances des requêtes dans Azure Database pour PostgreSQL
description: Cet article décrit la fonctionnalité Analyse des performances des requêtes dans Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2019
ms.openlocfilehash: 56abdd819e78312e64209078c3966826385df7bc
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620731"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**S’applique à :** Azure Database pour PostgreSQL 9.6 et 10

L’analyse des performances des requêtes vous aide à identifier rapidement vos requêtes les plus longues, comment elles évoluent au fil du temps et les attentes qui les affectent.

## <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour voir le texte des requêtes dans l’analyse des performances des requêtes. Un **Lecteur** peut voir les graphiques et les tableaux, mais pas le texte des requêtes.

## <a name="prerequisites"></a>Conditions préalables
Pour que l’analyse des performances des requêtes puisse fonctionner, des données doivent exister dans le [Magasin des requêtes](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Affichage d’une analyse des performances
La vue [Analyse des performances des requêtes](concepts-query-performance-insight.md) du portail Azure montre des visualisations sur les informations importantes du magasin des requêtes. 

Dans la page du portail de votre serveur Azure Database pour PostgreSQL, sélectionnez **Insight de performances des requêtes** sous le **performances Intelligent** section de la barre de menus.

![Requêtes longues d’analyse des performances des requêtes](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Le **longues requêtes** onglet affiche les requêtes de cinq principales par durée moyenne par exécution, agrégées par intervalles de 15 minutes. Vous pouvez afficher plus de requêtes en faisant une sélection dans la liste déroulante **Nombre de requêtes**. Quand vous faites cela, les couleurs du graphique peuvent changer pour un ID de requête spécifique.

Vous pouvez cliquer et faire glisser dans le graphique pour réduire l’affichage à une fenêtre de temps spécifique. Vous pouvez également utiliser les icônes Zoom avant et Zoom arrière pour afficher respectivement une période plus courte ou plus longue.

Le tableau sous le graphique fournit plus d’informations sur les requêtes longues dans cette fenêtre de temps.

Sélectionnez l’onglet **Statistiques sur les attentes** pour voir les visualisations correspondantes sur les temps d’attente dans le serveur.

![Query Performance Insight attend des statistiques](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database pour PostgreSQL.


