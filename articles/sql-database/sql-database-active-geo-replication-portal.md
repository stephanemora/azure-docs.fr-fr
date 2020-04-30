---
title: 'Tutoriel : Géoréplication et basculement dans le portail'
description: Configurez la géoréplication pour une base de données unique ou mise en pool dans Azure SQL Database à l’aide du portail Azure et initiez le basculement.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 59616fb217b28a8c47d9a5d13e2f4c1b9a8f6bb3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605225"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configurer la géoréplication active pour Azure SQL Database dans le portail Azure et initier le basculement

Cet article montre comment configurer la [géoréplication active pour les bases de données uniques ou mises en pool](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) dans Azure SQL Database via le [portail Azure](https://portal.azure.com) et initier le basculement.

Pour plus d’informations sur les groupes de basculement automatique avec les bases de données uniques et mises en pool, consultez [Meilleures pratiques d’utilisation des groupes de basculement avec des bases de données uniques et mises en pool](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Pour plus d’informations sur les groupes de basculement automatique avec Managed Instance, consultez [Meilleures pratiques d’utilisation des groupes de basculement avec des instances gérées](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Prérequis

Pour configurer la géo-réplication active à l’aide du portail Azure, vous devez disposer des ressources suivantes :

* Une base de données Azure SQL : la base de données primaire que vous souhaitez répliquer vers une autre région géographique.

> [!Note]
> Lorsque vous utilisez le portail Azure, pour créer une base de données secondaire, vous devez utiliser l'abonnement associé à la base de données principale. Si la base de données secondaire doit être associée à un autre abonnement, utilisez l'[API REST Créer une base de données](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) ou l'[API Transact-SQL ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Ajouter une base de données secondaire

Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat géo-réplication.  

Pour ajouter une base de données secondaire, vous devez être le propriétaire ou copropriétaire de l’abonnement.

La base de données secondaire a le même nom que la base de données primaire et, par défaut, le même niveau de service et la même taille de calcul. La base de données secondaire peut être une base de données unique ou une base de données mise en pool. Pour plus d’informations, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).
Une fois la base de données secondaire créée et amorcée, une réplication des données de la base de données primaire vers la base de données secondaire commence.

> [!NOTE]
> Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géo-réplication précédente), la commande échoue.

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la géo-réplication.
2. Dans la page SQL Database, sélectionnez **Géoréplication**, puis choisissez la région dans laquelle créer la base de données secondaire. Bien que vous puissiez sélectionner n’importe quelle région autre que la région qui héberge la base de données primaire, nous vous recommandons de sélectionner la [région jumelée](../best-practices-availability-paired-regions.md).

    ![Configuration de la géo-réplication](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Sélectionnez ou configurez le serveur et le niveau tarifaire pour la base de données secondaire.

    ![Configuration de la base de données secondaire](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool données élastique. Pour créer la base de données secondaire dans un pool, cliquez sur **Pool élastique**, puis sélectionnez un pool sur le serveur cible. Un pool doit déjà exister sur le serveur cible. Ce workflow ne crée pas un pool.
5. Cliquez sur **créer** pour ajouter la base de données secondaire.
6. La base de données secondaire est créée et le processus d’amorçage commence.

    ![Configuration de la base de données secondaire](./media/sql-database-geo-replication-portal/seeding0.png)
7. Lorsque le processus d’amorçage est terminé, la base de données secondaire affiche son état.

    ![Amorçage terminé](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initialisation d’un basculement

La base de données secondaire peut être basculée en base de données primaire.  

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Dans le panneau SQL Database, sélectionnez **Tous les paramètres** > **Géoréplication**.
3. Dans la liste **SECONDAIRES**, sélectionnez la base de données qui doit devenir la nouvelle base primaire et cliquez sur **Basculement forcé**.

    ![failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Cliquez sur **Oui** pour commencer le basculement.

La commande fait immédiatement basculer la base de données secondaire vers le rôle primaire. Ce processus doit normalement se terminer dans un délai de 30 secondes ou moins.

Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales.

> [!NOTE]
> Cette commande est conçue pour récupérer rapidement la base de données en cas de panne. Elle déclenche un basculement sans synchronisation des données (basculement forcé).  Si la base de données primaire est en ligne et valide des transactions lorsque la commande est émise, une perte de données peut se produire.

## <a name="remove-secondary-database"></a>Supprimer une base de données secondaire

Cette opération arrête définitivement la réplication vers la base de données secondaire et modifie le rôle de la base de données secondaire en une base de données normale accessible en lecture et en écriture. Si la connectivité à la base de données secondaire est interrompue, la commande aboutit, mais la base de données secondaire ne passe pas en mode lecture-écriture une fois la connectivité rétablie.  

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Dans la page SQL Database, sélectionnez **Géoréplication**.
3. Dans la liste des bases de données **SECONDAIRES**, sélectionnez la base de données que vous souhaitez supprimer du partenariat de géo-réplication.
4. Cliquez sur **Arrêter la réplication**.

    ![Suppression de la base de données secondaire](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Une fenêtre de confirmation s’ouvre. Cliquez sur **Oui** pour supprimer la base de données de partenariat de géo-réplication (définissez-la sur une base de données en lecture-écriture ne faisant pas partie d’une réplication).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la géoréplication active, voir [Géoréplication active](sql-database-active-geo-replication.md).
* Pour en savoir plus sur les groupes de basculement automatique, consultez [Groupes de basculement automatique](sql-database-auto-failover-group.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
