---
title: Configurez des points de terminaison privés pour le magasin analytique Azure Cosmos DB.
description: Découvrez comment configurer des points de terminaison privés managés pour le magasin analytique Azure Cosmos DB afin de restreindre l’accès réseau.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479047"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Configurer Azure Private Link pour le magasin analytique Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Dans cet article, vous allez apprendre à configurer des points de terminaison privés managés pour le magasin analytique Azure Cosmos DB. Si vous utilisez le magasin transactionnel, consultez l’article [Points de terminaison privés pour le magasin transactionnel](how-to-configure-private-endpoints.md). À l’aide de [points de terminaison privés managés](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md), vous pouvez limiter l’accès réseau de votre magasin analytique Azure Cosmos DB à un réseau virtuel managé associé à votre espace de travail Azure Synapse. Les points de terminaison privés managés établissent une liaison privée avec votre magasin analytique.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Activer un point de terminaison privé pour le magasin analytique

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Configurer un espace de travail Azure Synapse Analytics avec un réseau virtuel managé

[Créez un espace de travail dans Azure Synapse Analytics avec l’option d’exfiltration de données activée.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Avec la [protection contre l’exfiltration de données](../synapse-analytics/security/workspace-data-exfiltration-protection.md), vous empêchez les utilisateurs malveillants potentiels de copier ou transférer des données à partir de vos ressources Azure vers des emplacements en dehors de l’étendue de votre organisation.

Les restrictions d’accès suivantes s’appliquent quand la protection contre l’exfiltration de données est activée pour un espace de travail Azure Synapse Analytics :

* Si vous utilisez Azure Spark pour Azure Synapse Analytics, l’accès est autorisé uniquement aux points de terminaison privés managés qui sont approuvés pour le magasin analytique Azure Cosmos DB.

* Si vous utilisez des pools SQL serverless Synapse, vous pouvez interroger n’importe quel compte Azure Cosmos DB avec Azure Synapse Link. Toutefois, les requêtes d’écriture [CETAS (create external tables as select)](../synapse-analytics/sql/develop-tables-cetas.md) sont uniquement autorisées aux points de terminaison privés managés et approuvés dans le réseau virtuel de l’espace de travail.

> [!NOTE]
> Vous ne pouvez pas modifier la configuration du réseau virtuel managé et de la protection contre l’exfiltration de données après que l’espace de travail a été créé.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Ajouter un point de terminaison privé managé pour le magasin analytique Azure Cosmos DB

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Dans le portail Azure, accédez à votre espace de travail Synapse Analytics et ouvrez le volet **Vue d’ensemble**.

1. Lancez Synapse Studio en accédant au volet **Bien démarrer** et en sélectionnant **Ouvrir** sous **Ouvrir Synapse Studio**.

1. Dans Synapse Studio, ouvrez l’onglet **Gérer**.

1. Accédez à **Points de terminaison privés managés**, puis sélectionnez **Nouveau**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Créez un point de terminaison privé pour le magasin analytique." border="true":::

1. Sélectionnez **Azure Cosmos DB (API SQL)**  type de compte > **Continuer**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Sélectionnez API SQL Azure Cosmos DB pour créer un point de terminaison privé." border="true":::

1. Remplissez le formulaire **Nouveau point de terminaison privé managé** avec les informations suivantes :

   * **Nom** : nom du point de terminaison privé managé. Ce nom ne pourra pas être changé une fois le point de terminaison créé.
   * **Description** : entrez une description qui identifie clairement le point de terminaison privé.
   * **Abonnement Azure** : sélectionnez un compte Azure Cosmos DB dans la liste des comptes disponibles dans vos abonnements Azure.
   * **Nom du compte Azure Cosmos DB** : sélectionnez un compte Azure Cosmos DB existant de type SQL ou MongoDB.
   * **Sous-ressource cible** : sélectionnez l’une des options suivantes : **Analytique**, si vous souhaitez ajouter le point de terminaison privé pour le magasin analytique Azure Cosmos DB.
     **SQL** (ou **MongoDB**), si vous souhaitez ajouter le point de terminaison pour un compte transactionnel ou OLTP.

   > [!NOTE]
   > Vous pouvez ajouter des points de terminaison privés de magasin transactionnel et de magasin analytique au même compte Azure Cosmos DB dans un espace de travail Azure Synapse Analytics. Si vous voulez seulement exécuter des requêtes analytiques, vous pouvez mapper seulement le point de terminaison privé analytique.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Choisissez Analytique pour la sous-ressource cible." border="true":::

1. Après la création, accédez au nom du point de terminaison privé et sélectionnez **Gérer les approbations dans le portail Azure**.

1. Accédez à votre compte Azure Cosmos DB, sélectionnez le point de terminaison privé, puis sélectionnez **Approuver**.

1. Revenez à l’espace de travail Synapse Analytics et cliquez sur **Actualiser** dans le volet **Points de terminaison privés managés**. Vérifiez que le point de terminaison privé affiche l’état **Approuvé**.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Vérifiez que le point de terminaison privé est approuvé" border="true":::.

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Utiliser Apache Spark pour Azure Synapse Analytics

Si vous avez créé un espace de travail Azure Synapse avec la protection contre l’exfiltration de données activée, l’accès sortant de Synapse Spark aux comptes Azure Cosmos DB sera bloqué par défaut. De plus, si Azure Cosmos DB possède déjà un point de terminaison privé, Synapse Spark ne pourra pas y accéder.

Pour autoriser l’accès aux données Azure Cosmos DB :

* Si vous utilisez Azure Synapse Link pour interroger des données Azure Cosmos DB, ajoutez un point de terminaison privé **analytique** managé pour le compte Azure Cosmos DB.

* Si vous utilisez des opérations de lecture/écriture par lots et/ou de lecture/écriture en streaming dans un magasin transactionnel, ajoutez un point de terminaison privé managé de type *SQL* ou *MongoDB* pour le compte Azure Cosmos DB. Par ailleurs, définissez *connectionMode* sur *Gateway* (Passerelle) comme indiqué dans l’extrait de code suivant :

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Utilisation de pools SQL serverless Synapse

Les pools SQL serverless Synapse utilisent des fonctionnalités multilocataires qui ne sont pas déployées dans le réseau virtuel managé. Si le compte Azure Cosmos DB a déjà un point de terminaison privé, le pool SQL serverless Synapse ne pourra pas accéder au compte à cause des contrôles d’isolation réseau qui sont appliqués au compte Azure Cosmos DB.

Pour configurer l’isolation réseau pour ce compte à partir d’un espace de travail Synapse :

1. Autorisez l’espace de travail Synapse à accéder au compte Azure Cosmos DB en spécifiant le paramètre `NetworkAclBypassResourceId` sur le compte.

   **Utiliser PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Utilisation de l’interface de ligne de commande Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Le compte Azure Cosmos DB et l’espace de travail Azure Synapse Analytics doivent se trouver sous le même locataire Azure Active Directory (AD).

2. Vous pouvez maintenant accéder au compte à partir de pools SQL serverless, en exécutant des requêtes T-SQL sur Azure Synapse Link. Toutefois, pour garantir l’isolation réseau des données dans le magasin analytique, vous devez ajouter un point de terminaison privé managé **analytique** pour ce compte. Sinon, l’accès public aux données dans le magasin analytique sera possible.

> [!IMPORTANT]
> Si vous utilisez Azure Synapse Link et que vous avez besoin d’une isolation réseau de vos données dans le magasin analytique, vous devez mapper le compte Azure Cosmos DB dans l’espace de travail Synapse à l’aide d’un point de terminaison privé managé **analytique**.

## <a name="next-steps"></a>Étapes suivantes

* Démarrez avec les [requêtes sur le magasin analytique avec Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Démarrez avec les [requêtes sur le magasin analytique avec les pools SQL serverless Azure Synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
