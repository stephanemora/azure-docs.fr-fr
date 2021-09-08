---
title: 'Tutoriel : Géoréplication et basculement dans le portail'
description: Découvrez comment configurer la géoréplication pour une base de données SQL avec le portail Azure ou Azure CLI et lancer le basculement.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: e94b9cb0628fca112f3bb9181035d6d60bbd7b74
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866725"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-azure-sql-database"></a>Tutoriel : Configurer la géoréplication active et le basculement (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article montre comment configurer la [géoréplication active pour Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) avec le [portail Azure](https://portal.azure.com) ou Azure CLI et lancer le basculement.

Pour connaître les meilleures pratiques d’utilisation des groupes de basculement automatique, consultez [Meilleures pratiques pour Azure SQL Database](auto-failover-group-overview.md#best-practices-for-sql-database) et [Meilleures pratiques pour Azure SQL Managed Instance](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Prérequis

# <a name="portal"></a>[Portail](#tab/portal)

Pour configurer la géo-réplication active à l’aide du portail Azure, vous devez disposer des ressources suivantes :

* Une base de données dans Azure SQL Database : la base de données primaire que vous souhaitez répliquer vers une autre région géographique.

> [!Note]
> Lorsque vous utilisez le portail Azure, pour créer une base de données secondaire, vous devez utiliser l'abonnement associé à la base de données principale. Si une base de données secondaire doit être associée à un autre abonnement, utilisez l’[API REST Créer une base de données](/rest/api/sql/databases/createorupdate) ou l'[API Transact-SQL ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer la géoréplication active, vous avez besoin d’une base de données dans Azure SQL Database. Il s’agit de la base de données primaire que vous souhaitez répliquer vers une autre région géographique.

Préparez votre environnement pour l’interface Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="add-a-secondary-database"></a>Ajouter une base de données secondaire

Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat géo-réplication.  

Pour ajouter une base de données secondaire, vous devez être le propriétaire ou copropriétaire de l’abonnement.

La base de données secondaire a le même nom que la base de données primaire et, par défaut, le même niveau de service et la même taille de calcul. La base de données secondaire peut être une base de données unique ou une base de données mise en pool. Pour plus d’informations, consultez [Modèle d’achat DTU](service-tiers-dtu.md) et [Modèle d’achat vCore](service-tiers-vcore.md).
Une fois la base de données secondaire créée et amorcée, une réplication des données de la base de données primaire vers la base de données secondaire commence.

> [!NOTE]
> Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géoréplication précédente), la commande échoue.

# <a name="portal"></a>[Portail](#tab/portal)

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la géo-réplication.
2. Dans la page Base de données SQL, sélectionnez votre base de données, accédez à **Gestion des données**, sélectionnez **Réplicas**, puis sélectionnez **Créer un réplica**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-cli-create-geo-replica.png" alt-text="Configurer la géoréplication":::

3. Sélectionnez ou créez le serveur pour la base de données secondaire et configurez les options **Calcul + stockage**, si nécessaire. Vous pouvez sélectionner n’importe quelle région pour votre serveur secondaire, mais nous vous recommandons d’utiliser la [région appairée](../../best-practices-availability-paired-regions.md).

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-create-and-configure-replica.png" alt-text="{alt-text}":::

    Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool données élastique. Pour créer la base de données secondaire dans un pool, sélectionnez **Oui** en regard de **Vous souhaitez utiliser un pool élastique SQL ?** , puis sélectionnez un pool sur le serveur cible. Un pool doit déjà exister sur le serveur cible. Ce workflow ne crée pas un pool.

4. Cliquez sur **Vérifier + créer**, passez en revue les informations, puis cliquez sur **Créer**.
5. La base de données secondaire est créée et le processus de déploiement commence.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-geo-replica-deployment.png" alt-text="Capture d’écran montrant l’état de déploiement de la base de données secondaire.":::

6. Quand le déploiement est terminé, la base de données secondaire affiche son état.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-sql-database-secondary-status.png" alt-text="Capture d’écran montrant l’état de la base de données secondaire après le déploiement.":::

7. Revenez à la page de la base de données primaire, puis sélectionnez **Réplicas**. Votre base de données secondaire est listée sous **Géoreplicas**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-sql-db-geo-replica-list.png" alt-text="Capture d’écran montrant les géoréplicas et la base de données SQL primaire.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Sélectionnez la base de données que vous souhaitez configurer pour la géoréplication. Les informations suivantes sont nécessaires :
- Nom d’origine de votre base de données Azure SQL.
- Nom du serveur Azure SQL.
- Nom de votre groupe de ressources.
- Nom du serveur dans lequel créer le réplica.

> [!NOTE]
> La base de données secondaire doit avoir le même niveau de service que la base de données primaire.

Vous pouvez sélectionner n’importe quelle région pour votre serveur secondaire, mais nous vous recommandons d’utiliser la [région appairée](../../best-practices-availability-paired-regions.md).

Exécutez la commande [az sql db replica create](/cli/azure/sql/db/replica#az_sql_db_replica_create).

```azurecli
az sql db replica create --resource-group ContosoHotel --server contosoeast --name guestlist --partner-server contosowest --family Gen5 --capacity 2 --secondary-type Geo
```

Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool données élastique. Pour créer la base de données secondaire dans un pool, utilisez le paramètre `--elastic-pool`. Un pool doit déjà exister sur le serveur cible. Ce workflow ne crée pas un pool.

La base de données secondaire est créée et le processus de déploiement commence.

Une fois le déploiement terminé, vous pouvez vérifier l’état de la base de données secondaire en exécutant la commande [az sql db replica list-links](/cli/azure/sql/db/replica#az_sql_db_replica_list-links) :
    
```azurecli
az sql db replica list-links --name guestlist --resource-group ContosoHotel --server contosowest
```

---

## <a name="initiate-a-failover"></a>Initialisation d’un basculement

La base de données secondaire peut être basculée en base de données primaire.

# <a name="portal"></a>[Portail](#tab/portal)  

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Accédez à **Gestion des données**, puis sélectionnez **Réplicas**.
3. Dans la liste **Géoréplicas**, sélectionnez la base de données qui doit devenir la nouvelle base de données primaire, sélectionnez les points de suspension, puis sélectionnez **Basculement forcé**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-forced-failover.png" alt-text="Capture d’écran montrant la sélection du basculement forcé dans la liste déroulante.":::
4. Sélectionnez **Oui** pour commencer le basculement.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Exécutez la commande [az sql db replica set-primary](/cli/azure/sql/db/replica#az_sql_db_replica_set-primary).

```azurecli
az sql db replica set-primary --name guestlist --resource-group ContosoHotel --server contosowest
```

---

La commande fait immédiatement basculer la base de données secondaire vers le rôle primaire. Ce processus doit normalement se terminer dans un délai de 30 secondes ou moins.

Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponible, de l’ordre de 0 à 25 secondes, pendant que les rôles sont activés. Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales.

> [!NOTE]
> Cette commande est conçue pour récupérer rapidement la base de données en cas de panne. Elle déclenche un basculement sans synchronisation des données, ou basculement forcé.  Si la base de données primaire est en ligne et valide des transactions lorsque la commande est émise, une perte de données peut se produire.

## <a name="remove-secondary-database"></a>Supprimer une base de données secondaire

Cette opération arrête définitivement la réplication vers la base de données secondaire et change le rôle de la base de données secondaire en une base de données normale accessible en lecture et en écriture. Si la connectivité à la base de données secondaire est interrompue, la commande aboutit, mais la base de données secondaire ne passe pas en mode lecture-écriture une fois la connectivité rétablie.

# <a name="portal"></a>[Portail](#tab/portal)  

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Sélectionnez **Réplicas**.
3. Dans la liste **Géoréplicas**, sélectionnez la base de données que vous souhaitez supprimer du partenariat de géoréplication, sélectionnez les points de suspension, puis sélectionnez **Arrêter la réplication**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-stop-replication.png" alt-text="Capture d’écran montrant la sélection de l’option Arrêter la réplication dans la liste déroulante.":::
5. Une fenêtre de confirmation s’ouvre. Cliquez sur **Oui** pour supprimer la base de données de partenariat de géo-réplication (définissez-la sur une base de données en lecture-écriture ne faisant pas partie d’une réplication).
 
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Exécutez la commande [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete-link).

```azurecli
az sql db replica delete-link --name guestlist --resource-group ContosoHotel --server contosoeast --partner-server contosowest
```

Confirmez que vous voulez effectuer l’opération.

---

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la géoréplication active, voir [Géoréplication active](active-geo-replication-overview.md).
* Pour en savoir plus sur les groupes de basculement automatique, consultez [Groupes de basculement automatique](auto-failover-group-overview.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](business-continuity-high-availability-disaster-recover-hadr-overview.md).