---
title: Recommandations sur les performances dans Azure Database pour PostgreSQL
description: Cet article décrit la fonctionnalité de recommandation sur les performances dans Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620170"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recommandations sur les performances dans Azure Database pour PostgreSQL

**S’applique à :** Azure Database pour PostgreSQL 9.6 et 10

La fonctionnalité de recommandations relatives aux performances analyse vos bases de données pour créer des suggestions personnalisées pour améliorer les performances. Pour produire les recommandations, l’analyse examine les différentes caractéristiques de base de données, y compris le schéma. Activer [requête Store](concepts-query-store.md) sur votre serveur pour utiliser pleinement la fonctionnalité de recommandations relatives aux performances. Après avoir implémenté une recommandation de performances, vous devez tester les performances pour évaluer l’impact de ces modifications. 

## <a name="permissions"></a>Autorisations
Les autorisations **Propriétaire** ou **Contributeur** sont nécessaires pour exécuter l’analyse avec la fonctionnalité Recommandations sur les performances.

## <a name="performance-recommendations"></a>Recommandations en matière de performances
La fonctionnalité [Recommandations sur les performances](concepts-performance-recommendations.md) analyse les charges de travail sur votre serveur pour identifier les index ayant le potentiel pour améliorer les performances.

Ouvrez **recommandations relatives aux performances** à partir de la **performances Intelligent** section de la barre de menus sur la page du portail Azure pour votre serveur PostgreSQL.

![Page d’accueil des recommandations sur les performances](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Sélectionnez **analyser** et choisissez une base de données, qui commence l’analyse. Selon votre charge de travail, analyse de th peut prendre plusieurs minutes. Une fois l’analyse terminée, une notification apparaît dans le portail. Analyse effectue une étude approfondie de votre base de données. Nous vous recommandons de qu'effectuer analyse pendant les périodes creuses. 

Le **recommandations** fenêtre affiche une liste de recommandations ont été il en existe.

![Nouvelle page Recommandations sur les performances](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Recommandations ne sont pas appliquées automatiquement. Pour appliquer la recommandation, copiez le texte de requête et exécutez-le à partir du client de votre choix. N’oubliez pas de tester et surveiller pour évaluer la recommandation. 

## <a name="recommendation-types"></a>Types de recommandation

Actuellement, les deux types de recommandations sont prises en charge : *Créer Index* et *Drop Index*.

### <a name="create-index-recommendations"></a>Recommandations de création d’index
*Créer Index* recommandations suggèrent de nouveaux index pour accélérer les requêtes les plus fréquemment exécutées ou du temps dans la charge de travail. Ce type de recommandation requiert [requête Store](concepts-query-store.md) doit être activé. Requête Store collecte des informations de requête et fournit les statistiques de runtime et la fréquence des requêtes détaillées que l’analyse utilise pour apporter la recommandation.

### <a name="drop-index-recommendations"></a>Recommandations de suppression d’index
Outre la détection des index manquants, la base de données Azure pour PostgreSQL analyse les performances des index existants. Si un index est rarement utilisés ou redondantes, l’analyseur recommande sa suppression.


## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus d’informations sur la [surveillance et l’optimisation](concepts-monitoring.md) dans Azure Database pour PostgreSQL.

