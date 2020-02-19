---
title: Stocker les sauvegardes jusqu’à une durée de 10 ans
description: Découvrez comment Azure SQL Database prend en charge le stockage des sauvegardes complètes de bases de données jusqu’à une durée de 10 ans.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 9c5534f2df4a375daf355d74f788b7f610f92919
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162155"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Stocker les sauvegardes Azure SQL Database jusqu’à une durée de 10 ans

De nombreuses applications sont dédiées à la réglementation, à la conformité ou à d’autres fins professionnelles qui vous obligent à conserver des sauvegardes de données au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) Azure SQL Database. À l’aide de la fonctionnalité de conservation à long terme (LTR), vous pouvez stocker des sauvegardes complètes de bases de données SQL dans un stockage Blob Azure en disposant d’un stockage géoredondant avec accès en lecture pour une période allant jusqu’à 10 ans. Vous pouvez ensuite restaurer la sauvegarde de votre choix en tant que nouvelle base de données. Pour plus d’informations sur la redondance du Stockage Azure, consultez [Redondance du Stockage Azure](../storage/common/storage-redundancy.md).

> [!NOTE]
> La conservation à long terme (LTR) peut être activée pour les bases de données uniques et mises en pool. Elle n’est pas encore disponible pour les bases de données dans des instances managées. Vous pouvez utiliser des travaux SQL Agent pour planifier des [sauvegardes de base de données en copie seule](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) comme alternative à la conservation à long terme au-delà de 35 jours.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Mode de fonctionnement de la rétention à long terme SQL Database

La rétention des sauvegardes à long terme (LTR) s’appuie sur les sauvegardes intégrales des bases de données qui sont [créées automatiquement](sql-database-automated-backups.md) pour permettre la récupération jusqu’à une date et heure. Si une stratégie LTR est spécifiée, ces sauvegardes sont copiées vers différents objets blob pour stockage à long terme. L’opération de copie est une tâche en arrière-plan qui n’a aucun impact de performances sur la charge de travail de la base de données. Les sauvegardes LTR sont conservées pendant une période de temps définie par la stratégie LTR. La stratégie LTR de chaque base de données SQL peut également spécifier la fréquence à laquelle les sauvegardes LTR sont créées. Pour parvenir à une telle flexibilité, vous pouvez définir cette stratégie à l’aide d’une combinaison de quatre paramètres : rétention des sauvegardes hebdomadaire (W), rétention des sauvegardes mensuelle (M), rétention des sauvegardes annuelle (Y) et semaine de l’année (WeekOfYear). Si vous indiquez W, une sauvegarde est copiée sur le dispositif de stockage à long terme toutes les semaines. Si vous indiquez M, une sauvegarde est copiée sur le dispositif de stockage à long terme la première semaine du mois. Si vous indiquez Y, une sauvegarde est copiée sur le dispositif de stockage à long terme pendant la semaine définie par WeekOfYear. Toutes les sauvegardes sont conservées sur le dispositif de stockage à long terme pendant la durée définie par ces paramètres. Toute modification apportée à la stratégie LTR s’applique aux sauvegardes futures. Par exemple, si la valeur WeekOfYear spécifiée est dans le passé lorsque la stratégie est configurée, la première sauvegarde LTR sera créée l’année suivante. 

Exemples de stratégie LTR :

-  W=0, M=0, Y=5, WeekOfYear=3

   La troisième sauvegarde complète de l’année est conservée pendant cinq ans.
   
- W=0, M=3, Y=0

   La première sauvegarde complète du mois est conservée pendant trois mois.

- W=12, M=0, Y=0

   Chaque sauvegarde complète hebdomadaire est conservée pendant 12 semaines.

- W=6, M=12, Y=10, WeekOfYear=16

   Chaque sauvegarde complète hebdomadaire est conservée pendant six semaines. à l’exception de la 1re sauvegarde complète du mois qui est conservée pendant 12 mois et de la sauvegarde complète effectuée durant la 16e semaine de l’année et qui est quant à elle conservée pendant 10 ans. 

Le tableau suivant illustre la cadence et l’expiration des sauvegardes à long terme pour la stratégie suivante :

W=12 semaines (84 jours), M=12 mois (365 jours), Y=10 ans (3 650 jours), WeekOfYear=15 (semaine après le 15 avril)

   ![Par exemple](./media/sql-database-long-term-retention/ltr-example.png)



Si vous modifiez la stratégie ci-dessus et définissez W=0 (aucune sauvegarde hebdomadaire), la cadence des copies de sauvegarde sera modifiée comme indiqué dans le tableau ci-dessus par les dates en surbrillance. La quantité de stockage nécessaire pour conserver ces sauvegardes diminuerait en conséquence. 

> [!IMPORTANT]
> Le calendrier des sauvegardes LTR individuelles est contrôlé par Azure SQL Database. Vous ne pouvez pas créer manuellement une sauvegarde LTR ni contrôler le calendrier de création de sauvegarde. Après avoir configuré une stratégie de rétention à long terme (LTR), vous devrez peut-être patienter jusqu’à sept jours avant que la première sauvegarde LTR n’apparaisse dans la liste des sauvegardes disponibles.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Géo-réplication et conservation de sauvegarde à long terme

Si vous utilisez une géoréplication active ou des groupes de basculement en tant que solution de continuité des activités métier, vous devez vous préparer à des basculements éventuels et configurer la même stratégie LTR sur la base de données géosecondaire. Le coût de votre stockage LTR n’augmente pas, car les sauvegardes ne sont pas générées à partir des bases de données secondaires. Les sauvegardes sont créées uniquement lorsque la base de données secondaire devient primaire. Cela garantit une génération ininterrompue des sauvegardes LTR lorsque le basculement est déclenché et lorsque la base de données primaire est déplacée vers la région secondaire. 

> [!NOTE]
> Lorsque la base de données primaire d’origine récupère après une panne ayant entraîné le basculement, elle devient une nouvelle base de données secondaire. Par conséquent, la création de sauvegarde ne reprend pas, et la stratégie de conservation à long terme existante ne prend effet qu’après que la base de données est redevenue primaire. 

## <a name="configure-long-term-backup-retention"></a>Configurer la rétention des sauvegardes à long terme

Pour apprendre à configurer la conservation à long terme via le Portail Azure ou PowerShell, consultez [Gérer la conservation à long terme des sauvegardes Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Restaurer la base de données à partir de la sauvegarde LTR

Pour restaurer une base de données à partir du stockage LTR, vous pouvez sélectionner une sauvegarde spécifique en fonction de son horodatage. Vous pouvez restaurer la base de données sur n’importe quel serveur existant, en utilisant le même abonnement que celui de la base de données d’origine. Pour apprendre à restaurer votre base de données à partir d’une sauvegarde LTR à l’aide du Portail Azure ou de PowerShell, consultez [Gérer la conservation à long terme des sauvegardes Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Dans la mesure où les sauvegardes de base de données protègent les données des corruptions et des suppressions accidentelles, elles sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence. Pour en savoir plus sur les autres solutions de continuité des activités SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
