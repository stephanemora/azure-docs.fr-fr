---
title: Analyse des performances des requêtes - Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit la fonctionnalité Analyse des performances des requêtes dans Azure Database pour PostgreSQL - Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a7573e99de96e3d13509056c49bc24be7fe166bf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906519"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**S’applique à :** Azure Database pour PostgreSQL - Serveur unique versions 9.6, 10, 11

L’analyse des performances des requêtes vous aide à identifier rapidement vos requêtes les plus longues, comment elles évoluent au fil du temps et les attentes qui les affectent.

## <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour voir le texte des requêtes dans l’analyse des performances des requêtes. Un **Lecteur** peut voir les graphiques et les tableaux, mais pas le texte des requêtes.

## <a name="prerequisites"></a>Prérequis
Pour que l’analyse des performances des requêtes puisse fonctionner, des données doivent exister dans le [Magasin des requêtes](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Affichage d’une analyse des performances
La vue [Analyse des performances des requêtes](concepts-query-performance-insight.md) du portail Azure montre des visualisations sur les informations importantes du magasin des requêtes. 

Sur la page du portail de votre serveur Azure Database pour PostgreSQL, sélectionnez **Analyse des performances des requêtes** sous la section **Performances intelligentes** de la barre de menus.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Requêtes longues d’analyse des performances des requêtes":::

L’onglet **Requêtes longues** montre les 5 requêtes dont la durée moyenne d’exécution est la plus longue, agrégées par intervalles de 15 minutes. Vous pouvez afficher plus de requêtes en faisant une sélection dans la liste déroulante **Nombre de requêtes**. Quand vous faites cela, les couleurs du graphique peuvent changer pour un ID de requête spécifique.

Vous pouvez cliquer et faire glisser dans le graphique pour réduire l’affichage à une fenêtre de temps spécifique. Vous pouvez également utiliser les icônes Zoom avant et Zoom arrière pour afficher respectivement une période plus courte ou plus longue.

Le tableau sous le graphique fournit plus d’informations sur les requêtes longues dans cette fenêtre de temps.

Sélectionnez l’onglet **Statistiques sur les attentes** pour voir les visualisations correspondantes sur les temps d’attente dans le serveur.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Statistiques sur les attentes de l’analyse des performances des requêtes":::

## <a name="considerations"></a>Considérations
* Query Performance Insight n’est pas disponible pour les [réplicas en lecture](concepts-read-replicas.md).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database pour PostgreSQL.


