---
title: Stocker les sauvegardes Azure SQL Database pendant 10 ans | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge le stockage des sauvegardes complètes de bases de données pendant 10 ans.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: b2f3c454ba84c7b892096cc42dcbe2706ab6159f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648290"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Stocker les sauvegardes Azure SQL Database pendant 10 ans

De nombreuses applications sont dédiées à la réglementation, à la conformité ou à d’autres fins professionnelles qui vous obligent à conserver des sauvegardes de données au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) Azure SQL Database. À l’aide de la fonctionnalité de rétention à long terme (LTR), vous pouvez stocker pendant 10 ans des sauvegardes complètes de bases de données SQL dans un stockage Blob [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Vous pouvez ensuite restaurer la sauvegarde de votre choix en tant que nouvelle base de données.

> [!IMPORTANT]
> La fonctionnalité de rétention à long terme est actuellement proposée en version préliminaire. Les sauvegardes existantes stockées dans le coffre Azure Services Recovery Service dans le cadre de la préversion précédente de cette fonctionnalité sont migrées vers le stockage SQL Azure.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Mode de fonctionnement de la rétention à long terme SQL Database

La rétention des sauvegardes à long terme s’appuie sur les [sauvegardes SQL Database automatiques](sql-database-automated-backups.md) créées avec limite de restauration dans le temps. Vous pouvez configurer une stratégie de rétention à long terme pour chaque base de données SQL et spécifier la fréquence à laquelle vous devez copier les sauvegardes sur le dispositif de stockage à long terme. Pour parvenir à une telle flexibilité, vous pouvez définir cette stratégie à l’aide d’une combinaison de quatre paramètres : rétention des sauvegardes hebdomadaire (W), rétention des sauvegardes mensuelle (M), rétention des sauvegardes annuelle (Y) et Semaine de l’année (WeekOfYear). Si vous indiquez W, une sauvegarde est copiée sur le dispositif de stockage à long terme toutes les semaines. Si vous indiquez M, une sauvegarde est copiée sur le dispositif de stockage à long terme la première semaine du mois. Si vous indiquez Y, une sauvegarde est copiée sur le dispositif de stockage à long terme pendant la semaine définie par WeekOfYear. Toutes les sauvegardes sont conservées sur le dispositif de stockage à long terme pendant la durée définie par ces paramètres. 

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

## <a name="configure-long-term-backup-retention"></a>Configurer la rétention des sauvegardes à long terme

Pour apprendre à configurer la rétention à long terme via le Portail Azure ou à l’aide de PowerShell, consultez [Configurer la rétention des sauvegardes à long terme](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Étapes suivantes

Dans la mesure où les sauvegardes de base de données protègent les données des corruptions et des suppressions accidentelles, elles sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence. Pour en savoir plus sur les autres solutions de continuité des activités SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
