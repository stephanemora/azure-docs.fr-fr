---
title: Query Performance Insight dans Azure Database for MariaDB
description: Cet article décrit la fonctionnalité Query Performance Insight dans Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48ff1fdc08e0df463ec48fd1415c7b67d5beb744
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462099"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Query Performance Insight dans Azure Database for MariaDB

**S’applique à :**  Azure Database for MariaDB 10.2

> [!NOTE]
> Query Performance Insight est en préversion.

L’analyse des performances des requêtes vous aide à identifier rapidement vos requêtes les plus longues, comment elles évoluent au fil du temps et les attentes qui les affectent.

## <a name="common-scenarios"></a>Scénarios courants

### <a name="long-running-queries"></a>Requêtes longues

- Identification des requêtes dont l’exécution est la plus longue au cours des X dernières heures
- Identification des N premières requêtes en attente de ressources
 
### <a name="wait-statistics"></a>Statistiques d’attente

- Compréhension de la nature de l’attente pour une requête
- Fonctionnement des tendances des attentes de ressources en cas de conflit de ressource

## <a name="permissions"></a>Autorisations

**Les autorisations Propriétaire** ou **Contributeur** sont requises pour afficher le texte des requêtes dans la fonction Query Performance Insight. **Un utilisateur Lecteur** peut afficher des graphiques et tables, mais non le texte des requêtes.

## <a name="prerequisites"></a>Prérequis

Pour que Query Performance Insight puisse fonctionner, le  [Magasin des requêtes](concepts-query-store.md) doit inclure des données.

## <a name="viewing-performance-insights"></a>Affichage d’une analyse des performances

La vue [Analyse des performances des requêtes](concepts-query-performance-insight.md) du portail Azure montre des visualisations sur les informations importantes du magasin des requêtes.

Sur la page du portail de votre serveur Azure Database for MariaDB, sélectionnez **Query Performance Insight** sous la section **Performances intelligentes** de la barre de menus.

### <a name="long-running-queries"></a>Requêtes longues

L’onglet  **Requêtes longues** montre les 5 requêtes dont la durée moyenne d’exécution est la plus longue, agrégées par intervalles de 15 minutes. Vous pouvez afficher plus de requêtes en faisant une sélection dans la liste déroulante **Nombre de requêtes** . Quand vous faites cela, les couleurs du graphique peuvent changer pour un ID de requête spécifique.

Vous pouvez cliquer et faire glisser dans le graphique pour réduire l’affichage à une fenêtre de temps spécifique. Vous pouvez également utiliser les icônes Zoom avant et Zoom arrière pour afficher une période plus courte ou plus longue, respectivement.

![Requêtes longues d’analyse des performances des requêtes](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Statistiques d’attente 

> [!NOTE]
> Les statistiques d’attente sont destinées à la résolution des problèmes de performances des requêtes. Nous vous recommandons de ne les activer qu’à des fins de résolution des problèmes.

Les statistiques d’attente fournissent une vue des événements d’attente qui se produisent pendant l’exécution d’une requête spécifique. Pour en savoir plus sur les types d’événements d’attente, consultez la [documentation du moteur MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Sélectionnez l’onglet  **Statistiques d’attentes** pour afficher les visualisations correspondantes sur les temps d’attente dans le serveur.

Les requêtes affichées dans la vue des statistiques d’attente sont regroupées en fonction des requêtes qui présentent les temps d’attente les plus longs au cours de l’intervalle indiqué.

![Statistiques sur les attentes de l’analyse des performances des requêtes](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database for MariaDB.