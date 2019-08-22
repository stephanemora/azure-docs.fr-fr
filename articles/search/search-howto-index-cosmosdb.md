---
title: Indexer une source de données Azure Cosmos DB - Recherche Azure
description: Analyser une source de données Azure Cosmos DB et ingérer des données dans un index de recherche en texte intégral dans Recherche Azure. Les indexeurs automatisent l’ingestion des données pour les sources de données sélectionnées telles qu’Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656697"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Comment indexer Cosmos DB à l’aide d’un indexeur de Recherche Azure


> [!Note]
> La prise en charge de l’API MongoDB est en préversion et n’a pas été conçue pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de portail ou de SDK .NET pour l’instant.
>
> L’API SQL est mise à la disposition générale.

Cet article vous montre comment configurer l’[indexeur](search-indexer-overview.md) Azure Cosmos DB pour extraire le contenu et les rendre détectables dans la Recherche Azure. Ce flux de travail crée un index Recherche Azure et le charge avec le texte existant extrait d’Azure Cosmos DB. 

Étant donné que la terminologie peut prêter à confusion, il est important de souligner que l’[indexation Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) et l’[indexation Recherche Azure](search-what-is-an-index.md) sont des opérations distinctes, propres à chaque service. Avant de commencer l’indexation Recherche Azure, votre base de données Azure Cosmos DB doit déjà exister et contenir des données.

Vous pouvez utiliser le [portail](#cosmos-indexer-portal), les API REST ou le SDK .NET pour indexer du contenu Cosmos. L’indexeur Cosmos DB dans la Recherche Azure peut analyser les [éléments d’Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) accessibles via ces protocoles :

* [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [API MongoDB (préversion)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice a des éléments existants pour la prise en charge d’API supplémentaires. Vous pouvez effectuer un cast pour les API Cosmos que vous aimeriez voir prises en charge dans la Recherche Azure : [API Table](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [API Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Utiliser le portail

La méthode la plus simple pour l’indexation d’éléments Azure Cosmos consiste à utiliser un Assistant dans le [portail Azure](https://portal.azure.com/). En analysant les données et en lisant les métadonnées dans le conteneur, l’Assistant [**Importation de données**](search-import-data-portal.md) peut créer un index par défaut, mapper des champs sources aux champs d’index cibles et charger l’index en une seule opération. Selon la taille et la complexité de la source de données, vous pouvez obtenir un index de recherche en texte intégral opérationnel en quelques minutes.

Nous vous recommandons d’utiliser le même abonnement Azure pour Recherche Azure et Azure Cosmos DB, de préférence dans la même région.

### <a name="1---prepare-source-data"></a>1 - Préparez les données sources

Vous devez avoir un compte Cosmos, une base de données Azure Cosmos mappée à l’API SQL ou l’API MongoDB et un conteneur de documents JSON. 

Assurez-vous que votre base de données Cosmos DB contient des données. L’[Assistant Importation de données](search-import-data-portal.md) lit les métadonnées et effectue un échantillonnage des données pour déduire un schéma d’index, mais il charge également des données à partir de Cosmos DB. Si les données sont manquantes, l’Assistant s’arrête avec l’erreur « Erreur lors de la détection du schéma d’index à partir de la source de données : Impossible de générer un index prototype, car la source de données 'emptycollection' n’a retourné aucune donnée ».

### <a name="2---start-import-data-wizard"></a>2 - Démarrez l’Assistant Importation de données

Vous pouvez [démarrer l’Assistant](search-import-data-portal.md) à partir de la barre de commandes dans la page du service Recherche Azure, ou en cliquant sur **Ajouter Recherche Azure** dans la section **Paramètres** du volet de navigation gauche de votre compte de stockage.

   ![Commande Importer des données dans le portail](./media/search-import-data-portal/import-data-cmd2.png "Démarrer l’Assistant Importation de données")

### <a name="3---set-the-data-source"></a>3 - Définissez la source de données

> [!NOTE] 
> Pour l’instant, vous ne pouvez pas créer ou modifier des sources de données **MongoDB** à l’aide du portail Azure ou du Kit de développement logiciel (SDK) .NET. Cependant, vous **pouvez** surveiller l’historique d’exécution des indexeurs MongoDB dans le portail.

Dans la page **Source de données**, la source doit être **Cosmos DB**, avec les spécifications suivantes :

+ Le **Nom** correspond au nom de l’objet source de données. Une fois créé, vous pouvez le choisir pour d’autres charges de travail.

+ Un **Compte Cosmos DB** doit être la chaîne de connexion primaire ou secondaire à partir de Cosmos DB, avec un élément `AccountEndpoint` et un élément `AccountKey`. Le compte détermine si les données sont castées en tant qu’API SQL ou qu’API Mongo DB

+ La **Base de données** correspond à une base de données existante à partir du compte. 

+ La **Collection** correspond à un conteneur de documents. Des documents doivent exister pour que l’importation réussisse. 

+ Une **Requête** peut être vide si vous souhaitez tous les documents, sinon vous pouvez saisir une requête qui sélectionne un sous-ensemble du document. 

   ![Définition de source de données Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Définition de source de données Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Passez la page « Ajouter la recherche cognitive » dans l’Assistant

L’ajout de compétences cognitives n’est pas nécessaire pour l’importation de documents. Sauf si vous avez un besoin spécifique d’[inclure des API et des transformations Cognitive Services](cognitive-search-concept-intro.md) à votre pipeline d’indexation, vous devez ignorer cette étape.

Pour ignorer cette étape, accédez d’abord à la page suivante.

   ![Bouton de page suivante pour la recherche cognitive](media/search-get-started-portal/next-button-add-cog-search.png)

Dans cette page, vous pouvez passer directement à la personnalisation de l’index.

   ![Ignorer l’étape des compétences cognitives](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - Définissez les attributs de l’index

Dans la page **Index**, vous devez voir une liste de champs avec un type de données et une série de cases à cocher permettant de définir les attributs de l’index. L’Assistant peut générer une liste de champs basée sur les métadonnées et en échantillonnant les données sources. 

Vous pouvez sélectionner des attributs en bloc en cliquant sur la case à cocher en haut de la colonne d’attribut. Choisissez **Récupérable** et **Possibilité de recherche** pour chaque champ qui doit être retourné vers une application cliente et soumis à un traitement de recherche de texte intégral. Vous remarquerez que les entiers ne peuvent pas être recherchés en texte intégral ou partiel (les nombres sont évalués textuellement et sont généralement utiles dans les filtres).

Pour plus d’informations, passez en revue la description des [attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) et des [analyseurs de langage](https://docs.microsoft.com/rest/api/searchservice/language-support). 

Prenez un moment pour passer en revue vos sélections. Une fois que vous exécutez l’Assistant, des structures de données physiques sont créées : vous ne pourrez donc plus modifier ces champs sans supprimer et recréer tous les objets.

   ![Définition d’index Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Définition d’index Cosmos DB")

### <a name="6---create-indexer"></a>6 - Créez un indexeur

Une fois que tout est spécifié, l’Assistant crée trois objets distincts dans votre service de recherche. Un objet source de données et un objet index sont enregistrés comme ressources nommées dans votre service Recherche Azure. La dernière étape crée un objet indexeur. Le fait de nommer l’indexeur lui permet d’exister comme ressource autonome, que vous pouvez planifier et gérer indépendamment de l’objet index et de l’objet source de données, créés dans la même séquence de l’Assistant.

Si vous n’êtes pas familiarisé avec les indexeurs, en voici une définition : un *indexeur* est une ressource dans Recherche Azure qui analyse une source de données externe et son contenu avec possibilité de recherche. La sortie de l’Assistant **Importation de données** est un indexeur qui analyse votre source de données Cosmos DB, extrait le contenu avec possibilité de recherche et l’importe dans un index sur Recherche Azure.

La capture d’écran suivante montre la configuration de l’indexeur par défaut. Vous pouvez basculer sur **Une seule fois** si vous souhaitez exécuter l’indexeur une seule fois. Cliquez sur **Envoyer** pour exécuter l’Assistant et créer tous les objets. L’indexation commence immédiatement.

   ![Définition d’indexeur Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Définition d’indexeur Cosmos DB")

Vous pouvez surveiller l’importation des données dans les pages du portail. Des notifications de l’avancement indiquent l’état de l’indexation et le nombre de documents chargés. 

Quand l’indexation est terminée, vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) pour interroger votre index.

> [!NOTE]
> Si vous ne voyez pas les données attendues, vous devrez peut-être définir d’autres attributs sur d’autres champs. Supprimez l’index et l’indexeur que vous venez de créer et réexécutez l’Assistant, en modifiant vos sélections pour les attributs d’index à l’étape 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utiliser les API REST

Vous pouvez utiliser l’API REST pour indexer des données Azure Cosmos DB en suivant un flux de travail en trois parties commun à tous les indexeurs dans Recherche Azure : créer une source de données, créer un index, créer un indexeur. L’extraction de données du stockage Cosmos se produit lorsque vous envoyez la requête de création d’un indexeur. Lorsque cette requête est terminée, vous disposez d’un index pouvant être interrogé. 

Si vous évaluez MongoDB, vous devez utiliser l’`api-version=2019-05-06-Preview` REST pour créer la source de données.

Dans votre compte Cosmos DB, vous pouvez choisir si vous souhaitez que la collection indexe automatiquement tous les documents. Par défaut, tous les documents sont indexés automatiquement, mais vous pouvez désactiver l’indexation automatique. Quand l’indexation est désactivée, les documents sont accessibles uniquement par le biais de leurs liens réflexifs ou de requêtes avec l’ID de document. Azure Search nécessite l’activation de l’indexation automatique Cosmos DB dans la collection qui sera indexée par Azure Search. 

> [!WARNING]
> Azure Cosmos DB est la nouvelle génération de DocumentDB. Précédemment, avec l’API version **2017-11-11**, vous pouviez utiliser la syntaxe `documentdb`. Autrement dit, vous pouviez spécifier votre type de source de données en tant que `cosmosdb` ou `documentdb`. À compter de la version d’API **2019-05-06**, les APIS Recherche Azure et le portail prennent uniquement en charge la syntaxe `cosmosdb` comme indiqué dans cet article. Cela signifie que le type de source de données doit être `cosmosdb` si vous souhaitez vous connecter à un point de terminaison Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Assembler des entrées pour la requête

Pour chaque requête, vous devez fournir le nom du service et la clé d’administration pour Recherche Azure (dans l’en-tête POST), ainsi que le nom du compte de stockage et la clé pour le stockage d’objets Blob. Vous pouvez utiliser [Postman](search-get-started-postman.md) pour envoyer des requêtes HTTP à Recherche Azure.

Copiez les quatre valeurs suivantes dans le bloc-notes pour pouvoir les coller dans une requête :

+ Nom du service Recherche Azure
+ Clé d’administration de Recherche Azure
+ Chaîne de connexion Cosmos DB

Vous pouvez trouver ces valeur dans le portail :

1. Dans les pages du portail pour Recherche Azure, copiez l’URL du service de recherche dans la page Vue d’ensemble.

2. Dans le volet de navigation gauche, cliquez sur **Clés**, puis copiez la clé primaire ou secondaire (elles sont équivalentes).

3. Basculez vers les pages du portail pour votre compte de stockage Cosmos. Dans le volet de navigation gauche, sous **Paramètres**, sélectionnez **Clés**. Cette page fournit un URI, deux ensembles de chaînes de connexion, et deux ensembles de clés. Copiez une des chaînes de connexion dans le bloc-notes.

### <a name="2---create-a-data-source"></a>2 - Création d'une source de données

Une **source de données** spécifie les données à indexer, les informations d’identification et les stratégies pour identifier les modifications des données (par exemple, les documents modifiés ou supprimés dans votre collection). La source de données est définie en tant que ressource indépendante de manière à pouvoir être utilisée par plusieurs indexeurs.

Pour créer une source de données, formulez une requête POST :

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Le corps de la requête contient la définition de la source de données, qui doit inclure les champs suivants :

| Champ   | Description |
|---------|-------------|
| **name** | Requis. Choisissez un nom pour représenter votre objet source de données. |
|**type**| Requis. Doit être `cosmosdb`. |
|**credentials** | Requis. Doit être une chaîne de connexion Cosmos DB.<br/>Pour les collections SQL, les chaînes de connexion sont au format suivant : `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Pour les collections MongoDB, ajoutez **ApiKind=MongoDb** à la chaîne de connexion :<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Évitez les numéros de port dans l’URL du point de terminaison. Si vous incluez le numéro de port, Recherche Azure ne peut pas indexer votre base de données Azure Cosmos DB.|
| **container** | Contient les éléments suivants : <br/>**nom** : Requis. Spécifiez l’ID de la collection de bases de données à indexer.<br/>**query** : facultatif. Vous pouvez spécifier une requête pour obtenir un schéma plat à partir d'un document JSON arbitraire de manière à ce qu'Azure Search puisse procéder à l'indexation.<br/>Pour les collections MongoDB, les requêtes ne sont pas prises en charge. |
| **dataChangeDetectionPolicy** | Recommandé. Consultez la section [Indexation des documents modifiés](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | facultatif. Consultez la section [Indexation des documents supprimés](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Utilisation de requêtes pour formater les données indexées
Vous pouvez spécifier une requête SQL pour aplatir les propriétés ou les tableaux imbriqués, projeter des propriétés JSON et filtrer les données à indexer. 

> [!WARNING]
> Les requêtes personnalisées ne sont pas pris en charge pour les collections **MongoDB** : le paramètre `container.query` doit être défini sur Null ou être omis. Si vous avez besoin d’utiliser une requête personnalisée, indiquez-le nous sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Exemple de document :

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Requête de filtre :

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Requête d’aplatissage :

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Requête de projection :

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Requête d’aplatissage de tableau :

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Créer un index de recherche cible 

[Créez un index Azure Search cible](/rest/api/searchservice/create-index) si vous n'en possédez pas déjà un. L'exemple suivant crée un index avec un champ ID et un champ Description :

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Assurez-vous que le schéma de votre index cible est compatible avec le schéma des documents JSON source ou la sortie de votre projection de requête personnalisée.

> [!NOTE]
> Pour les collections partitionnées, la clé de document par défaut est la propriété `_rid` d’Azure Cosmos DB, que Recherche Azure renomme automatiquement `rid`, car les noms de champ ne peuvent pas commencer par un trait de soulignement. De même, les valeurs `_rid` d’Azure Cosmos DB contiennent des caractères non valides dans les clés de Recherche Azure. Par conséquent, les valeurs `_rid` sont codées en Base64.
> 
> Pour les collections MongoDB, Recherche Azure renomme automatiquement la propriété `_id` `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappage entre les types de données JSON et les types de données Azure Search
| Type de données JSON | Types de champs d’index cible compatibles |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Nombres qui ressemblent à des nombres entiers |Edm.Int32, Edm.Int64, Edm.String |
| Nombres qui ressemblent à des nombres avec points flottants |Edm.Double, Edm.String |
| Chaîne |Edm.String |
| Tableaux de types primitifs, par exemple ["a", "b", "c"] |Collection(Edm.String) |
| Chaînes qui ressemblent à des dates |Edm.DateTimeOffset, Edm.String |
| Objets GeoJSON, par exemple { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Autres objets JSON |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4 - Configurer et exécuter l’indexeur

Une fois l'index et la source de données créés, vous êtes prêt à créer l’indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Cet indexeur s’exécute toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Pour plus d’informations sur la définition des planifications de l’indexeur, consultez [Comment planifier des indexeurs pour Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Utiliser .NET

Le kit de développement logiciel (SDK) .NET mis à la disposition générale offre une parité complète avec l’API REST mise à la disposition générale. Nous vous recommandons de consulter la section précédente de l’API REST pour découvrir les concepts, les workflows et les exigences. Vous pouvez alors vous référer à la documentation de référence des API .NET suivante pour implémenter un indexeur JSON dans du code managé.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexation des documents modifiés

L'objectif d'une stratégie de détection des changements de données est d'identifier efficacement les données modifiées. La seule stratégie actuellement prise en charge est la stratégie `High Water Mark` qui utilise la propriété `_ts` (timestamp) fournie par Azure Cosmos DB, définie ainsi :

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Cette stratégie est vivement recommandée pour garantir de bonnes performances pour l’indexeur. 

Si vous utilisez une requête personnalisée, assurez-vous que la propriété `_ts` est projetée par la requête.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progression incrémentielle et requêtes personnalisées

Dans le cas où l’exécution de l’indexeur est interrompue par des défaillances passagères ou un dépassement du délai d’exécution, la progression incrémentielle pendant l’indexation permet à l’indexeur de reprendre là il en était lors de sa dernière exécution, plutôt que d’avoir à tout réindexer depuis le début. Ceci est particulièrement important lors de l’indexation de grandes collections. 

Pour activer la progression incrémentielle lors de l’utilisation d’une requête personnalisée, assurez-vous que votre requête classe les résultats par la colonne `_ts`. Ceci permet de créer des points de contrôle périodiques dont Azure Search se sert pour proposer la progression incrémentielle en cas d’erreurs.   

Dans certains cas, il se peut qu’Azure Search ne déduise pas que la requête est ordonnée par `_ts`, même si elle contient une clause `ORDER BY [collection alias]._ts`. Vous pouvez indiquer à Azure Search que les résultats sont triés à l’aide de la propriété de configuration `assumeOrderByHighWaterMarkColumn`. Pour ce faire, créez ou mettez à jour l’indexeur comme suit : 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexation des documents supprimés

Lorsque des lignes sont supprimées de la collection, vous devez normalement supprimer ces lignes de l'index de recherche. L'objectif d'une stratégie de détection des suppressions de données est d'identifier efficacement les données supprimées. La seule stratégie actuellement prise en charge est la stratégie `Soft Delete` (où la suppression est signalée par un indicateur quelconque), spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Si vous utilisez une requête personnalisée, assurez-vous que la propriété référencée par `softDeleteColumnName` est projetée par la requête.

L'exemple suivant crée une source de données avec des conseils pour une stratégie de suppression en douceur :

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous avez appris à intégrer Azure Cosmos DB avec Recherche Azure à l’aide d’un indexeur.

* Pour en savoir plus sur Azure Cosmos DB, consultez la [page du service Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Pour en savoir plus sur la Recherche Azure, consultez la [page du service Recherche](https://azure.microsoft.com/services/search/).
