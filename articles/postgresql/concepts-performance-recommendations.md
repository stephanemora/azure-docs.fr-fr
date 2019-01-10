---
title: Recommandations sur les performances dans Azure Database pour PostgreSQL
description: Cet article décrit les recommandations sur les performances qu’il est possible d’obtenir dans Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 1dedc08f27d1a483290dc61cce879290ca66592d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548090"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recommandations sur les performances dans Azure Database pour PostgreSQL

**S’applique à :** Azure Database pour PostgreSQL 9.6 et 10

> [!IMPORTANT]
> La fonctionnalité Recommandations sur les performances est en préversion publique.

La fonctionnalité Recommandations sur les performances identifie les principaux index qui peuvent être créés dans votre serveur Azure Database pour PostgreSQL pour améliorer les performances. Pour produire des recommandations d’index, la fonctionnalité prend en compte différentes caractéristiques de base de données, notamment son schéma et la charge de travail comme indiqué par le Magasin des requêtes. Après avoir implémenté une recommandation sur les performances, les clients doivent tester les performances pour évaluer l’impact des changements. 

## <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour exécuter l’analyse avec la fonctionnalité Recommandations sur les performances.

## <a name="performance-recommendations"></a>Recommandations en matière de performances
La fonctionnalité [Recommandations sur les performances](concepts-performance-recommendations.md) analyse les charges de travail sur votre serveur pour identifier les index ayant le potentiel pour améliorer les performances.

Ouvrez **Recommandations sur les performances** à partir de la section **Support + dépannage** de la barre de menus sur la page du portail Azure pour votre serveur PostgreSQL.

![Page d’accueil des recommandations sur les performances](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Sélectionnez **Analyser** et choisissez une base de données. Cette opération démarre l’analyse. En fonction de votre charge de travail, cela peut prendre plusieurs minutes. Une fois l’analyse terminée, une notification apparaît dans le portail.

La fenêtre **Recommandations sur les performances** affiche une liste de recommandations si des recommandations ont été trouvées. Une recommandation montre des informations sur les éléments **Base de données**, **Table**, **Colonne** et **Taille de l’index** pertinents.

![Nouvelle page Recommandations sur les performances](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Pour implémenter la recommandation, copiez le texte de la requête et exécutez-le à partir du client de votre choix.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database pour PostgreSQL.

