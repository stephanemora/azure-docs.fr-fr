---
title: 'Tutoriel : Indexer des données semi-structurées dans des objets blob JSON'
titleSuffix: Azure Cognitive Search
description: Apprenez à indexer et à rechercher des objets blob Azure JSON semi-structurés à l’aide des API REST de la recherche cognitive Azure et Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0603ad1fbecf33e5880fd7f18d35af51795f8e39
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251989"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>Tutoriel REST : Indexer et rechercher des données semi-structurées (objets blob JSON) dans la recherche cognitive Azure

La recherche cognitive Azure peut indexer des tableaux et documents JSON dans le stockage d’objets blob Azure à l’aide d’un [indexeur](search-indexer-overview.md) qui sait comment lire des données semi-structurées. Les données semi-structurées contiennent des balises ou des marquages qui séparent le contenu au sein des données. Elles séparent les données non structurées, qui doivent être indexées entièrement, des données formellement structurées qui respectent un modèle de données (tel qu’un schéma de base de données relationnelle), qui peuvent être indexées par champ.

Dans ce tutoriel, vous allez utiliser les [API REST de la recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/) et un client REST pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer une source de données de recherche cognitive Azure pour un conteneur d’objets blob Azure
> * Créer un index de recherche cognitive Azure où stocker le contenu pouvant faire l’objet d’une recherche
> * Configurer et exécuter un indexeur pour lire le conteneur et extraire le contenu de recherche à partir du stockage d’objets blob Azure
> * Effectuer une recherche dans l’index que vous venez de créer

## <a name="prerequisites"></a>Prérequis

Voici les services, outils et données utilisés dans ce guide de démarrage rapide. 

[Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel. 

[Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données.

[Application de bureau Postman](https://www.getpostman.com/) pour envoyer des requêtes à la recherche cognitive Azure.

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contient les données utilisées dans ce tutoriel. Téléchargez et décompressez ce fichier dans son propre dossier. Les données proviennent de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), converties au format JSON pour ce tutoriel.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-postman/get-url-key.png "Obtenir un point de terminaison et une clé d’accès HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

1. [Connectez-vous au portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) pour contenir des exemples de données. Vous pouvez définir le niveau d’accès public sur l’une de ses valeurs valides.

1. Une fois le conteneur créé, ouvrez-le et sélectionnez **Charger** dans la barre de commandes.

   ![Charger dans la barre de commandes](media/search-semi-structured-data/upload-command-bar.png "Charger dans la barre de commandes")

1. Accédez au dossier contenant les exemples de fichiers. Sélectionnez-les tous, puis cliquez sur **Charger**.

   ![Charger des fichiers](media/search-semi-structured-data/clinicalupload.png "Charger des fichiers")

Une fois le chargement terminé, les fichiers doivent apparaître dans leur propre sous-dossier au sein du conteneur de données.

## <a name="set-up-postman"></a>Configurer Postman

Démarrez Postman et paramétrez une requête HTTP. Si vous ne connaissez pas bien cet outil, consultez [Explorer les API REST de Recherche cognitive Azure avec Postman](search-get-started-postman.md).

La méthode de demande pour chaque appel de ce tutoriel est **POST**. Les clés d’en-tête sont « Content-type » et « api-key ». Les valeurs des clés d’en-tête sont, respectivement, « application/json » et votre « clé d’administration » (la clé d’administration est un espace réservé pour votre clé de recherche primaire). Le corps est l’endroit où vous ajoutez le contenu réel de votre appel. Selon le client que vous utilisez, il existe différentes façons de créer votre requête. Nous vous présentons ici les étapes de création de base.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/postmanoverview.png)

Nous utilisons le client Postman pour effectuer trois appels d’API vers votre service de recherche afin de créer une source de données, un index et un indexeur. La source de données inclut un pointeur vers votre compte de stockage et vos données JSON. Votre service de recherche établit la connexion lors du chargement des données.

Les chaînes de requête doivent spécifier un élément « api-version » et chaque appel doit renvoyer une réponse **201 Créé**. L'api-version généralement disponible pour utiliser les tableaux JSON est `2019-05-06`.

Exécutez les trois appels d’API suivants à partir de votre client REST.

## <a name="create-a-data-source"></a>Création d'une source de données

L’[API Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) crée un objet Recherche cognitive Azure qui spécifie les données à indexer.

Le point de terminaison de cet appel est `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Remplacez `[service name]` par le nom de votre service de recherche. 

Pour cet appel, le corps de la requête doit inclure le nom de votre compte de stockage, la clé de compte de stockage et le nom du conteneur d’objets blob. La clé du compte de stockage est disponible dans le portail Azure, dans la section **Clés d’accès** de votre compte de stockage. L’emplacement est indiqué dans l’image suivante :

  ![Recherche de données semi-structurées](media/search-semi-structured-data/storagekeys.png)

Veillez à remplacer `[storage account name]`, `[storage account key]` et `[blob container name]` dans le corps de votre appel avant de l’exécuter.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

La réponse doit ressembler à ce qui suit :

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Création d'un index
    
Le deuxième appel est celui de l’[API de création d’index](https://docs.microsoft.com/rest/api/searchservice/create-index), qui permet de créer un index de recherche cognitive Azure pour stocker toutes les données pouvant faire l’objet de recherches. Un index spécifie tous les paramètres et leurs attributs.

L’URL pour cet appel est `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Remplacez `[service name]` par le nom de votre service de recherche.

Tout d’abord, remplacez l’URL. Ensuite, copiez et collez le code suivant dans le corps de votre appel et exécutez la requête.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

La réponse doit ressembler à ce qui suit :

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Créer et exécuter un indexeur

Un indexeur connecte la source de données, importe les données dans l’index de recherche cible, et peut fournir une planification afin d’automatiser l’actualisation des données. L’API REST est celle qui consiste à [créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

L’URL pour cet appel est `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Remplacez `[service name]` par le nom de votre service de recherche.

Tout d’abord, remplacez l’URL. Ensuite, copiez et collez le code suivant dans le corps de votre appel et envoyez la requête. La requête est traitée immédiatement. Une fois la réponse reçue, vous aurez un index de recherche en texte intégral consultable.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

La réponse doit ressembler à ce qui suit :

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Rechercher vos fichiers JSON

Vous pouvez démarrer la recherche dès que le premier document est chargé. Pour cette tâche, utilisez l’[**Explorateur de recherche**](search-explorer.md) dans le portail.

Dans le portail Azure, ouvrez la page **Vue d’ensemble** du service de recherche, puis recherchez l’index créé dans la liste **Index**.

Veillez à choisir l’index que vous venez de créer. 

  ![Recherche de données non structurées](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Recherche de métadonnées définies par l’utilisateur

Comme auparavant, les données peuvent être interrogées de plusieurs façons : recherche en texte intégral, propriétés système ou métadonnées définies par l’utilisateur. Les propriétés système et les métadonnées définies par l’utilisateur peuvent uniquement être recherchées avec le paramètre `$select` si elles ont été marquées comme **récupérables** lors de la création de l’index cible. Les paramètres de l’index ne peuvent pas être modifiés après leur création. Toutefois, des paramètres supplémentaires peuvent être ajoutés.

`$select=Gender,metadata_storage_size` est un exemple de requête de base qui limite le renvoi à ces deux paramètres.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/lastquery.png)

`$filter=MinimumAge ge 30 and MaximumAge lt 75` est un exemple de requête plus complexe qui renvoie uniquement les résultats pour lesquels le paramètre MinimumAge est supérieur ou égal à 30 et le paramètre MaximumAge inférieur à 75.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/metadatashort.png)

N’hésitez pas à mettre en pratique ce que vous avez appris et à essayer vous-même d’exécuter d’autres requêtes. Notez que vous pouvez utiliser des opérateurs logiques (AND, OR, NOT) et des opérateurs de comparaison (eq, ne, gt, lt, ge, le). Les comparaisons de chaînes sont sensibles à la casse.

Le paramètre `$filter` fonctionne uniquement avec des métadonnées qui ont été marquées comme « filtrables » lors de la création de l’index.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Le moyen le plus rapide de procéder à un nettoyage à la fin d’un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche cognitive Azure. Vous pouvez maintenant supprimer le groupe de ressources pour supprimer définitivement tout ce qu’il contient. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble du service Recherche cognitive Azure.

## <a name="next-steps"></a>Étapes suivantes

Il existe plusieurs approches et plusieurs options pour l’indexation d’objets blob JSON. Votre prochaine étape consiste à passer en revue et à tester les diverses options pour déterminer ce qui fonctionne le mieux pour votre scénario.

> [!div class="nextstepaction"]
> [Guide pratique pour indexer des objets blob JSON avec l’indexeur d’objets blob de la Recherche cognitive Azure](search-howto-index-json-blobs.md)
