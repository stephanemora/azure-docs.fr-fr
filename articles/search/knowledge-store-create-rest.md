---
title: Créer une base de connaissances à l’aide de REST - Recherche Azure
description: Créez une base de connaissances Recherche Azure pour les enrichissements persistants du pipeline de recherche cognitive, à l’aide de l’API REST et de Postman.
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: e28fa919c4c656b9ceb1d34806c3ef08aec2df2c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719934"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Créer une base de connaissances Recherche Azure à l’aide de REST

La base de connaissances est une fonctionnalité du service Recherche Azure. Elle permet de conserver la sortie d’un pipeline enrichi par IA (intelligence artificielle) pour une analyse future ou tout autre traitement en aval. Un pipeline enrichi par IA accepte des fichiers image ou des fichiers texte non structurés, les indexe à l’aide du service Recherche Azure, applique des enrichissements par IA à partir de Cognitive Services (par exemple l’analyse d’images et le traitement du langage naturel), puis enregistre les résultats au sein d’une base de connaissances dans Stockage Azure. Vous pouvez ensuite utiliser des outils tels que Power BI ou l’Explorateur Stockage pour explorer la base de connaissances.

Dans cet article, vous allez utiliser l’interface de l’API REST pour ingérer, indexer et appliquer des enrichissements par IA à un ensemble d’avis sur des hôtels. Les avis sur les hôtels sont importés dans Stockage Blob Azure, et les résultats sont enregistrés en tant que base de connaissances dans Stockage Table Azure.

Une fois que vous avez créé la base de connaissances, vous pouvez apprendre à y accéder à l’aide de l’[Explorateur Stockage](knowledge-store-view-storage-explorer.md) ou de [Power BI](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1 - Créer les services

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

+ [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données et la base de connaissances. Votre compte de stockage doit utiliser la même région (par exemple USA Ouest) pour votre service Recherche Azure. Le *Type de compte* doit être *StorageV2 (v2 à usage général)* (valeur par défaut) ou *Storage (v1 à usage général)* .

+ Recommandé : L’[application de bureau Postman](https://www.getpostman.com/) permet d’envoyer des requêtes au service Recherche Azure. Vous pouvez vous servir de l’API REST avec n’importe quel outil capable d’utiliser les requêtes et réponses HTTP. Postman est un bon choix pour l’exploration des API REST, et sera utilisé dans cet article. De plus, le [code source](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) de cet article inclut une collection de requêtes Postman. 

## <a name="2---store-the-data"></a>2 - Stocker les données

Chargez le fichier CSV d’avis sur les hôtels dans Stockage Blob Azure afin de le rendre accessible à un indexeur Recherche Azure et pour qu’il soit alimenté via le pipeline d’enrichissement par IA.

### <a name="create-an-azure-blob-container-with-the-data"></a>Créer un conteneur d’objets blob Azure avec les données

1. [Téléchargez les données d’avis sur les hôtels enregistrées dans un fichier CSV (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Ces données proviennent de Kaggle.com et contiennent des commentaires de clients sur les hôtels.
1. [Connectez-vous au portail Azure](https://portal.azure.com), puis accédez à votre compte de stockage Azure.
1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Pour créer le conteneur, dans la barre de navigation de gauche correspondant à votre compte de stockage, cliquez sur **Objets blob**, puis sur **+ Conteneur** dans la barre de commandes.
1. Pour le **Nom** du nouveau conteneur, entrez `hotel-reviews`.
1. Sélectionnez un **Niveau d’accès public**. Nous avons utilisé la valeur par défaut.
1. Cliquez sur **OK** pour créer le conteneur d’objets blob Azure.
1. Ouvrez le nouveau conteneur `hotels-review`, cliquez sur **Charger**, puis sélectionnez le fichier **HotelReviews-Free.csv** que vous avez téléchargé à la première étape.

    ![Charger les données](media/knowledge-store-create-portal/upload-command-bar.png "Charger les avis sur les hôtels")

1. Cliquez sur **Charger** pour importer le fichier CSV dans Stockage Blob Azure. Le nouveau conteneur s’affiche.

    ![Créer le conteneur d’objets blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Créer le conteneur d’objets blob Azure")

## <a name="3---configure-postman"></a>3 - Configurer Postman

Téléchargez le [code source de la collection Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json), puis importez-le dans Postman via **Fichier, Importer**. Passez à l’onglet **Collections**, cliquez sur le bouton **...** , puis sélectionnez **Modifier**. 

![Navigation dans l’application Postman](media/knowledge-store-create-rest/postman-edit-menu.png "Accéder au menu Edit (Edition) dans Postman")

Dans la boîte de dialogue d’édition qui s’affiche, accédez à l’onglet **Variables**. 

L’onglet **Variables** vous permet d’ajouter des valeurs que Postman va remplacer chaque fois qu’il les rencontrera entre deux accolades. Par exemple, Postman va remplacer le symbole `{{admin-key}}` par la « valeur actuelle » de `admin-key`. Postman va effectuer cette substitution dans les URL, les en-têtes, le corps de la requête, etc. 

Vous trouverez la valeur de `admin-key` sous l’onglet**Keys** (Clés) du service Recherche. Vous devez remplacer `search-service-name` et `storage-account-name` par les valeurs que vous avez choisies à l’[Étape 1](#1---create-services). Définissez `storage-connection-string` à partir de la valeur de l’onglet **Access Keys** (Clés d’accès) du compte de stockage. Vous pouvez laisser les autres valeurs inchangées.

![Onglet des variables de l’application Postman](media/knowledge-store-create-rest/postman-variables-window.png "Fenêtre des variables de Postman")


| Variable    | Comment les obtenir |
|-------------|-----------------|
| `admin-key` | Service Recherche, onglet **Keys** (Clés)              |
| `api-version` | Laisser « 2019-05-06-Preview » |
| `datasource-name` | Laisser « hotel-reviews-ds » | 
| `indexer-name` | Laisser « hotel-reviews-ixr » | 
| `index-name` | Laisser « hotel-reviews-ix » | 
| `search-service-name` | Service Recherche, nom principal. L’URL est `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Laisser « hotel-reviews-ss » | 
| `storage-account-name` | Compte de stockage, nom principal | 
| `storage-connection-string` | Compte de stockage, onglet **Access Keys** (clés d’accès), **chaîne de connexion** **clé1** | 
| `storage-container-name` | Laisser « hotel-reviews » | 

### <a name="review-the-request-collection-in-postman"></a>Passer en revue la collection de requêtes dans Postman

Pour créer une base de connaissances, vous devez émettre quatre requêtes HTTP : 

1. Une requête PUT pour créer l’index. Cet index contient les données utilisées et retournées par Recherche Azure.
1. Une requête POST pour créer la source de données. Cette source de données connecte le comportement du service Recherche Azure au compte de stockage des données et de la base de connaissances. 
1. Une requête PUT pour créer l’ensemble de compétences. L’ensemble de compétences spécifie les enrichissements appliqués à vos données et à la structure de la base de connaissances.
1. Une requête PUT pour créer l’indexeur. L’exécution de l’indexeur lit les données, applique l’ensemble de compétences et stocke les résultats. Vous devez exécuter cette requête en dernier.

Le [code source](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) contient une collection Postman avec ces quatre requêtes. Pour émettre les requêtes, passez à l’onglet de la requête dans Postman, puis ajoutez les en-têtes `api-key` et `Content-Type`. Affectez à `api-key` la valeur `{{admin-key}}`. Affectez à `Content-type` la valeur `application/json`. 

> [!div class="mx-imgBorder"]
> ![Capture d’écran illustrant l’interface de Postman pour les en-têtes](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Vous allez devoir définir les en-têtes `api-key` et `Content-type` dans toutes vos requêtes. Si une variable est reconnue par Postman, elle est affichée en orange, à l’image de `{{admin-key}}` dans la capture d’écran. Si la variable est mal orthographiée, elle est affichée en rouge.
>

## <a name="4---create-an-azure-search-index"></a>4 - Créer un index Recherche Azure

Vous devez créer un index Recherche Azure pour représenter les données sur lesquelles vous souhaitez effectuer des recherches, des filtrages et des améliorations. Pour créer l’index, vous devez émettre une requête PUT vers `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman remplace les symboles entre doubles accolades, par exemple `{{search-service-name}}`, `{{index-name}}` et `{{api-version}}`, par les valeurs spécifiées à l’[Étape 3](#3---configure-postman). Si vous utilisez un autre outil pour émettre vos commandes REST, vous devrez remplacer ces variables vous-même.

Spécifiez la structure de votre index Recherche Azure dans le corps de la requête. Dans Postman, après avoir défini les en-têtes `api-key` et `Content-type`, passez au volet **Body** (Corps) de la requête. Vous devez voir le code JSON suivant, mais dans le cas contraire, choisissez **Raw** et **JSON (application/json)** , puis collez le code suivant en tant que corps :

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Vous allez voir que cette définition d’index est une combinaison de données que vous souhaitez présenter à l’utilisateur (nom de l’hôtel, contenu de l’avis, date, etc.), de métadonnées de recherche et de données d’amélioration de l’IA (sentiment, phrases clés et langue).

Appuyez sur le bouton **Send** (Envoyer) pour émettre la requête PUT. Vous devez recevoir le message d’état `201 - Created`. Si vous recevez un autre message d’état, le volet **Body** (Corps) affiche une réponse JSON avec un message d’erreur. 

## <a name="5---create-the-datasource"></a>5 - Créer la source de données

Vous devez à présent connecter Recherche Azure aux données relatives aux hôtels enregistrées à l’[Étape 2](#2---store-the-data). Pour créer la source de données, émettez une requête POST vers `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Vous devez à nouveau définir les en-têtes `api-key` et `Content-Type`, comme vous l’avez déjà fait. 

Dans Postman, ouvrez la requête « Create Datasource » (Créer une source de données). Passez au volet **Body** (Corps), qui doit contenir le code suivant :

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Appuyez sur le bouton **Send** pour émettre la requête POST. 

## <a name="6---create-the-skillset"></a>6 - Créer l’ensemble de compétences 

L’étape suivante consiste à spécifier l’ensemble de compétences, qui indique à la fois les améliorations à appliquer et la base de connaissances où sont stockés les résultats. Dans Postman, ouvrez l’onglet « Create the Skillset » (Créer l’ensemble de compétences). Cette requête envoie un PUT à `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Définissez les en-têtes `api-key` et `Content-type` comme vous l’avez déjà fait. 

Il existe deux grands objets de niveau supérieur : `"skills"` et `"knowledgeStore"`. Chaque objet contenu dans l’objet `"skills"` est un service d’enrichissement. Chaque service d’enrichissement comporte `"inputs"` et `"outputs"`. Notez que `LanguageDetectionSkill` a une sortie `targetName` qui correspond à `"Language"`. La valeur de ce nœud est utilisée par la plupart des autres compétences en tant qu’entrée, la source étant `document/Language`. Cette possibilité d’utiliser la sortie d’un nœud en tant qu’entrée d’un autre nœud est encore plus évidente dans `ShaperSkill`, qui spécifie le flux des données dans les tables de la base de connaissances.

L’objet `"knowledge_store"` se connecte au compte de stockage via la variable Postman`{{storage-connection-string}}`. Il contient ensuite un ensemble de mappages entre le document amélioré et les tables et colonnes disponibles dans la base de connaissances elle-même. 

Pour générer l’ensemble de compétences, émettez la requête PUT en appuyant sur le bouton **Send** dans Postman.

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="7---create-the-indexer"></a>7 - Créer l’indexeur

La dernière étape consiste à créer l’indexeur, qui lit les données de manière effective et active l’ensemble de compétences. Dans Postman, passez à la requête « Create Indexer » (Créer l’indexeur), puis examinez le corps. Comme vous pouvez le voir, la définition de l’indexeur fait référence à plusieurs autres ressources que vous avez déjà créées, à savoir la source de données, l’index et l’ensemble de compétences. 

L’objet `"parameters/configuration"` contrôle la manière dont l’indexeur ingère les données. Dans ce cas, les données d’entrée se trouvent dans un seul document comportant une ligne d’en-tête et des valeurs séparées par des virgules. La clé de document est un identificateur unique du document. Avant l’encodage, elle correspond à l’URL du document source. Enfin, les valeurs de sortie de l’ensemble de compétences, par exemple le code de langue, les sentiments et les phrases clés, sont mappées à leurs emplacements appropriés dans le document. Notez que même s’il existe une seule valeur pour `Language`, `Sentiment` est appliqué à chaque élément du tableau de `pages`. `Keyphrases` est lui-même un tableau et est également appliqué à chaque élément du tableau `pages`.

Une fois que vous avez défini les en-têtes `api-key` et `Content-type`, et vérifié que le corps de la requête est similaire au code source ci-après, appuyez sur **Send** dans Postman. Postman émet un PUT de la requête vers `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Recherche Azure crée et exécute l’indexeur. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="8---run-the-indexer"></a>8 - Exécuter l’indexeur 

Dans le portail Azure, accédez à la **Vue d’ensemble** du service Recherche, puis sélectionnez l’onglet **Indexeurs**. Cliquez sur le **hotels-reviews-ixr** que vous avez créé à l’étape précédente. Si l’indexeur n’a pas déjà été exécuté, appuyez sur le bouton **Run** (Exécuter). La tâche d’indexation peut déclencher des avertissements liés à la reconnaissance de la langue, car les données incluent des avis écrits dans des langues qui ne sont pas encore prises en charge par les compétences cognitives. 

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez enrichi vos données à l’aide de Cognitive Services et que vous avez projeté les résultats dans une base de connaissances, vous pouvez utiliser l’Explorateur Stockage ou Power BI pour explorer votre jeu de données enrichi.

Pour apprendre à explorer cette base de connaissances à l’aide de l’Explorateur Stockage, consultez la procédure pas à pas suivante.

> [!div class="nextstepaction"]
> [Voir avec l’Explorateur Stockage](knowledge-store-view-storage-explorer.md)

Pour apprendre à connecter cette base de connaissances à Power BI, consultez la procédure pas à pas suivante.

> [!div class="nextstepaction"]
> [Connexion avec Power BI](knowledge-store-connect-power-bi.md)

Si vous souhaitez répéter cet exercice ou essayer une autre procédure pas à pas d’enrichissement par IA, supprimez l’indexeur *hotel-reviews-idxr*. La suppression de l’indexeur réinitialise le compteur de transactions quotidiennes gratuites à zéro.
