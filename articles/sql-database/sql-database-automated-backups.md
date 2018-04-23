---
title: Sauvegardes Azure SQL Database automatiques ou géoredondantes | Microsoft Docs
description: SQL Database crée automatiquement une sauvegarde de base de données locale toutes les cinq minutes et utilise le stockage géoredondant avec accès en lecture pour fournir la géoredondance.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ab1793621950fd57d3f0be545772d85b32f5d7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="learn-about-automatic-sql-database-backups"></a>En savoir plus sur les sauvegardes automatiques SQL Database

SQL Database crée automatiquement des sauvegardes de base de données et utilise le stockage géoredondant avec accès en lecture (RA-GRS) d’Azure pour fournir la géoredondance. Ces sauvegardes sont créées automatiquement et sans frais supplémentaires. Vous n’avez pas besoin de faire quoi que ce soit pour qu’elles se produisent. Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Si vous souhaitez conserver les sauvegardes dans votre propre conteneur de stockage, vous pouvez configurer une stratégie de rétention de sauvegarde à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Qu’est-ce qu’une sauvegarde SQL Database ?

SQL Database utilise la technologie SQL Server pour créer des sauvegardes [complètes](https://msdn.microsoft.com/library/ms186289.aspx), [différentielles](https://msdn.microsoft.com/library/ms175526.aspx) et du [journal des transactions](https://msdn.microsoft.com/library/ms191429.aspx) dans le cadre de la limite de restauration dans le temps. Les sauvegardes du journal des transactions se produisent, en général, toutes les 5 à 10 minutes. La fréquence varie selon le niveau de performance et l’activité de la base de données. Les sauvegardes du journal des transactions, avec les sauvegardes complètes et différentielles, vous permettent de restaurer une base de données à un point spécifique sur le même serveur qui héberge la base de données. Quand vous restaurez une base de données, le service identifie les sauvegardes nécessitant une restauration (complète, différentielle ou journal des transactions).


Vous pouvez utiliser ces sauvegardes aux fins suivantes :

* Restaure une base de données à un point dans le temps dans la période de rétention. Cette opération crée une base de données dans le même serveur que la base de données d’origine.
* Restaurer une base de données supprimée sur le moment où elle été supprimée ou tout moment pendant la période de rétention. La base de données supprimée ne peut être restaurée que sur le même serveur où la base de données d’origine a été créée.
* Restaurer une base de données dans une autre région géographique. Ceci vous permet de procéder à la récupération après un sinistre géographique lorsque vous ne pouvez pas accéder à votre serveur, ni à la base de données. Cette opération crée une base de données sur n’importe quel serveur existant dans le monde entier. 
* Restaurer une base de données à partir d’une sauvegarde à long terme spécifique si la base de données a été configurée avec une stratégie de rétention à long terme. Ceci vous permet de restaurer une ancienne version de la base de données pour répondre à une demande de conformité ou exécuter une ancienne version de l’application. Consultez [Rétention à long terme](sql-database-long-term-retention.md).
* Pour effectuer une restauration, consultez [Restauration de la base de données à partir de la sauvegarde](sql-database-recovery-using-backups.md).

> [!NOTE]
> Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire. 
> 

## <a name="how-often-do-backups-happen"></a>À quelle fréquence les sauvegardes se produisent-elles ?
Les sauvegardes complètes de base de données sont effectuées chaque semaine, les sauvegardes différentielles de base de données sont, en général, effectuées à quelques heures d’intervalle et les sauvegardes du journal des transactions sont effectuées toutes les 5 à 10 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps s’il s’agit d’une base de données de taille considérable. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de données. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. Le moment exact de toutes les sauvegardes de base de données est déterminé par le service SQL Database en fonction de l’équilibrage de la charge de travail globale du système. 

La géoréplication du stockage de sauvegarde se produit en fonction de la planification de réplication de Stockage Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Combien de temps conservez-vous mes sauvegardes ?
Chaque sauvegarde SQL Database a une période de rétention qui est basée sur le [niveau de service](sql-database-service-tiers.md) de la base de données. Les périodes de rétention pour une base de données sont les suivantes :


* Niveau de service De base : 7 jours.
* Niveau de service Standard : 35 jours.
* Niveau de service Premium : 35 jours.
* Pour le niveau Usage général, une période de 35 jours maximum peut être configurée (7 jours par défaut)*.
* Pour le niveau Critique pour l’entreprise (préversion), une période de 35 jours maximum peut être configurée (7 jours par défaut)*.

\* Pendant la préversion, la période de rétention des sauvegardes n’est pas configurable et est fixée à 7 jours.

Si vous convertissez une base de données en une base de données présentant une période de rétention plus courte, toutes les sauvegardes plus anciennes que la période de rétention du niveau cible ne sont plus disponibles.

Si vous mettez à niveau une base de données vers une base de données présentant une période de rétention plus longue, SQL Database conserve les sauvegardes existantes jusqu’à ce que la période de rétention en question soit atteinte. 

Si vous supprimez une base de données, SQL Database conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, supposons que vous supprimez une base de données qui a une période de rétention de sept jours. Une sauvegarde dont l’ancienneté est de quatre jours est conservée trois jours de plus.

> [!IMPORTANT]
> Si vous supprimez le serveur Azure SQL Server, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. Vous ne pouvez pas restaurer un serveur supprimé.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Comment étendre la période de rétention de la sauvegarde ?

Si votre application nécessite que les sauvegardes soient disponibles pendant une période plus longue que la limite maximale de restauration dans le temps, vous pouvez configurez une stratégie de rétention des sauvegardes à long terme pour des bases de données individuelles (stratégie LTR). Cela permet d’étendre la période de rétention intégrée de 35 jours maximum à 10 ans. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

Une fois que vous ajoutez la stratégie LTR à une base de données à l’aide de l’API ou du portail Azure, les sauvegardes de base de données complètes hebdomadaires sont copiées automatiquement dans un conteneur de stockage RA-GRS distinct aux fins de rétention à long terme. Si votre base de données est chiffrée à l’aide de TDE, les sauvegardes sont automatiquement chiffrées au repos. SQL Database supprime automatiquement vos sauvegardes ayant expiré en fonction de leur horodatage et de la stratégie LTR. Une fois la stratégie configurée, vous n’avez pas besoin de gérer la planification des sauvegardes ni de vous préoccuper du nettoyage des anciens fichiers. Vous pouvez utiliser le portail Azure ou PowerShell pour afficher, restaurer ou supprimer ces sauvegardes.

## <a name="are-backups-encrypted"></a>Les sauvegardes sont-elles chiffrées ?

Lorsque le TDE est activé pour une base de données SQL Azure, les sauvegardes sont également chiffrées. Le TDE est configuré par défaut sur l’ensemble des nouvelles bases de données SQL Azure. Pour en savoir plus sur le TDE, consultez la page [Chiffrement transparent des données avec Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>Les sauvegardes automatiques respectent-elles les exigences du RGPD ?
Si la sauvegarde contient des données personnelles, qui sont soumises au Règlement général sur la protection des données (RGPD), vous devez appliquer des mesures de sécurité renforcées pour protéger les données contre tout accès non autorisé. Pour vous conformer au RGPD, vous devez pouvoir gérer les demandes de données des propriétaires de données sans avoir à accéder aux sauvegardes.  Pour les sauvegardes à court terme, vous pouvez par exemple réduire la fenêtre de sauvegarde à une période inférieure au délai maximal autorisé pour répondre aux demandes d’accès aux données, soit 30 jours.  Si vous avez besoin de sauvegardes à plus long terme, il est recommandé de stocker uniquement des données « pseudonymisées » dans les sauvegardes. Si des données relatives à une personne doivent par exemple être supprimées ou mises à jour, il ne sera alors par nécessaire de supprimer ou de mettre à jour les sauvegardes existantes. Pour plus d’informations sur les meilleures pratiques en matière de RGPD, consultez [Data Governance for GDPR Compliance](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html) (Gouvernance des données pour la conformité au RGPD).

## <a name="next-steps"></a>Étapes suivantes

- Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
- Pour effectuer une restauration à un point dans le temps à l’aide du portail Azure, consultez [Restauration d’une base de données à un point dans le temps à l’aide du portail Azure](sql-database-recovery-using-backups.md).
- Pour effectuer une restauration à un point dans le temps à l’aide de PowerShell, consultez [Restauration d’une base de données à un point dans le temps à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
- Pour configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec le portail Azure, consultez [Gestion de la rétention de sauvegarde à long terme avec le portail Azure](sql-database-long-term-backup-retention-configure.md).
- Pour configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec PowerShell, consultez [Gestion de la rétention de sauvegarde à long terme avec PowerShell](sql-database-long-term-backup-retention-configure.md).
