---
title: 'Didacticiel : Superviser et optimiser - Azure Database pour PostgreSQL - Serveur unique'
description: Ce tutoriel porte sur la supervision et l’optimisation dans Azure Database pour PostgreSQL (serveur unique).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: d1958c6ef0f7ed52e939967b5e82886fe1373ed8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774735"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Didacticiel : Superviser et optimiser Azure Database pour PostgreSQL (serveur unique)

Azure Database pour PostgreSQL a des fonctionnalités qui vous aident à comprendre et à améliorer les performances de votre serveur. Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Activer la collecte des statistiques sur les requêtes et les attentes
> * Accéder aux données collectées et les utiliser
> * Consulter les statistiques sur les performances des requêtes et les attentes au fil du temps
> * Analyser une base de données pour obtenir des recommandations sur les performances
> * Appliquer les recommandations en matière de performances

## <a name="before-you-begin"></a>Avant de commencer
Vous avez besoin d’un serveur Azure Database pour PostgreSQL avec PostgreSQL version 9.6 ou 10. Vous pouvez suivre les étapes du [tutoriel consacré à la création](tutorial-design-database-using-azure-portal.md) pour créer un serveur.

> [!IMPORTANT]
> **Magasin des requêtes**, **Analyse des performances des requêtes** et **Recommandation sur les performances** sont en préversion publique.

## <a name="enabling-data-collection"></a>Activation de la collecte des données
Le [Magasin des requêtes](concepts-query-store.md) capture un historique des statistiques sur les requêtes et les attentes sur votre serveur, et les stocke dans la base de données **azure_sys** sur votre serveur. Il s’agit d’une fonctionnalité avec option d’adhésion. Pour l’activer :

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database pour PostgreSQL.

3. Sélectionnez **Paramètres du serveur** qui se trouve dans la section **Paramètres** du menu de gauche.

4. Définissez **pg_qs.query_capture_mode** sur **TOP** pour démarrer la collecte des données de performances des requêtes. Définissez **pgms_wait_sampling.query_capture_mode** sur **ALL** pour démarrer la collecte des statistiques d’attente. Enregistrez.
   
   ![Paramètres du serveur de magasin des requêtes](./media/tutorial-performance-intelligence/query-store-parameters.png)

5. Autoriser jusqu’à 20 minutes de conservation pour le premier lot de données dans la base de données **azure_sys**.


## <a name="performance-insights"></a>Insights des performances
La vue [Analyse des performances des requêtes](concepts-query-performance-insight.md) du portail Azure montre des visualisations sur les informations importantes du magasin des requêtes. 

1. Dans la page du portail de votre serveur Azure Database pour PostgreSQL, sélectionnez **Analyse des performances des requêtes** sous la section **Support + dépannage** du menu de gauche.

2. L’onglet **Requêtes longues** montre les 5 requêtes dont la durée moyenne d’exécution est la plus longue, agrégées par intervalles de 15 minutes. 
   
   ![Page d’accueil de l’analyse des performances des requêtes](./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png)

   Vous pouvez afficher plus de requêtes en faisant une sélection dans la liste déroulante **Nombre de requêtes**. Quand vous faites cela, les couleurs du graphique peuvent changer pour un ID de requête spécifique.

3. Vous pouvez cliquer et faire glisser dans le graphique pour réduire l’affichage à une fenêtre de temps spécifique.

4. Utilisez les icônes Zoom avant et Zoom arrière pour afficher respectivement une période plus courte ou plus longue.

5. Consultez le tableau sous le graphique pour découvrir plus d’informations sur les requêtes longues dans cette fenêtre de temps.

6. Sélectionnez l’onglet **Statistiques sur les attentes** pour voir les visualisations correspondantes sur les temps d’attente dans le serveur.
   
   ![Statistiques sur les attentes de l’analyse des performances des requêtes](./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png)

### <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour voir le texte des requêtes dans l’analyse des performances des requêtes. Un **Lecteur** peut voir les graphiques et les tableaux, mais pas le texte des requêtes.


## <a name="performance-recommendations"></a>Recommandations en matière de performances
La fonctionnalité [Recommandations sur les performances](concepts-performance-recommendations.md) analyse les charges de travail sur votre serveur pour identifier les index ayant le potentiel pour améliorer les performances.

1. Ouvrez **Recommandations sur les performances** à partir de la section **Support + dépannage** de la barre de menus sur la page du portail Azure pour votre serveur PostgreSQL.
   
   ![Page d’accueil des recommandations sur les performances](./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png)

2. Sélectionnez **Analyser** et choisissez une base de données. Ceci démarre l’analyse.

3. En fonction de votre charge de travail, cette opération peut prendre plusieurs minutes. Une fois l’analyse terminée, une notification apparaît dans le portail.

4. La fenêtre **Recommandations sur les performances** affiche une liste de recommandations si des recommandations ont été trouvées. 

5. Une recommandation montre des informations sur les éléments **Base de données**, **Table**, **Colonne** et **Taille de l’index** pertinents.

   ![Résultat des recommandations sur les performances](./media/tutorial-performance-intelligence/performance-recommendations-result.png)

6. Pour implémenter la recommandation, copiez le texte de la requête et exécutez-le à partir du client de votre choix.

### <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour exécuter l’analyse avec la fonctionnalité Recommandations sur les performances.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database pour PostgreSQL.