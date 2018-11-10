---
title: Migrer vos données vers un compte d’API Cassandra Azure Cosmos DB
description: Découvrez comment utiliser la commande CQL Copy et Spark pour copier des données depuis Apache Cassandra vers l’API Cassandra d’Azure Cosmos DB.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 56fc07c6d775ee8015ce244acb7782607bda802a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739785"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Tutoriel : Migrer vos données vers un compte d’API Cassandra Azure Cosmos DB

Ce tutoriel fournit des instructions sur la migration de données Apache Cassandra dans l’API Cassandra d’Azure Cosmos DB. 

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Planifier la migration
> * Prérequis pour la migration
> * Migrer des données avec la commande cqlsh COPY
> * Migrer des données avec Spark 

## <a name="plan-for-migration"></a>Planifier la migration

Avant de migrer des données vers l’API Cassandra d’Azure Cosmos DB, vous devez estimer les besoins en débit de votre charge de travail. D’une façon générale, il est recommandé de commencer par le débit moyen nécessaire pour les opérations CRUD, puis d’inclure le débit supplémentaire nécessaire pour les opérations ETL (Extract Transform Load) ou entraînant une charge ponctuelle importante. Vous avez besoin des informations suivantes pour planifier la migration : 

* **Taille des données existantes ou taille estimée des données :** définit les exigences minimales de taille de la base de données et de débit. Si vous estimez la taille des données pour une nouvelle application, vous pouvez supposer que les données sont réparties uniformément entre les lignes et estimer la valeur en multipliant par la taille des données. 

* **Débit nécessaire :** débit approximatif en lecture (interroger/obtenir) et en écriture (mettre à jour/supprimer/insérer). Cette valeur est nécessaire pour calculer les unités de requête nécessaires, ainsi que la taille des données à l’état stable.  

* **Obtenir le schéma :** connectez-vous à votre cluster Cassandra existant via cqlsh et exportez le schéma depuis Cassandra : 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

Après avoir identifié les exigences de votre charge de travail existante, vous devez créer un compte, une base de données et des conteneurs Azure Cosmos DB conformément aux spécifications établies en matière de débit.  

* **Déterminer le coût en unités de requête pour une opération :** vous pouvez déterminer les unités de requête avec le SDK de l’API Cassandra d’Azure Cosmos DB API de votre choix. Cet exemple montre la version .NET pour obtenir les frais de RU.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **Allouer le débit requis :** Azure Cosmos DB peut mettre à l’échelle automatiquement le stockage et le débit au fil de l’accroissement de vos besoins. Vous pouvez estimer vos besoins en débit en utilisant le [calculateur d’unités de requête d’Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

## <a name="prerequisites-for-migration"></a>Prérequis pour la migration

* **Créer des tables dans le compte de l’API Cassandra d’Azure Cosmos DB :** avant de commencer la migration des données, créez au préalable toutes vos tables à partir du portail Azure ou de cqlsh. Si vous migrez vers un compte Azure Cosmos DB qui a un débit de niveau de base de données, assurez-vous de fournir une clé de partition lors de la création de conteneurs Azure Cosmos DB.

* **Augmenter le débit :** la durée de la migration de vos données dépend de la quantité de débit que vous avez provisionnée pour les tables dans Azure Cosmos DB. Augmentez le débit pendant la durée de la migration. Si le débit est plus élevé, vous pouvez éviter la limitation de débit et procéder à une migration plus rapide. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit, consultez [définir le débit](set-throughput.md) pour les conteneurs Azure Cosmos DB. Il est également recommandé d’avoir un compte Azure Cosmos DB dans la même région que votre base de données source. 

* **Activer SSL :** Azure Cosmos DB a des spécifications et des standards de sécurité stricts. Veillez à activer SSL lorsque vous interagissez avec votre compte. Lorsque vous utilisez CQL avec SSH, vous pouvez fournir des informations sur SSL.

## <a name="options-to-migrate-data"></a>Options pour migrer des données

Vous pouvez déplacer des données depuis des charges de travail Cassandra existantes vers Azure Cosmos DB en utilisant les options suivantes :

* [Avec la commande cqlsh COPY](#using-cqlsh-copy-command)  
* [Avec Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrer des données avec la commande cqlsh COPY

La [commande CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) est utilisée pour copier des données locales vers le compte de l’API Cassandra d’Azure Cosmos DB. Pour copier des données, effectuez les étapes suivantes :

1. Obtenez les informations de la chaîne de connexion de votre compte d’API Cassandra :

   * Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte Azure Cosmos DB.

   * Ouvrez le volet **Chaîne de connexion** qui contient toutes les informations dont vous avez besoin pour vous connecter à votre compte d’API Cassandra depuis cqlsh.

2. Connectez-vous à cqlsh en utilisant les informations de connexion du portail.

3. Utilisez la commande CQL COPY pour copier des données locales vers le compte d’API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrer des données avec Spark 

Effectuez les étapes suivantes pour migrer des données vers l’API Cassandra d’Azure Cosmos DB avec Spark :

- Provisionnez une instance [Azure Databricks](cassandra-spark-databricks.md) ou un [cluster HDInsight](cassandra-spark-hdinsight.md) 

- Déplacez les données vers le point de terminaison de l’API Cassandra de destination avec [une opération de copie de table](cassandra-spark-table-copy-ops.md) 

La migration de données avec des travaux Spark est une option recommandée si vos données se trouvent dans un cluster existant dans des machines virtuelles Azure ou dans un autre cloud. Cette option nécessite la configuration de Spark en tant qu’intermédiaire pour une ingestion ponctuelle ou régulière. Vous pouvez accélérer cette migration avec une connectivité ExpressRoute entre le système local et Azure. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à migrer vos données vers le compte d’API Cassandra d’Azure Cosmos DB. Pour plus d’informations sur Azure Cosmos DB, vous pouvez maintenant passer à la section Concepts. 

> [!div class="nextstepaction"]
> [Niveaux de cohérence des données paramétrables dans Azure Cosmos DB](../cosmos-db/consistency-levels.md)


