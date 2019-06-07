---
title: Indexer une source de données Azure Cosmos DB - Recherche Azure
description: Analyser une source de données Azure Cosmos DB et ingérer des données dans un index de recherche en texte intégral dans Recherche Azure. Les indexeurs automatisent l’ingestion des données pour les sources de données sélectionnées telles qu’Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0610f57e264189961a6803bee5bb93c1ec9fb103
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753987"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Comment indexer Cosmos DB à l’aide d’un indexeur recherche Azure


> [!Note]
> Prise en charge de l’API MongoDB est en version préliminaire et les a pas été conçu pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’existe aucun portail ou la prise en charge du SDK .NET pour l’instant.
>
> API SQL est généralement disponible.

Cet article vous montre comment configurer Azure Cosmos DB [indexeur](search-indexer-overview.md) pour extraire le contenu et les rendre détectables dans Azure Search. Ce flux de travail crée un index Azure Search et la charge de texte existant extrait à partir d’Azure Cosmos DB. 

Étant donné que la terminologie peut prêter à confuse, il est important de souligner que [indexation Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) et [l’indexation de recherche Azure](search-what-is-an-index.md) sont des opérations distinctes, propres à chaque service. Avant de commencer la recherche Azure l’indexation, votre base de données Azure Cosmos DB doit déjà exister et contiennent des données.

Vous pouvez utiliser la [portal](#cosmos-indexer-portal), API REST ou .NET SDK pour indexer le contenu Cosmos. L’indexeur Cosmos DB dans Azure Search peut analyser [éléments d’Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) accessibles via ces protocoles :

* [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [API de MongoDB (version préliminaire)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice a des éléments existants pour la prise en charge des API supplémentaire. Vous pouvez effectuer un cast d’un vote pour les API Cosmos vous aimeriez voir prises en charge dans Azure Search : [API table](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [API Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Utiliser le portail

La méthode la plus simple pour l’indexation des éléments d’Azure Cosmos consiste à utiliser un Assistant dans le [Azure portal](https://portal.azure.com/). Via un échantillonnage des données et de lecture des métadonnées sur le conteneur, le [ **importer des données** ](search-import-data-portal.md) Assistant dans recherche Azure peut créer un index par défaut, mapper des champs sources aux champs d’index cible et charger l’index dans un seul opération. Selon la taille et la complexité de la source de données, vous pouvez obtenir un index de recherche en texte intégral opérationnel en quelques minutes.

Nous vous recommandons d’utiliser le même abonnement Azure pour Azure Search et Azure Cosmos DB, de préférence dans la même région.

### <a name="1---prepare-source-data"></a>1 - Préparez les données sources

Vous devez avoir un compte Cosmos, une base de données Azure Cosmos mappé à l’API SQL ou l’API MongoDB et un conteneur de documents JSON. 

Assurez-vous que votre base de données Cosmos DB contient des données. Le [Assistant Importer des données](search-import-data-portal.md) lit les métadonnées et effectue un échantillonnage de données pour déduire un schéma d’index, mais également charge des données à partir de Cosmos DB. Si les données sont manquantes, l’Assistant s’arrête avec l’erreur « Erreur du schéma index détection à partir de la source de données : Impossible de générer un index prototype, car la source de données 'emptycollection' a retourné aucune donnée ».

### <a name="2---start-import-data-wizard"></a>2 - Démarrez l’Assistant Importation de données

Vous pouvez [démarrer l’Assistant](search-import-data-portal.md) à partir de la barre de commandes dans la page du service recherche Azure, ou en cliquant sur **ajouter recherche Azure** dans le **paramètres** gauche de la section de votre compte de stockage volet de navigation.

   ![Commande Importer des données dans le portail](./media/search-import-data-portal/import-data-cmd2.png "Démarrer l’Assistant Importation de données")

### <a name="3---set-the-data-source"></a>3 - Définissez la source de données

> [!NOTE] 
> Actuellement, vous ne pouvez pas créer ou modifier **MongoDB** des sources de données à l’aide du portail Azure ou le SDK .NET. Cependant, vous **pouvez** surveiller l’historique d’exécution des indexeurs MongoDB dans le portail.

Dans le **source de données** page, la source doit être **Cosmos DB**, avec les spécifications suivantes :

+ **Nom** est le nom de l’objet de source de données. Une fois créé, vous pouvez le choisir pour les autres charges de travail.

+ **Compte COSMOS DB** doit être la chaîne de connexion primaire ou secondaire à partir de Cosmos DB, avec un `AccountEndpoint` et un `AccountKey`. Le compte détermine si les données sont converties en tant qu’API SQL ou l’API Mongo DB

+ **Base de données** est une base de données existante à partir du compte. 

+ **Collection** est un conteneur de documents. Documents doivent exister dans l’ordre pour l’importation réussisse. 

+ **Requête** ne peut être vide si vous souhaitez que tous les documents, sinon vous pouvez entrer une requête qui sélectionne un sous-ensemble du document. 

   ![Définition de source de données de COSMOS DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "définition de source de données Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Passez la page « Ajouter la recherche cognitive » dans l’Assistant

Ajout de compétences cognitives n’est pas nécessaire pour l’importation du document. Sauf si vous avez un besoin spécifique d’[inclure des API et des transformations Cognitive Services](cognitive-search-concept-intro.md) à votre pipeline d’indexation, vous devez ignorer cette étape.

Pour ignorer cette étape, accédez d’abord à la page suivante.

   ![Bouton de page suivante pour la recherche cognitive](media/search-get-started-portal/next-button-add-cog-search.png)

À partir de cette page vous pouvez passer directement à la personnalisation de l’index.

   ![Ignorer l’étape des compétences cognitives](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - Définissez les attributs de l’index

Dans la page **Index**, vous devez voir une liste de champs avec un type de données et une série de cases à cocher permettant de définir les attributs de l’index. L’Assistant peut générer une liste de champs en fonction de métadonnées et en échantillonnant les données source. 

Vous pouvez sélectionner les attributs en cliquant sur la case à cocher en haut de la colonne d’attribut. Choisissez **récupérable** et **possibilité de recherche** pour chaque champ qui doit être retourné à une application cliente et de traitement de recherche de texte intégral. Vous remarquerez que les entiers ne sont pas recherche en texte intégral ou floues interrogeables (numéros sont évaluées textuellement et sont souvent utiles dans les filtres).

Passez en revue la description de [attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) et [analyseurs de langage](https://docs.microsoft.com/rest/api/searchservice/language-support) pour plus d’informations. 

Prenez un moment pour passer en revue vos sélections. Une fois que vous exécutez l’Assistant, des structures de données physiques sont créées : vous ne pourrez donc plus modifier ces champs sans supprimer et recréer tous les objets.

   ![Définition d’index de COSMOS DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "définition d’index Cosmos DB")

### <a name="6---create-indexer"></a>6 - Créez un indexeur

Une fois que tout est spécifié, l’Assistant crée trois objets distincts dans votre service de recherche. Un objet source de données et un objet index sont enregistrés comme ressources nommées dans votre service Recherche Azure. La dernière étape crée un objet indexeur. Le fait de nommer l’indexeur lui permet d’exister comme ressource autonome, que vous pouvez planifier et gérer indépendamment de l’objet index et de l’objet source de données, créés dans la même séquence de l’Assistant.

Si vous n’êtes pas familiarisé avec les indexeurs, en voici une définition : un *indexeur* est une ressource dans Recherche Azure qui analyse une source de données externe et son contenu avec possibilité de recherche. La sortie de la **importer des données** Assistant est un indexeur qui analyse votre source de données Cosmos DB, extrait le contenu de recherche et les importe dans un index de recherche Azure.

La capture d’écran suivante montre la configuration de l’indexeur par défaut. Vous pouvez basculer vers **une fois** si vous souhaitez exécuter l’indexeur une seule fois. Cliquez sur **Submit** pour exécuter l’Assistant et créer tous les objets. L’indexation commence immédiatement.

   ![Définition de l’indexeur COSMOS DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "définition d’indexeur Cosmos DB")

Vous pouvez surveiller l’importation des données dans les pages du portail. Des notifications de l’avancement indiquent l’état de l’indexation et le nombre de documents chargés. 

Quand l’indexation est terminée, vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) pour interroger votre index.

> [!NOTE]
> Si vous ne voyez pas les données que vous attendez, vous devrez peut-être définir d’autres attributs sur plusieurs champs. Supprimer l’index et l’indexeur que vous venez de créer et exécutez l’Assistant à nouveau, modifier vos sélections pour les attributs d’index à l’étape 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utiliser les API REST

Vous pouvez utiliser l’API REST pour indexer les données Azure Cosmos DB, suivant un flux de travail en trois parties commun à tous les indexeurs dans recherche Azure : créer une source de données, de créer un index, de créer un indexeur. Extraction de données à partir du stockage de Cosmos se produit lorsque vous envoyez la demande de créer un indexeur. Une fois cette demande est terminée, vous aurez un index peut être interrogé. 

Si vous évaluez MongoDB, vous devez utiliser le reste `api-version=2019-05-06-Preview` pour créer la source de données.

Dans votre compte Cosmos DB, vous pouvez choisir si vous souhaitez que la collection indexe automatiquement tous les documents. Par défaut, tous les documents sont indexés automatiquement, mais vous pouvez désactiver l’indexation automatique. Quand l’indexation est désactivée, les documents sont accessibles uniquement par le biais de leurs liens réflexifs ou de requêtes avec l’ID de document. Azure Search nécessite l’activation de l’indexation automatique Cosmos DB dans la collection qui sera indexée par Azure Search. 

> [!WARNING]
> Azure Cosmos DB est la nouvelle génération de DocumentDB. Précédemment avec la version d’API **2017-11-11** vous pouvez utiliser le `documentdb` syntaxe. Cela signifiait que vous pouvez spécifier votre type de source de données en tant que `cosmosdb` ou `documentdb`. En commençant par la version de l’API **2019-05-06** l’API de recherche Azure et le portail prennent uniquement en charge la `cosmosdb` syntaxe comme indiqué dans cet article. Cela signifie que le type de source de données doit `cosmosdb` si vous souhaitez vous connecter à un point de terminaison Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - assembler des entrées pour la demande

Pour chaque demande, vous devez fournir le nom du service et la clé d’administration pour Azure Search (dans l’en-tête POST) et le nom de compte de stockage et la clé pour le stockage blob. Vous pouvez utiliser [Postman](search-fiddler.md) pour envoyer des requêtes HTTP à recherche Azure.

Copiez les quatre valeurs suivantes dans le bloc-notes afin que vous pouvez les coller dans une demande :

+ Nom du service recherche Azure
+ Clé d’administration de recherche Azure
+ Chaîne de connexion COSMOS DB

Vous pouvez trouver ces valeurs dans le portail :

1. Dans les pages du portail pour la recherche Azure, copiez l’URL de service de recherche à partir de la page de présentation.

2. Dans le volet de navigation gauche, cliquez sur **clés** puis copiez la clé primaire ou secondaire (ils sont équivalents).

3. Basculer vers les pages du portail de votre compte de stockage Cosmos. Dans le volet de navigation de gauche, sous **paramètres**, cliquez sur **clés**. Cette page fournit un URI, deux ensembles de chaînes de connexion, et deux ensembles de clés. Copiez une des chaînes de connexion dans le bloc-notes.

### <a name="2---create-a-data-source"></a>2 - créer une source de données

Une **source de données** spécifie les données à indexer, les informations d’identification et les stratégies pour identifier les modifications des données (par exemple, les documents modifiés ou supprimés dans votre collection). La source de données est définie en tant que ressource indépendante de manière à pouvoir être utilisée par plusieurs indexeurs.

Pour créer une source de données, formuler une demande POST :

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
| **name** | Requis. Choisissez un nom pour représenter votre objet de source de données. |
|**type**| Requis. Doit être `cosmosdb`. |
|**credentials** | Requis. Doit être une chaîne de connexion Cosmos DB.<br/>Pour les collections de SQL, les chaînes de connexion sont au format suivant : `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Pour les collections MongoDB, ajoutez **ApiKind = MongoDb** à la chaîne de connexion :<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Évitez les numéros de port dans l’URL du point de terminaison. Si vous incluez le numéro de port, Recherche Azure ne peut pas indexer votre base de données Azure Cosmos DB.|
| **container** | contient les éléments suivants : <br/>**nom** : Requis. Spécifiez l’ID de la collection de base de données à indexer.<br/>**query** : facultatif. Vous pouvez spécifier une requête pour obtenir un schéma plat à partir d'un document JSON arbitraire de manière à ce qu'Azure Search puisse procéder à l'indexation.<br/>Pour les collections MongoDB, les requêtes ne sont pas prises en charge. |
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


### <a name="3---create-a-target-search-index"></a>3 - créer un index de recherche cible 

[Créer un index de recherche Azure cible](/rest/api/searchservice/create-index) si vous n’en avez pas déjà. L’exemple suivant crée un index avec un champ d’ID et la description :

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

### <a name="4---configure-and-run-the-indexer"></a>4 - configurer et exécuter l’indexeur

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

Pour plus d’informations sur la définition des planifications de l’indexeur, consultez [comment planifier des indexeurs pour Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Utiliser .NET

Le kit SDK .NET à la disposition générale offre une parité complète avec l’API REST à la disposition générale. Nous vous recommandons de consulter la section précédente de l’API REST pour découvrir les concepts, les workflows et les exigences. Vous pouvez alors vous référer à la documentation de référence des API .NET suivante pour implémenter un indexeur JSON dans du code managé.

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

Félicitations ! Vous avez appris à intégrer Azure Cosmos DB avec Recherche Azure à l’aide d’un indexeur.

* Pour en savoir plus sur Azure Cosmos DB, consultez la [page du service Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Pour en savoir plus sur la Recherche Azure, consultez la [page du service Recherche](https://azure.microsoft.com/services/search/).
