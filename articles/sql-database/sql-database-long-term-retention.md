---
title: Stocker les sauvegardes Azure SQL Database pendant 10 ans | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge le stockage des sauvegardes complètes de bases de données pendant 10 ans.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/24/2018
ms.openlocfilehash: 7fe34423e706054daf84eaa8baf45fe201a661c9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026175"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Stocker les sauvegardes Azure SQL Database pendant 10 ans

De nombreuses applications sont dédiées à la réglementation, à la conformité ou à d’autres fins professionnelles qui vous obligent à conserver des sauvegardes de données au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) Azure SQL Database. À l’aide de la fonctionnalité de rétention à long terme (LTR), vous pouvez stocker pendant 10 ans des sauvegardes complètes de bases de données SQL dans un stockage Blob [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Vous pouvez ensuite restaurer la sauvegarde de votre choix en tant que nouvelle base de données.

> [!NOTE]
> La conservation à long terme peut être activée sur les bases de données hébergées sur des serveurs logiques Azure SQL Database. Elle n’est pas encore disponible pour les bases de données hébergées dans les instances Managed Instance.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Mode de fonctionnement de la rétention à long terme SQL Database

La rétention des sauvegardes à long terme (LTR) s’appuie sur les sauvegardes intégrales des bases de données qui sont [créées automatiquement](sql-database-automated-backups.md) pour permettre la récupération jusqu’à une date et heure. Ces sauvegardes sont copiées vers différents objets blob de stockage si la stratégie de rétention à long terme a été configurée.
Vous pouvez configurer une stratégie de rétention à long terme pour chaque base de données SQL et spécifier la fréquence de copie des sauvegardes pour les objets blob de stockage à long terme. Pour parvenir à une telle flexibilité, vous pouvez définir cette stratégie à l’aide d’une combinaison de quatre paramètres : rétention des sauvegardes hebdomadaire (W), rétention des sauvegardes mensuelle (M), rétention des sauvegardes annuelle (Y) et Semaine de l’année (WeekOfYear). Si vous indiquez W, une sauvegarde est copiée sur le dispositif de stockage à long terme toutes les semaines. Si vous indiquez M, une sauvegarde est copiée sur le dispositif de stockage à long terme la première semaine du mois. Si vous indiquez Y, une sauvegarde est copiée sur le dispositif de stockage à long terme pendant la semaine définie par WeekOfYear. Toutes les sauvegardes sont conservées sur le dispositif de stockage à long terme pendant la durée définie par ces paramètres. 

Exemples :

-  W=0, M=0, Y=5, WeekOfYear=3

   La 3e sauvegarde complète de l’année est conservée pendant 5 ans.
- W=0, M=3, Y=0

   La 1re sauvegarde complète du mois est conservée pendant 3 mois.

- W=12, M=0, Y=0

   Chaque sauvegarde complète hebdomadaire est conservée pendant 12 semaines.

- W=6, M=12, Y=10, WeekOfYear=16

   Toutes les sauvegardes complètes hebdomadaires sont conservées pendant 6 semaines, à l’exception de la 1re sauvegarde complète du mois qui est conservée pendant 12 mois et de la sauvegarde complète effectuée durant la 16e semaine de l’année et qui est quant à elle conservée pendant 10 ans. 

Le tableau suivant illustre la cadence et l’expiration des sauvegardes à long terme pour la stratégie suivante :

W=12 semaines (84 jours), M=12 mois (365 jours), Y=10 ans (3 650 jours), WeekOfYear=15 (semaine après le 15 avril)

   ![Par exemple](./media/sql-database-long-term-retention/ltr-example.png)


 
Si vous deviez modifier la stratégie ci-dessus et définir W=0 (aucune sauvegarde hebdomadaire), la cadence des copies de sauvegarde pourrait être modifiée comme indiqué dans le tableau ci-dessus par les dates en surbrillance. La quantité de stockage nécessaire pour conserver ces sauvegardes diminuerait en conséquence. 

> [!NOTE]
1. Les copies LTR sont créées par le service de stockage Azure. Ainsi, le processus de copie n’a aucun impact sur les performances de la base de données existante.
2. La stratégie s’applique aux sauvegardes futures. Par exemple, si la valeur WeekOfYear spécifiée est dans le passé lorsque la stratégie est configurée, la première sauvegarde LTR sera créée l’année suivante. 
3. Pour restaurer une base de données à partir du stockage LTR, vous pouvez sélectionner une sauvegarde spécifique en fonction de son horodatage.   Vous pouvez restaurer la base de données sur n’importe quel serveur existant, en utilisant le même abonnement que celui de la base de données d’origine. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Géo-réplication et conservation de sauvegarde à long terme

Si vous utilisez une géoréplication active ou des groupes de basculement en tant que solution de continuité des activités métier, vous devez vous préparer à des basculements éventuels et configurer la même stratégie de conservation à long terme sur la base de données géosecondaire. Cela n’augmente pas le coût de stockage de conservation à long terme, car les sauvegardes ne sont pas générés à partir des bases de données secondaires. Les sauvegardes sont créées uniquement lorsque la base de données secondaire devient primaire. Cela garantit une génération ininterrompue des sauvegardes de conservation à long terme lorsque le basculement est déclenché et lorsque la base de données primaire est déplacée vers la région secondaire. 

> [!NOTE]
Lors de la base de données primaire d’origine récupère après la panne qui a entraîné son basculement, elle devient une nouvelle base de données secondaire. Par conséquent, la création de sauvegarde ne reprend pas, et la stratégie de conservation à long terme existante ne prend effet qu’après que la base de données est redevenue primaire. 
> 

## <a name="configure-long-term-backup-retention"></a>Configurer la rétention des sauvegardes à long terme

Pour apprendre à configurer la rétention à long terme via le Portail Azure ou à l’aide de PowerShell, consultez [Configurer la rétention des sauvegardes à long terme](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Dans la mesure où les sauvegardes de base de données protègent les données des corruptions et des suppressions accidentelles, elles sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence. Pour en savoir plus sur les autres solutions de continuité des activités SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
