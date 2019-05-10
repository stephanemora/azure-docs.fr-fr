---
title: Prise en main de la Base de connaissances (préversion) - Recherche Azure
description: Familiarisez-vous avec les étapes d'envoi de documents enrichis créés par les pipelines d'indexation IA de Recherche Azure vers une base de connaissances de votre compte de stockage Azure. De là, vous pouvez consulter, remodeler et utiliser des documents enrichis dans Recherche Azure et dans d'autres applications.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.openlocfilehash: d9006e3fcfc9691b9f3eec4b86c545fd3fea9f8a
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471754"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Prise en main de la Base de connaissances

La [Base de connaissances](knowledge-store-concept-intro.md) (disponible en préversion) est une nouvelle fonctionnalité de Recherche Azure qui enregistre les enrichissements IA créés dans un pipeline d'indexation à des fins d'exploration des connaissances dans d'autres applications. Vous pouvez également utiliser des enrichissements enregistrés pour comprendre et affiner un pipeline d'indexation Recherche Azure.

Une base de connaissances est définie par un ensemble de compétences. Dans les scénarios de recherche en texte intégral Recherche Azure classiques, un ensemble de compétences est destiné à fournir des enrichissements IA afin de faciliter les recherches de contenu. Pour les scénarios d’exploration des connaissances, le rôle d’un ensemble de compétences consiste à créer, remplir et stocker plusieurs structures de données à des fins d’analyse ou de modélisation dans d’autres applications et processus.

Dans cet exercice, vous allez commencer avec des exemples de données, de services et d'outils pour vous familiariser avec les bases de la création et de l'utilisation d'une base de connaissances, en mettant l'accent sur la définition des compétences.

## <a name="prerequisites"></a>Prérequis

Voici les services, outils et données utilisés dans ce guide de démarrage rapide. 

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel. 

+ [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données. Votre base de connaissances sera présente dans Stockage Azure. 

+ [Créez une ressource Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) au niveau paiement à l'utilisation S0 pour un accès à large spectre à la gamme complète des compétences utilisées dans le cadre des enrichissements IA. Cette ressource et votre service Recherche Azure doivent se trouver dans la même région.

+ L’[application de bureau Postman](https://www.getpostman.com/) permet d’envoyer des requêtes au service Recherche Azure.

+ La [collection Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Caselaw) et ses requêtes préparées permettent de créer une source de données, un index, un ensemble de compétences et un indexeur. Certaines définitions d'objets sont trop longues pour être incluses dans cet article. Vous devez vous procurer cette collection pour pouvoir consulter les définitions d'index et d'ensembles de compétences dans leur intégralité.

+ [Exemple de données de jurisprudence](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) provenant de la page de téléchargement de données publiques en bloc [Projet d'accès à la jurisprudence](https://case.law/bulk/download/). Pour être précis, l'exercice utilise les 10 premiers documents du premier téléchargement (Arkansas). Nous avons chargé un échantillon de 10 documents sur GitHub pour cet exercice.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

    ![Obtenir une clé d’accès et un point de terminaison HTTP](media/search-fiddler/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service.

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

1. [Connectez-vous au portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) pour contenir des exemples de données. Utilisez le nom de conteneur « caselaw-test ». Vous pouvez définir le niveau d’accès public sur l’une de ses valeurs valides.

1. Une fois le conteneur créé, ouvrez-le et sélectionnez **Charger** dans la barre de commandes.

   ![Charger dans la barre de commandes](media/search-semi-structured-data/upload-command-bar.png "Charger dans la barre de commandes")

1. Accédez au dossier contenant l'exemple de fichier **caselaw-sample.json**. Sélectionnez le fichier, puis cliquez sur **Charger**.


## <a name="set-up-postman"></a>Configurer Postman

Démarrez Postman et importez la collection Postman Caselaw. Vous pouvez également configurer une série de requêtes HTTP. Si vous ne connaissez pas bien cet outil, consultez [Explorer les API REST de la Recherche Azure avec Postman](search-fiddler.md).

+ La méthode de requête pour chaque appel de cette procédure est **POST** ou **POST**.
+ Les en-têtes de requête (2) comprennent les éléments suivants : respectivement, « Content-type » défini sur « application/json » et « api-key » défini sur votre « clé d'administration » (la clé d'administration est un espace réservé pour votre clé primaire de recherche). 
+ Vous pouvez ajouter le contenu réel de votre appel dans le corps de la requête. 

  ![Recherche de données semi-structurées](media/search-semi-structured-data/postmanoverview.png)

Nous utilisons Postman pour exécuter quatre appels d’API vers votre service de recherche, ce qui crée, successivement, une source de données, un index, un ensemble de compétences et un indexeur. La source de données comprend un pointeur dirigé vers votre compte de stockage Azure et les données JSON. Votre service de recherche établit la connexion lors de l'importation des données.

Cette procédure pas à pas repose essentiellement sur la [création d'un ensemble de compétences](#create-skillset) ; elle spécifie les étapes d'enrichissement et la façon dont les données sont conservées dans une base de connaissances.

Le point de terminaison de l'URL doit spécifier un élément « api-version » et chaque appel doit renvoyer une réponse **201 Créé**. L’élément « api-version » de la préversion permettant de créer un ensemble de compétences avec la prise en charge d’une base de connaissances est `2019-05-06-Preview` (respect de la casse).

Exécutez les appels d'API suivants à partir de votre client REST.

## <a name="create-a-data-source"></a>Création d'une source de données

L'[API Créer une source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) crée un objet Recherche Azure qui spécifie les données à indexer.

Le point de terminaison de cet appel est `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Remplacez `[service name]` par le nom de votre service de recherche. 

2. Pour cet appel, le corps de la requête doit inclure la chaîne de connexion de votre compte de stockage et le nom du conteneur d'objets blob. La connexion est disponible sur le portail Azure, dans la section **Clés d'accès** de votre compte de stockage. 

   Veillez à remplacer la chaîne de connexion et le nom du conteneur d'objets blob dans le corps de la requête avant d'exécuter l'appel.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. Envoyez la demande. La réponse doit être **201** et le corps de la requête doit être presque identique à la charge utile de la requête que vous avez fournie.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Création d'un index
    
Le deuxième appel est celui de l’[API de création d’index](https://docs.microsoft.com/rest/api/searchservice/create-data-source), qui permet de créer un index Recherche Azure pour stocker toutes les données pouvant faire l’objet de recherches. Un index spécifie tous les champs, paramètres et attributs.

Vous n'avez pas nécessairement besoin d'un index pour l'exploration des connaissances, mais un indexeur ne s'exécutera que si un index est fourni. 

L'URL de cet appel est `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Remplacez `[service name]` par le nom de votre service de recherche.

2. Copiez la définition de l'index depuis la requête Créer un index de la collection Postman vers le corps de la requête. La définition de l'index comporte plusieurs centaines de lignes, ce qui est trop long pour être illustré ici. 

   L'enveloppe extérieure d'un index est constituée des éléments suivants. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. La collection `fields` contient la majeure partie de la définition de l'index. Elle inclut des champs simples, des [champs complexes](search-howto-complex-data-types.md) avec des sous-structures imbriquées, et des collections.

   Consultez la définition du champ de `casebody` sur les lignes 302 à 384. Notez qu'un champ complexe peut contenir d'autres champs complexes lorsque des représentations hiérarchiques sont nécessaires.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. Envoyez la demande. 

   La réponse doit être **201** et être semblable à l'exemple suivant, qui présente les premiers champs :

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Créer un ensemble de compétences et une base de connaissances

L'API [Créer un ensemble de compétences](https://docs.microsoft.com/rest/api/searchservice/create-skillset) crée un objet Recherche Azure qui spécifie quelles compétences cognitives appeler, comment enchaîner les compétences et, plus important encore pour cette procédure pas à pas, comment spécifier une base de connaissances.

Le point de terminaison de cet appel est `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Remplacez `[service name]` par le nom de votre service de recherche.

2. Copiez la définition de l'ensemble de compétences depuis la requête Créer un ensemble de compétences de la collection Postman vers le corps de la requête. La définition de l'ensemble de compétences comporte plusieurs centaines de lignes, ce qui est trop long pour être illustré ici, mais il s'agit du principal objectif de cette procédure.

   L'enveloppe extérieure d'un ensemble de compétences comprend les éléments suivants. La collection `skills` définit les enrichissements présents en mémoire, mais la définition de la `knowledgeStore` spécifie le mode de stockage de la sortie. La définition de `cognitiveServices` constitue votre lien avec les moteurs d'enrichissement IA.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Commencez par définir la clé et la chaîne de connexion `cognitiveServices` et `knowledgeStore`. Dans l'exemple, ces chaînes se trouvent après la définition de l'ensemble de compétences, vers la fin du corps de la requête. Utilisez une ressource Cognitive Services, provisionnée au niveau S0, située dans la même région que Recherche Azure.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

3. Examinez la collection de compétences, en particulier les compétences Modélisateur des lignes 85 et 170, respectivement. La compétence Modélisateur est importante car elle assemble les structures de données que vous souhaitez utiliser pour l'exploration des connaissances. Pendant l'exécution de l'ensemble de compétences, ces structures sont uniquement disponibles en mémoire, mais lorsque vous passerez à l'étape suivante, vous verrez comment cette sortie peut être enregistrée dans une base de connaissances pour une exploration ultérieure.

   L’extrait de code suivant provient de la ligne 217. 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

3. Examinez l’élément `projections` de `knowledgeStore`, à partir de la ligne 262. Les projections spécifient la composition de la base de connaissances. Les projections sont spécifiées dans des paires tables-objets, mais une seule à la fois pour le moment. Comme vous pouvez le constater dans la première projection, `tables` est spécifié, mais pas `objects`. Dans la seconde, c'est le contraire.

   Dans Stockage Azure, des tables seront créées dans Stockage Tables pour chaque table que vous créez, et chaque objet recevra un conteneur dans Stockage Blob.

   Les objets blob contiennent généralement l’expression complète d’un enrichissement. Les tables contiennent généralement des enrichissements partiels, dans des combinaisons que vous organisez à des fins spécifiques. Cet exemple présente une table Cases et une table Opinions, mais les autres tables comme Entities, Attorneys, Judges et Parties ne sont pas affichées.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

5. Envoyez la demande. La réponse doit être **201**, et être semblable à l'exemple suivant, avec la première partie de la réponse.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Créer et exécuter un indexeur

L'[API Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer) crée et exécute immédiatement un indexeur. Toutes les définitions que vous avez créées jusqu'à présent sont mises en œuvre lors de cette étape. L'indexeur s'exécute immédiatement car il n'existe pas dans le service. Une fois qu'il existe, un appel POST à ​​un indexeur existant est une opération de mise à jour.

Le point de terminaison de cet appel est `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Remplacez `[service name]` par le nom de votre service de recherche. 

2. Pour cet appel, le corps de la requête spécifie le nom de l'indexeur. Une source de données et un index sont requis par l'indexeur. Un ensemble de compétences est facultatif pour un indexeur, mais obligatoire pour l'enrichissement IA.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. Envoyez la demande. La réponse doit être **201**, et le corps de la réponse doit être presque identique à la charge utile de la requête que vous avez fournie (par souci de concision).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Explorer la base de connaissances

Vous pouvez entamer l'exploration dès que le premier document est importé. Pour cette tâche, utilisez l'[**Explorateur Stockage**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) du portail.

Vous devez bien comprendre qu'une base de connaissances est entièrement détachée de Recherche Azure. L'index Recherche Azure et la base de connaissances contiennent tous deux une représentation des données et du contenu, mais il s'agit de leurs seuls points communs. Utilisez l'index pour la recherche en texte intégral, la recherche filtrée et tous les scénarios pris en charge dans Recherche Azure. Vous pouvez également associer d'autres outils à votre base de connaissances pour analyser le contenu.

## <a name="takeaways"></a>Éléments importants à retenir

Vous venez de créer votre première base de connaissances dans Stockage Azure et d'utiliser l'Explorateur Stockage pour visualiser les enrichissements. Il s'agit de l'expérience de base pour travailler avec des enrichissements stockés. 

## <a name="next-steps"></a>Étapes suivantes

La compétence Modélisateur permet de créer des formulaires de données granulaires qui peuvent être combinés pour créer de nouveaux formulaires. L'étape suivante consiste à consulter la page de référence de cette compétence pour obtenir des informations sur son utilisation.

> [!div class="nextstepaction"]
> [Référence relative à la compétence Modélisateur](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->