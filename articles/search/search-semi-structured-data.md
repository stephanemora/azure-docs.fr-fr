---
title: Tutoriel pour la recherche de données JSON dans le stockage Blob Azure - Recherche Azure
description: Dans ce tutoriel, découvrez comment rechercher des données d’objets blob Azure semi-structurées à l’aide de Recherche Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201345"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Didacticiel : Rechercher des données semi-structurées dans le stockage cloud Azure

Recherche Azure peut indexer des tableaux et documents JSON dans le stockage d’objets blob Azure à l’aide d’un [indexeur](search-indexer-overview.md) qui sait comment lire des données semi-structurées. Les données semi-structurées contiennent des balises ou des marquages qui séparent le contenu au sein des données. Elles séparent les données non structurées, qui doivent être indexées entièrement, des données formellement structurées qui respectent un modèle de données (tel qu’un schéma de base de données relationnelle), qui peuvent être indexées par champ.

Dans ce tutoriel, vous allez utiliser les [API REST Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/) et un client REST pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer une source de données de Recherche Azure pour un conteneur d’objets blob Azure
> * Créer un index Recherche Azure où stocker le contenu de recherche
> * Configurer et exécuter un indexeur pour lire le conteneur et extraire le contenu de recherche à partir du stockage d’objets blob Azure
> * Effectuer une recherche dans l’index que vous venez de créer

> [!NOTE]
> Ce didacticiel implique la prise en charge des tableaux JSON, qui est une fonctionnalité en version préliminaire du service Recherche Azure. Elle n’est pas disponible dans le portail. Pour cette raison, nous utilisons l’API REST d’évaluation, qui fournit cette fonctionnalité, ainsi qu’un outil client REST pour appeler l’API.

## <a name="prerequisites"></a>Prérequis

[Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

[Créez un compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données.

[Utilisez Postman](https://www.getpostman.com/) ou un autre client REST pour envoyer vos requêtes. Les instructions de configuration d’une requête HTTP dans Postman sont fournies dans la section suivante.

## <a name="set-up-postman"></a>Configurer Postman

Démarrez Postman et paramétrez une requête HTTP. Si vous ne connaissez pas bien cet outil, consultez [Explorer les API REST de la Recherche Azure avec Postman](search-fiddler.md).

La méthode de requête « POST » est utilisée pour chaque appel de ce didacticiel. Les clés d’en-tête sont « Content-type » et « api-key ». Les valeurs des clés d’en-tête sont, respectivement, « application/json » et votre « clé d’administration » (la clé d’administration est un espace réservé pour votre clé de recherche primaire). Le corps est l’endroit où vous ajoutez le contenu réel de votre appel. Selon le client que vous utilisez, il existe différentes façons de créer votre requête. Nous vous présentons ici les étapes de création de base.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/postmanoverview.png)

Pour les appels REST traités dans ce didacticiel, vous aurez besoin de votre clé d’API de recherche. Vous la trouverez dans la section **Clés** de votre service de recherche. Cette clé d’API doit figurer dans l’en-tête de chaque appel d’API (remplacez la « clé d’administration » dans la capture d’écran précédente avec cette clé) que vous devrez effectuer dans le cadre de ce didacticiel. Veillez à bien conserver la clé, car vous en aurez besoin pour chaque appel.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

1. **Téléchargez le fichier [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** et décompressez-le dans son propre dossier. Les données proviennent de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), converties au format JSON pour ce tutoriel.

2. Connectez-vous au [portail Azure](https://portal.azure.com), accédez à votre compte Stockage Azure, ouvrez le conteneur **données**, puis cliquez sur **Charger**.

3. Cliquez sur **Avancé**, saisissez « clinical-trials-json », puis chargez tous les fichiers JSON que vous avez téléchargés.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/clinicalupload.png)

Une fois le chargement terminé, les fichiers doivent apparaître dans leur propre sous-dossier au sein du conteneur de données.

## <a name="connect-your-search-service-to-your-container"></a>Connecter votre service de recherche à votre conteneur

Nous utilisons le client Postman pour effectuer trois appels d’API vers votre service de recherche afin de créer une source de données, un index et un indexeur. La source de données inclut un pointeur vers votre compte de stockage et vos données JSON. Votre service de recherche établit la connexion lors du chargement des données.

La chaîne de requête doit contenir une API en préversion (telle que **api-version=2017-11-11-Preview**) et chaque appel doit retourner une réponse **201 Créé**. La version de l’API en disponibilité générale ne permet pas encore de traiter les fichiers JSON en tant que tableau JSON. Pour l’instant, ceci est possible uniquement dans la version préliminaire de l’API.

Exécutez les trois appels d’API suivants à partir de votre client REST.

## <a name="create-a-data-source"></a>Création d'une source de données

Une source de données est un objet Recherche Azure qui spécifie les données à indexer.

Le point de terminaison de cet appel est `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Remplacez `[service name]` par le nom de votre service de recherche. Pour cet appel, vous avez besoin du nom de votre compte de stockage et de la clé de votre compte de stockage. La clé du compte de stockage est disponible dans le portail Azure, dans la section **Clés d’accès** de votre compte de stockage. L’emplacement est indiqué dans l’image suivante :

  ![Recherche de données semi-structurées](media/search-semi-structured-data/storagekeys.png)

Veillez à remplacer les éléments `[storage account name]` et `[storage account key]` dans le corps de votre appel avant d’exécuter ce dernier.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
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
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Création d'un index
    
Le deuxième appel d’API crée un index Recherche Azure. Un index spécifie tous les paramètres et leurs attributs.

L’URL pour cet appel est `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Remplacez `[service name]` par le nom de votre service de recherche.

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

Un indexeur connecte la source de données, importe les données dans l’index de recherche cible, et peut fournir une planification afin d’automatiser l’actualisation des données.

L’URL pour cet appel est `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Remplacez `[service name]` par le nom de votre service de recherche.

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

Vous pouvez maintenant émettre des requêtes sur l’index. Pour cette tâche, utilisez l’[**Explorateur de recherche**](search-explorer.md) dans le portail.

  ![Recherche de données non structurées](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Recherche de métadonnées définies par l’utilisateur

Comme auparavant, les données peuvent être interrogées de plusieurs façons : recherche en texte intégral, propriétés système ou métadonnées définies par l’utilisateur. Les propriétés système et les métadonnées définies par l’utilisateur peuvent uniquement être recherchées avec le paramètre `$select` si elles ont été marquées comme **récupérables** lors de la création de l’index cible. Les paramètres de l’index ne peuvent pas être modifiés après leur création. Toutefois, des paramètres supplémentaires peuvent être ajoutés.

`$select=Gender,metadata_storage_size` est un exemple de requête de base qui limite le renvoi à ces deux paramètres.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/lastquery.png)

`$filter=MinimumAge ge 30 and MaximumAge lt 75` est un exemple de requête plus complexe qui renvoie uniquement les résultats pour lesquels le paramètre MinimumAge est supérieur ou égal à 30 et le paramètre MaximumAge inférieur à 75.

  ![Recherche de données semi-structurées](media/search-semi-structured-data/metadatashort.png)

N’hésitez pas à mettre en pratique ce que vous avez appris et à essayer vous-même d’exécuter d’autres requêtes. Notez que vous pouvez utiliser des opérateurs logiques (AND, OR, NOT) et des opérateurs de comparaison (eq, ne, gt, lt, ge, le). Les comparaisons de chaînes sont sensibles à la casse.

Le paramètre `$filter` fonctionne uniquement avec des métadonnées qui ont été marquées comme « filtrables » lors de la création de l’index.

## <a name="clean-up-resources"></a>Supprimer des ressources

Le moyen le plus rapide de procéder à un nettoyage après un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche Azure. Vous pouvez maintenant supprimer le groupe de ressources pour supprimer définitivement tout ce qu’il contient. Sur le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble du service Recherche Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez attacher des algorithmes intégrant l’intelligence artificielle à un pipeline d’indexeur. Comme prochaine étape, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Indexation de documents dans Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)