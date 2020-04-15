---
title: 'Tutoriel : Exemple d’application d’API SQL Java asynchrone de bout en bout avec le flux de modification'
description: Ce tutoriel vous guide à travers une application d’API SQL Java simple qui insère des documents dans un conteneur Azure Cosmos DB, tout en conservant une vue matérialisée du conteneur avec le flux de modification.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587216"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Tutoriel : Exemple d’application d’API SQL Java asynchrone de bout en bout avec le flux de modification

Ce tutoriel vous guide à travers une application d’API SQL Java simple qui insère des documents dans un conteneur Azure Cosmos DB, tout en conservant une vue matérialisée du conteneur avec le flux de modification.

## <a name="prerequisites"></a>Prérequis

* Ordinateur personnel

* URI et clé de votre compte Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Arrière-plan

Le flux de modification Azure Cosmos DB fournit une interface basée sur des événements pour déclencher des actions en réponse à l’insertion de documents. Il a de nombreux usages. Par exemple, dans les applications gourmandes en opérations de lecture et d’écriture, le flux de modification est important, car il permet de créer une **vue matérialisée** en temps réel d’un conteneur durant le processus d’ingestion des documents. Le conteneur de la vue matérialisée héberge les mêmes données, mais celles-ci sont partitionnées pour optimiser les lectures, ce qui rend l’application performante aussi bien en lecture qu’en écriture.

La gestion des événements du flux de modification est en grande partie prise en charge par la bibliothèque du processeur de flux de modification qui est intégrée au SDK. Cette bibliothèque est suffisamment puissante pour distribuer les événements du flux de modification entre plusieurs workers quand cela est nécessaire. Il vous suffit de fournir un rappel à la bibliothèque du processeur de flux de modification.

Cet exemple simple illustre une bibliothèque du processeur de flux de modification avec un seul worker qui crée et supprime des documents dans une vue matérialisée.

## <a name="setup"></a>Programme d’installation

Si vous ne l’avez pas déjà fait, clonez cet exemple de dépôt d’application :

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Vous avez le choix d’utiliser le SDK Java 4.0 ou le SDK Java 3.7.0 pour suivre ce guide de démarrage rapide. **Si vous souhaitez utiliser le SDK Java 3.7.0, dans le terminal, tapez ```git checkout SDK3.7.0```** . Sinon, restez sur la branche ```master```, qui utilise par défaut le SDK Java 4.0.

Ouvrez un terminal à partir du répertoire du dépôt. Générez l’application en exécutant

```bash
mvn clean package
```

## <a name="walkthrough"></a>Procédure pas à pas

1. En premier lieu, vous devez avoir un compte Azure Cosmos DB. Ouvrez le **portail Azure** dans votre navigateur, accédez à votre compte Azure Cosmos DB et, dans le volet de gauche, accédez à **Explorateur de données**.

    ![Compte Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Exécutez l’application dans le terminal à l’aide de la commande suivante :

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Appuyez sur Entrée quand vous voyez

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Retournez ensuite dans l’Explorateur de données du portail Azure dans votre navigateur. Vous voyez qu’une base de données **GroceryStoreDatabase** avec trois conteneurs vides a été ajoutée : 

    * **InventoryContainer** : enregistrement d’inventaire pour notre exemple de magasin, partitionné sur l’élément ```id``` qui est un UUID.
    * **InventoryContainer-pktype** : vue matérialisée de l’enregistrement d’inventaire, optimisée pour les requêtes sur l’élément ```type```.
    * **InventoryContainer-leases** : un conteneur de baux est obligatoire pour le flux de modification ; les baux suivent la progression de l’application dans la lecture du flux de modification.


    ![Conteneurs vides](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. Dans le terminal, vous devez maintenant voir une invite

    ```bash
    Press enter to start creating the materialized view...
    ```

    Appuyez sur Entrée. À présent, le bloc de code suivant va exécuter et initialiser le processeur de flux de modification sur un autre thread : 


    **Java SDK 4.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"``` est le nom du worker du processeur de flux de modification. ```changeFeedProcessorInstance.start()``` est ce qui démarre le processeur de flux de modification.

    Retournez ensuite dans l’Explorateur de données du portail Azure dans votre navigateur. Sous le conteneur **InventoryContainer-leases**, cliquez sur **Éléments** pour voir le contenu. Vous voyez que le processeur de flux de modification a rempli le conteneur de baux, c’est-à-dire que le processeur a affecté au worker ```SampleHost_1``` un bail sur certaines partitions du conteneur **InventoryContainer**.

    ![Baux](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Appuyez une nouvelle fois sur Entrée dans le terminal. Cette action déclenche l’insertion de dix documents dans **InventoryContainer**. Chaque insertion de document apparaît dans le flux de modification au format JSON. Le code de rappel suivant gère ces événements en mettant en miroir les documents JSON dans une vue matérialisée :

    **Java SDK 4.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. Laissez le code s’exécuter pendant 5 à 10 secondes. Ensuite, retournez dans l’Explorateur de données du portail Azure et accédez à **InventoryContainer > Éléments**. Vous devez voir que les éléments sont insérés dans le conteneur d’inventaire. Notez la clé de partition (```id```).

    ![Conteneur de flux](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. À présent, dans l’Explorateur de données, accédez à **InventoryContainer-pktype > Éléments**. Il s’agit de la vue matérialisée : les éléments de ce conteneur sont en miroir d’**InventoryContainer** parce qu’ils ont été insérés programmatiquement par le flux de modification. Notez la clé de partition (```type```). Cette vue matérialisée est optimisée pour un filtrage des requêtes sur ```type```, mais c’est inefficace sur **InventoryContainer** qui est lui partitionné sur ```id```.

    ![Vue matérialisée](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Nous allons supprimer un document dans **InventoryContainer** et **InventoryContainer-pktype** en utilisant un seul appel ```upsertItem()```. Avant cela, jetons un coup d’œil à l’Explorateur de données du portail Azure. Nous supprimerons le document dont l’élément ```/type == "plums"``` est entouré en rouge ci-dessous

    ![Vue matérialisée](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Appuyez de nouveau sur Entrée pour appeler la fonction ```deleteDocument()``` dans l’exemple de code. Cette fonction, illustrée ci-dessous, fait un upsert d’une nouvelle version du document avec ```/ttl == 5```, qui définit la durée de vie (TTL) du document à cinq secondes. 
    
    **Java SDK 4.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    **Java SDK 3.7.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    Le flux de modification ```feedPollDelay``` est défini à 100 ms. Il répond donc presque instantanément à cette mise à jour et appelle ```updateInventoryTypeMaterializedView()``` comme illustré ci-dessus. Ce dernier appel de fonction fait un upsert du nouveau document avec une durée de vie de cinq secondes dans **InventoryContainer-pktype**.

    L’effet est qu’après environ cinq secondes, le document expire et est supprimé des deux conteneurs.

    Cette procédure est nécessaire du fait que le flux de modification émet des événements quand des éléments sont insérés ou mis à jour, mais pas quand ils sont supprimés.

1. Appuyez une dernière fois sur Entrée pour fermer le programme et nettoyer ses ressources.
