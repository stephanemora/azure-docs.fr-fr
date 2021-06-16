---
title: Récupérer votre base de données Oracle sur Azure BareMetal Infrastructure
description: Découvrez comment récupérer votre base de données Oracle sur Azure BareMetal Infrastructure.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: c93159476403ac01ea9622b438fb36e5c423ecb4
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578479"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Récupérer votre base de données Oracle sur Azure BareMetal Infrastructure

Bien qu’aucune technologie unique ne protège contre tous les scénarios de défaillance, la combinaison de fonctionnalités offre aux administrateurs de base de données la possibilité de récupérer leur base de données dans pratiquement n’importe quelle situation.

## <a name="causes-of-database-failure"></a>Causes de défaillance de la base de données

Les défaillances de base de données peuvent se produire pour de nombreuses raisons, mais elles appartiennent généralement à l’une des catégories suivantes :

- Erreurs de manipulation de données
- Perte de journaux de phase de restauration par progression en ligne
- Perte de fichiers de contrôle de base de données
- Perte de fichiers de données de base de données
- Altération des données physiques

## <a name="choose-your-method-of-recovery"></a>Choisir votre méthode de récupération

Le type de récupération dépend du type de défaillance. Supposez qu’un objet soit supprimé ou que les données ne soient pas modifiées correctement. La solution la plus rapide consiste généralement à effectuer une opération Flashback Database. Dans d’autres cas, la récupération par le biais d’une capture instantanée Azure NetApp Files peut être suffisante. L’arbre de décision de la figure suivante représente les scénarios courants de défaillance et de récupération si toutes les options de protection des données décrites ci-dessus sont implémentées.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagramme de l’arbre de décision de récupération de base de données." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Gardez à l’esprit que cet exemple d’arbre de décision est valable uniquement du point de vue d’un administrateur de base de données. Chaque déploiement peut avoir des exigences différentes susceptibles de changer l’ordre des choix. Par exemple, l’exécution d’un basculement de rôle de base de données vers une autre région par le biais de Data Guard peut avoir un effet néfaste sur les performances de l’application. Cela pourrait donner un RTO inférieur pour la méthode de récupération par instantané. Pour être sûr de répondre aux exigences de RTO/RPO, nous vous recommandons d’effectuer ces opérations et de créer des procédures documentées afin de les exécuter en cas de nécessité.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur BareMetal Infrastructure :

- [Qu’est-ce que BareMetal Infrastructure sur Azure ?](../../concepts-baremetal-infrastructure-overview.md)
- [Connecter des instances BareMetal Infrastructure dans Azure](../../connect-baremetal-infrastructure.md)
