---
title: Stocker les sauvegardes Azure SQL Database pendant 10 ans | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge le stockage des sauvegardes complètes de bases de données pendant 10 ans.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 0f764ebbad53185f46c7166011e05493ed261d6a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696656"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Stocker les sauvegardes Azure SQL Database pendant 10 ans

De nombreuses applications sont dédiées à la réglementation, à la conformité ou à d’autres fins professionnelles qui vous obligent à conserver des sauvegardes de données au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) Azure SQL Database. À l’aide de la fonctionnalité de rétention à long terme (LTR), vous pouvez stocker pendant 10 ans des sauvegardes complètes de bases de données SQL dans un stockage Blob [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Vous pouvez ensuite restaurer la sauvegarde de votre choix en tant que nouvelle base de données.

> [!NOTE]
> La conservation à long terme (LTR) peut être activée pour les bases de données uniques et mises en pool. Elle n’est pas encore disponible pour les bases de données dans des instances managées. Vous pouvez utiliser des travaux SQL Agent pour planifier des [sauvegardes de base de données en copie seule](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) comme alternative à la conservation à long terme au-delà de 35 jours.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Mode de fonctionnement de la rétention à long terme SQL Database

La rétention des sauvegardes à long terme (LTR) s’appuie sur les sauvegardes intégrales des bases de données qui sont [créées automatiquement](sql-database-automated-backups.md) pour permettre la récupération jusqu’à une date et heure. Si une stratégie de rétention à long terme est configurée, ces sauvegardes sont copiées dans différents objets BLOB pour le stockage à long terme. L’opération de copie est une tâche en arrière-plan qui n’a aucun impact de performances sur la charge de travail de base de données. Les sauvegardes de rétention à long terme sont conservés pendant une période de temps définie par la stratégie de rétention à long terme. La stratégie de rétention à long terme pour chaque base de données SQL peut également spécifier la fréquence à laquelle les sauvegardes de rétention à long terme sont créées. Pour activer cette flexibilité, vous pouvez définir la stratégie à l’aide d’une combinaison de quatre paramètres : rétention des sauvegardes hebdomadaire (W), rétention des sauvegardes mensuelle (M), rétention des sauvegardes annuelle (Y) et la semaine de l’année (WeekOfYear). Si vous indiquez W, une sauvegarde est copiée sur le dispositif de stockage à long terme toutes les semaines. Si vous indiquez M, une sauvegarde est copiée sur le dispositif de stockage à long terme la première semaine du mois. Si vous indiquez Y, une sauvegarde est copiée sur le dispositif de stockage à long terme pendant la semaine définie par WeekOfYear. Toutes les sauvegardes sont conservées sur le dispositif de stockage à long terme pendant la durée définie par ces paramètres. Toute modification de la stratégie de rétention à long terme s’applique aux futures sauvegardes. Par exemple, si le WeekOfYear spécifié est dans le passé lorsque la stratégie est configurée, la première sauvegarde de rétention à long terme sera créée l’année prochaine. 

Exemples de la stratégie de rétention à long terme :

-  W=0, M=0, Y=5, WeekOfYear=3

   La troisième sauvegarde complète de chaque année sera conservée pendant cinq ans.
   
- W=0, M=3, Y=0

   La première sauvegarde complète de chaque mois est conservée pendant trois mois.

- W=12, M=0, Y=0

   Chaque sauvegarde complète hebdomadaire est conservée pendant 12 semaines.

- W=6, M=12, Y=10, WeekOfYear=16

   Chaque sauvegarde complète hebdomadaire est conservée pendant six semaines. à l’exception de la 1re sauvegarde complète du mois qui est conservée pendant 12 mois et de la sauvegarde complète effectuée durant la 16e semaine de l’année et qui est quant à elle conservée pendant 10 ans. 

Le tableau suivant illustre la cadence et l’expiration des sauvegardes à long terme pour la stratégie suivante :

W=12 semaines (84 jours), M=12 mois (365 jours), Y=10 ans (3 650 jours), WeekOfYear=15 (semaine après le 15 avril)

   ![Par exemple](./media/sql-database-long-term-retention/ltr-example.png)



Si vous modifiez la stratégie ci-dessus et définir W = 0 (aucune sauvegarde hebdomadaire), la cadence des copies de sauvegarde change comme indiqué dans le tableau ci-dessus par les dates en surbrillance. La quantité de stockage nécessaire pour conserver ces sauvegardes diminuerait en conséquence. 

> [!NOTE]
> Le minutage des sauvegardes de rétention à long terme individuels est contrôlé par la base de données SQL Azure. Vous ne pouvez pas manuellement créer une sauvegarde de rétention à long terme ou contrôler le minutage de la création de la sauvegarde.
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Géo-réplication et conservation de sauvegarde à long terme

Si vous utilisez la géo-réplication active ou groupes de basculement en tant que votre solution de continuité, vous devez préparer pour les basculements éventuelle et configurer la même stratégie de rétention à long terme sur la base de données de géo-secondaire. Le coût de stockage de rétention à long terme augmentera pas à mesure que les sauvegardes ne sont pas générés à partir de bases de données secondaires. Les sauvegardes sont créées uniquement lorsque la base de données secondaire devient primaire. Il permet de s’assurer non interrompu la génération des sauvegardes de rétention à long terme lorsque le basculement est déclenché et le réplica principal se déplace vers la région secondaire. 

> [!NOTE]
> Lors de la base de données primaire d’origine récupère après une panne qui a provoqué le basculement, il deviendra une nouvelle base de données secondaire. Par conséquent, la création de sauvegarde ne reprend pas, et la stratégie de conservation à long terme existante ne prend effet qu’après que la base de données est redevenue primaire. 

## <a name="configure-long-term-backup-retention"></a>Configurer la rétention des sauvegardes à long terme

Pour savoir comment configurer la rétention à long terme à l’aide du portail Azure ou PowerShell, consultez [rétention de sauvegarde à long terme de gérer une base de données Azure SQL](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Restaurer la base de données à partir de la sauvegarde de rétention à long terme

Pour restaurer une base de données à partir du stockage LTR, vous pouvez sélectionner une sauvegarde spécifique en fonction de son horodatage. Vous pouvez restaurer la base de données sur n’importe quel serveur existant, en utilisant le même abonnement que celui de la base de données d’origine. Pour savoir comment restaurer votre base de données à partir d’une sauvegarde de rétention à long terme, à l’aide du portail Azure ou PowerShell, consultez [rétention de sauvegarde à long terme de gérer une base de données Azure SQL](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Dans la mesure où les sauvegardes de base de données protègent les données des corruptions et des suppressions accidentelles, elles sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence. Pour en savoir plus sur les autres solutions de continuité des activités SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
