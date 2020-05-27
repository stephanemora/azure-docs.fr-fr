---
title: Tutoriel - Implémenter Azure Databricks avec un point de terminaison Cosmos DB
description: Ce tutoriel explique comment implémenter Azure Databricks dans un réseau virtuel avec un point de terminaison de service activé pour Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779594"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Tutoriel : Implémenter Azure Databricks avec un point de terminaison Cosmos DB

Ce tutoriel explique comment implémenter un environnement Databricks injecté par réseau virtuel avec un point de terminaison de service activé pour Cosmos DB.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks dans un réseau virtuel
> * Créer un point de terminaison de service Cosmos DB
> * Créer un compte Cosmos DB et importer des données
> * Créer un cluster Azure Databricks
> * Interroger Cosmos DB à partir d’un bloc-notes Azure Databricks

## <a name="prerequisites"></a>Prérequis

Avant de commencer, procédez comme suit :

* Créez un [espace de travail Azure Databricks dans un réseau virtuel](quickstart-create-databricks-workspace-vnet-injection.md).

* Téléchargez le [connecteur Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Télécharger les exemples de données à partir de [NOAA National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/). Sélectionnez un état ou une zone, puis **Rechercher**. À la page suivante, acceptez les valeurs par défaut et sélectionnez **Rechercher**. Sélectionnez ensuite **Téléchargement du fichier CSV** sur le côté gauche de la page pour télécharger les résultats.

* Téléchargez le fichier [binaire précompilée](https://aka.ms/csdmtool) de l’outil de migration de données Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Créer un point de terminaison de service Cosmos DB

1. Une fois que vous avez déployé un espace de travail Azure Databricks dans un réseau virtuel, accédez au réseau virtuel dans le [portail Azure](https://portal.azure.com). Notez les sous-réseaux publics et privés qui ont été créés via le déploiement de Databricks.

   ![Sous-réseaux du réseau virtuel](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Sélectionnez *public-subnet* et créez un point de terminaison de service Cosmos DB. **Enregistrez**.
   
   ![Ajouter un point de terminaison de service Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Création d’un compte Cosmos DB

1. Ouvrez le portail Azure. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource > Bases de données > Azure Cosmos DB**.

2. Renseignez les **Détails de l’instance** sur l’onglet **De base** avec les paramètres suivants :

   |Paramètre|Valeur|
   |-------|-----|
   |Abonnement|*votre abonnement*|
   |Groupe de ressources|*votre groupe de ressources*|
   |Nom du compte|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Emplacement|USA Ouest|
   |Géoredondance|Disable|
   |Écritures multirégions|Activer|

   ![Ajouter un point de terminaison de service Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Sélectionnez l’onglet **Réseau** et configurez votre réseau virtuel. 

   a. Choisissez le réseau virtuel créé comme composant requis, puis sélectionnez *public-subnet*. Notez que *private-subnet* a la note *Point de terminaison « Microsoft AzureCosmosDB » manquant*. C’est dû au fait que vous avez activé uniquement le point de terminaison du service Cosmos DB sur *public-subnet*.

   b. Assurez-vous d’avoir activé **Autoriser l’accès à partir du portail Azure**. Ce paramètre vous permet d’accéder à votre compte Cosmos DB à partir du portail Azure. Si cette option est définie sur **Deny**, vous recevrez des erreurs si vous tentez d’accéder à votre compte. 

   > [!NOTE]
   > Cela n’est pas nécessaire pour ce tutoriel, mais vous pouvez également activer *Autoriser l’accès à partir de mon adresse IP* si vous souhaitez pouvoir accéder à votre compte Cosmos DB à partir de votre ordinateur local. Par exemple, si vous vous connectez à votre compte à l’aide du kit de développement logiciel (SDK) Cosmos DB, vous devez activer ce paramètre. S’il est désactivé, vous recevrez des erreurs « Accès refusé ».

   ![Paramètres de réseau du compte Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Sélectionnez **Vérifier + Créer**, puis **Créer** pour créer votre compte Cosmos DB à l’intérieur du réseau virtuel.

5. Une fois votre compte Cosmos DB créé, accédez à **Clés** sous **Paramètres**. Copiez la chaîne de connexion principale et enregistrez-la dans un éditeur de texte pour une utilisation ultérieure.

    ![Page des clés du compte Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Sélectionnez **Explorateur de données** et **Nouvelle collection** pour ajouter une base de données et une collection nouvelles à votre compte Cosmos DB.

    ![Nouvelle collection Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Charger des données dans Cosmos DB

1. Ouvrir la version de l’interface graphique de l’[outil de migration de données de Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Outil de migration de données Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Sur l’onglet **Informations sources**, sélectionnez **Fichier(s) CSV** dans la liste déroulante **Importer à partir de**. Sélectionnez ensuite **Ajouter des fichiers** et ajoutez les données de tempête CSV que vous avez téléchargées comme condition préalable.

    ![Information sur la source de l’outil de migration de données Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Sur l’onglet **Informations cibles**, entrez votre chaîne de connexion. Le format de chaîne de connexion est `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint et AccountKey sont inclus dans la chaîne de connexion principale que vous avez enregistrée dans la section précédente. Ajoutez `Database=<your database name>` à la fin de la chaîne de connexion, puis sélectionnez **Vérifier**. Ensuite, ajoutez le nom de la collection et la clé de partition.

    ![Information sur la cible de l’outil de migration de données Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Sélectionnez **Suivant** jusqu’à ce que vous atteigniez la section Résumé. Ensuite, sélectionnez **Importer**.

## <a name="create-a-cluster-and-add-library"></a>Créer un cluster et ajouter une bibliothèque

1. Accédez à votre service Azure Databricks dans le [portail Azure](https://portal.azure.com) et sélectionnez **Initialiser l’espace de travail**.

2. Créez un cluster. Choisissez un nom de cluster et acceptez les paramètres par défaut restants.

   ![Nouveaux paramètres du cluster](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Une fois que votre cluster est créé, accédez à la page du cluster et sélectionnez l’onglet **Bibliothèques**. Sélectionnez **Installer** et chargez le fichier JAR du connecteur Spark pour installer la bibliothèque.

    ![Installez la bibliothèque du connecteur Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Vous pouvez vérifier que la bibliothèque a été installée sur l’onglet **Bibliothèques**.

    ![Onglet Bibliothèques de cluster Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Interroger Cosmos DB à partir d’un bloc-notes Databricks

1. Accédez à votre espace de travail Azure Databricks et créez une bibliothèque python.

    ![Créez un bloc-notes Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Exécutez le code python suivant pour définir la configuration de connexion de Cosmos DB. Modifiez **Point de terminaison**, **Masterkey**, **Base de données** et **Collection** en conséquence.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Utilisez le code python suivant pour charger les données et créer un affichage temporaire.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Utilisez la commande magic suivante pour exécuter une instruction SQL qui retourne des données.

    ```python
    %sql
    select * from storm
    ```

    Vous avez correctement connecté votre espace de travail Databricks injecté par réseau virtuel à une ressource Cosmos DB activée pour le point de terminaison. Pour en savoir plus sur la façon de se connecter à Cosmos DB, consultez [Connecteur Azure Cosmos DB pour Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’espace de travail Azure Databricks et toutes les ressources associées. La suppression du travail évite une facturation inutile. Si vous avez l’intention d’utiliser l’espace de travail Azure Databricks à l’avenir, vous pouvez arrêter le cluster et le redémarrer ultérieurement. Si vous ne pensez pas continuer à utiliser cet espace de travail Azure Databricks, supprimez toutes les ressources créées au cours de ce tutoriel en procédant comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de ressources que vous avez créé.

2. Sur la page de votre groupe de ressources, sélectionnez **Supprimer**, saisissez le nom de la ressource à supprimer dans la zone de texte, puis sélectionnez à nouveau **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé un espace de travail Azure Databricks dans un réseau virtuel et utilisé le connecteur Spark Cosmos DB pour demander des données de Cosmos DB à partir de Databricks. Pour en savoir plus sur l’utilisation d’Azure Databricks dans un réseau virtuel, passez au tutoriel d’utilisation de SQL Server avec Azure Databricks.

> [!div class="nextstepaction"]
> [Tutoriel : Interroger un conteneur Docker SQL Server Linux dans un réseau virtuel à partir d’un bloc-notes Azure Databricks](vnet-injection-sql-server.md)
