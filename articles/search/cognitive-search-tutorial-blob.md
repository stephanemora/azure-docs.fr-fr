---
title: 'Didacticiel : Appeler des API REST Cognitive Services dans un pipeline d’indexation - Recherche Azure'
description: Examinez en détail un exemple d’extraction de données, de traitement du langage naturel et d’image par l’IA dans l’indexation Recherche Azure pour l’extraction et la transformation de données sur des objets blobs JSON à l’aide de Postman et de l’API REST.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 292368687ea30b87f4a1fa851027f9bde38c9549
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485779"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Tutoriel REST : Appeler des API Cognitive Services dans un pipeline d’indexation Recherche Azure

Dans ce tutoriel, vous apprendrez les mécanismes d’enrichissement des données de programmation dans Recherche Azure à l’aide de *compétences cognitives*. Les compétences sont secondées par des fonctions d’analyse des images et de traitement en langage naturel (NLP) dans Cognitive Services. Par le biais de la configuration et de la composition de compétences, vous pouvez extraire du texte et des représentations sous forme de texte d’un fichier image ou document analysé. Vous pouvez également détecter la langue, les entités, les expressions clés et bien plus encore. Le résultat final est un contenu supplémentaire riche dans un index Recherche Azure, créé avec des enrichissements IA dans un pipeline d’indexation. 

Dans ce tutoriel, vous effectuez des appels d’API REST pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un pipeline d’indexation qui enrichit les exemples de données destinés à un index
> * Appliquer des compétences intégrées : reconnaissance d’entité, détection de la langue, manipulation de texte et extraction d’expression clé
> * Découvrir comment chaîner des compétences en mappant les entrées aux sorties dans un ensemble de compétences
> * Exécuter des demandes et passer en revue les résultats
> * Réinitialiser l’index et les indexeurs pour un développement ultérieur

La sortie obtenue est un index de recherche en texte intégral sur Recherche Azure. Vous pouvez améliorer cet index avec d’autres fonctionnalités standard, telles que des [synonymes](search-synonyms.md), des [profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), des [analyseurs](search-analyzers.md) et des [filtres](search-filters.md).

Ce tutoriel s’exécute sur le service gratuit. Toutefois, le nombre de transactions gratuites est limité à 20 documents par jour. Si vous souhaitez exécuter ce tutoriel plusieurs fois par jour, utilisez un ensemble de fichiers plus petit pour pouvoir intégrer davantage d’exécutions.

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant plusieurs algorithmes d’IA, vous devez [attacher une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais sont applicables durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images durant la phase d’extraction du contenu des documents du service Recherche Azure. L’extraction de texte à partir des documents est gratuite.
>
> L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l'extraction d'images sont présentés sur la [page de tarification du service Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Voici les services, outils et données utilisés dans ce tutoriel. 

+ [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données. Vérifiez que le compte de stockage se trouve dans la même région que la Recherche Azure.

+ L’[application de bureau Postman](https://www.getpostman.com/) permet d’effectuer des appels REST au service Recherche Azure.

+ Les [exemples de données](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) se composent d’un petit ensemble de fichiers de types différents. 

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir une clé d’accès et un point de terminaison HTTP](media/search-get-started-postman/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

Le pipeline d’enrichissement extrait des données des sources de données Azure. Les données sources doivent provenir d’un type de source de données pris en charge d’un [indexeur Recherche Azure](search-indexer-overview.md). Stockage Table Azure n’est pas pris en charge pour la recherche cognitive. Pour cet exercice, nous utilisons le stockage d’objets blob pour présenter plusieurs types de contenu.

1. [Connectez-vous au portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) pour contenir des exemples de données. Vous pouvez définir le niveau d’accès public sur l’une de ses valeurs valides.

1. Une fois que le conteneur est créé, ouvrez-le, puis sélectionnez **Charger** dans la barre de commandes pour charger les exemples de fichiers que vous avez téléchargés lors de l’étape précédente.

   ![Fichiers source sur le Stockage Blob Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Une fois que les fichiers d’exemple sont chargés, obtenez le nom du conteneur et une chaîne de connexion pour votre stockage d’objets Blob. Pour ce faire, accédez à votre compte de stockage dans le portail Azure. Dans **Clés d’accès**, copiez le champ **Chaîne de connexion**.

   La chaîne de connexion doit être une URL similaire à l’exemple suivant :

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Il existe d’autres manières de spécifier la chaîne de connexion, par exemple en fournissant une signature d’accès partagé. Pour en savoir plus sur les informations d’identification de source de données, consultez [Indexation du stockage d’objets blob Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-postman"></a>Configurer Postman

Démarrez Postman et paramétrez une requête HTTP. Si vous ne connaissez pas bien cet outil, consultez [Explorer les API REST de la Recherche Azure avec Postman](search-get-started-postman.md).

Les méthodes de requête utilisées dans ce tutoriel sont **POST**, **PUT** et **GET**. Les clés d’en-tête sont « Content-type » définie sur « application/json » et « api-key » définie sur une clé d’administration de votre service Recherche Azure. Le corps est l’endroit où vous ajoutez le contenu réel de votre appel. 

  ![Recherche de données semi-structurées](media/search-semi-structured-data/postmanoverview.png)

Nous utilisons Postman pour effectuer quatre appels d’API vers votre service de recherche afin de créer une source de données, un ensemble de compétences, un index et un indexeur. La source de données inclut un pointeur vers votre compte de stockage et vos données JSON. Votre service de recherche établit la connexion lors du chargement des données.


## <a name="create-a-data-source"></a>Création d'une source de données

Maintenant que vos services et fichiers sources sont préparés, commencez à assembler les composants de votre pipeline d’indexation. Commencez par un [objet source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) qui indique à Recherche Azure comment récupérer des données d’une source externe.

Dans l’en-tête de requête, fournissez le nom du service que vous avez utilisé lors de la création du service Recherche Azure et la clé d’API générée pour votre service de recherche. Dans le corps de la demande, spécifiez le nom du conteneur blob et la chaîne de connexion.

### <a name="sample-request"></a>Exemple de demande
```http
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Envoyez la demande. L’outil de test web doit retourner un code d’état 201 confirmant la réussite. 

Dans la mesure où il s’agit de votre première demande, vérifiez le portail Azure pour confirmer que la source de données a été créée dans Recherche Azure. Dans la page du tableau de bord du service de recherche, vérifiez que la liste Sources de données comporte un nouvel élément. Vous devrez peut-être attendre quelques minutes que la page du portail soit actualisée. 

  ![Vignette de sources de données dans le portail](./media/cognitive-search-tutorial-blob/data-source-tile.png "Vignette de sources de données dans le portail")

Si vous avez obtenu une erreur 403 ou 404, vérifiez la construction de la demande : `api-version=2019-05-06` doit être sur le point de terminaison, `api-key` doit être dans l’en-tête après `Content-Type` et sa valeur doit être valide pour un service de recherche. Vous pouvez réutiliser l’en-tête pour les étapes restantes de ce tutoriel.

## <a name="create-a-skillset"></a>Créer un ensemble de compétences

Dans cette étape, vous définissez un ensemble d’étapes d’enrichissement que vous souhaitez appliquer à vos données. Vous appelez chaque étape d’enrichissement une *compétence* et l’ensemble des étapes d’enrichissement un *ensemble de compétences*. Ce tutoriel utilise des [compétences cognitives prédéfinies](cognitive-search-predefined-skills.md) pour l’ensemble de compétences :

+ [Détection de la langue](cognitive-search-skill-language-detection.md) pour identifier la langue du contenu.

+ [Fractionnement de texte](cognitive-search-skill-textsplit.md) pour découper un grand contenu en plus petits morceaux avant d’appeler la compétence d’extraction de phrases clés. L’extraction de phrases clés accepte des entrées de 50 000 caractères au maximum. Certains fichiers d’exemple doivent être fractionnés pour satisfaire cette limite.

+ [Reconnaissance d’entité](cognitive-search-skill-entity-recognition.md) pour extraire les noms d’organisations du contenu dans le conteneur d’objets blob.

+ [Extraction de phrases clés](cognitive-search-skill-keyphrases.md) pour extraire les principales expressions clés. 

### <a name="sample-request"></a>Exemple de demande
Avant d’effectuer cet appel REST, n’oubliez pas de remplacer le nom du service et la clé d’administration dans la demande ci-dessous si votre outil ne conserve pas l’en-tête de demande entre les appels. 

Cette demande crée un ensemble de compétences. Référencez le nom d’ensemble de compétences ```demoskillset``` pour le reste de ce tutoriel.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations", "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "languageCode",
          "targetName": "languageCode"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "textSplitMode" : "pages",
      "maximumPageLength": 4000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        },
        {
          "name": "languageCode",
          "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
      "context": "/document/pages/*",
      "inputs": [
        {
          "name": "text", "source": "/document/pages/*"
        },
        {
          "name":"languageCode", "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "keyPhrases",
          "targetName": "keyPhrases"
        }
      ]
    }
  ]
}
```

Envoyez la demande. L’outil de test web doit retourner un code d’état 201 confirmant la réussite. 

#### <a name="explore-the-request-body"></a>Explorer le corps de la demande

Notez comment la compétence d’extraction de phrases clés est appliquée pour chaque page. En définissant le contexte sur ```"document/pages/*"```, vous exécutez cette enrichisseur pour chaque membre du document/tableau de pages (pour chaque page dans le document).

Chaque compétence s’exécute sur le contenu du document. Au cours du traitement, Recherche Azure interprète chaque document pour lire le contenu de fichiers de différents formats. Le texte trouvé originaire du fichier source est placé dans un champ ```content``` généré, un pour chaque document. Par conséquent, définissez l’entrée en tant que ```"/document/content"```.

Une représentation graphique de l’ensemble de compétences est présentée ci-dessous. 

![Comprendre un ensemble de compétences](media/cognitive-search-tutorial-blob/skillset.png "Comprendre un ensemble de compétences")

Les sorties peuvent être mappées à un index, utilisées comme entrée d’une compétence en aval, ou les deux, comme c’est le cas avec le code de langue. Dans l’index, un code de langue est utile pour le filtrage. En tant qu’entrée, le code de langue est utilisé par les compétences d’analyse de texte pour informer les règles linguistiques en matière de césure de mots.

Pour plus d’informations sur les principes de base des ensembles de compétences, consultez [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Création d'un index

Dans cette section, vous définissez le schéma d’index en spécifiant les champs à inclure dans l’index de recherche et les attributs de recherche pour chaque champ. Les champs ont un type et peuvent prendre des attributs qui déterminent la façon dont le champ est utilisé (pour la recherche, le tri, etc.). Les noms des champs dans un index ne sont pas tenus de correspondre exactement aux noms des champs dans la source. Dans une étape ultérieure, vous ajoutez des mappages de champs dans un indexeur pour connecter les champs sources et de destination. Pour cette étape, définissez l’index à l’aide de conventions d’affectation de noms de champs appropriées à votre application de recherche.

Cet exercice utilise les champs et les types de champ suivants :

| Noms de champs : | `id`       | Contenu   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Types de champ : | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Exemple de demande
Avant d’effectuer cet appel REST, n’oubliez pas de remplacer le nom du service et la clé d’administration dans la demande ci-dessous si votre outil ne conserve pas l’en-tête de demande entre les appels. 

Cette demande crée un index. Utilisez le nom d’index ```demoindex``` pour le reste de ce tutoriel.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Envoyez la demande. L’outil de test web doit retourner un code d’état 201 confirmant la réussite. 

Pour en savoir plus sur la définition d’un index, consultez [Créer un index (API REST de Recherche Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Créer un indexeur, mapper les champs et exécuter des transformations

À ce stade, vous avez créé une source de données, un ensemble de compétences et un index. Ces trois composants deviennent partie intégrante d’un [indexeur](search-indexer-overview.md) qui extrait chaque élément pour l’insérer dans une opération unique à plusieurs phases. Pour lier ces éléments en un indexeur, vous devez définir des mappages de champs. 

+ Les fieldMappings sont traités avant l’ensemble de compétences, en mappant les champs sources de la source de données sur des champs cibles dans un index. Si les noms et types de champ sont identiques aux deux extrémités, aucun mappage n’est nécessaire.

+ Les outputFieldMappings sont traités après l’ensemble de compétences, en référençant les sourceFieldNames qui n’existent pas tant que le décodage de document ou l’enrichissement ne les ont pas créés. targetFieldName est un champ dans un index.

En plus de raccrocher des entrées à des sorties, vous pouvez également utiliser les mappages de champs pour aplatir les structures de données. Pour plus d’informations, consultez [Guide pratique pour mapper des champs enrichis sur un index pouvant faire l’objet d’une recherche](cognitive-search-output-field-mapping.md).

### <a name="sample-request"></a>Exemple de demande

Avant d’effectuer cet appel REST, n’oubliez pas de remplacer le nom du service et la clé d’administration dans la demande ci-dessous si votre outil ne conserve pas l’en-tête de demande entre les appels. 

De plus, fournissez le nom de votre indexeur. Vous pouvez le référencer en tant que ```demoindexer``` pour le reste de ce tutoriel.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
  "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}
```

Envoyez la demande. L’outil de test web doit retourner un code d’état 201 confirmant la réussite du processus. 

Attendez-vous à ce que cette étape prenne plusieurs minutes. Même si le jeu de données est petit, les compétences analytiques exigent des calculs intensifs. Certaines compétences, telles que l’analyse d’image, sont des opérations longues.

> [!TIP]
> La création d’un indexeur appelle le pipeline. En cas de problèmes pour atteindre les données, mapper les entrées et les sorties, ou ordonner les opérations, ceux-ci apparaissent à ce stade. Pour réexécuter le pipeline avec des modifications de code ou de script, vous pouvez être amené à supprimer d’abord des objets. Pour plus d’informations, consultez [Réinitialiser et réexécuter](#reset).

#### <a name="explore-the-request-body"></a>Explorer le corps de la demande

Le script affecte à ```"maxFailedItems"``` la valeur -1, ce qui indique au moteur d’indexation d’ignorer les erreurs au cours de l’importation des données. Cela est utile car très peu de documents figurent dans la source de données de démonstration. Pour une source de données plus volumineuse, vous définiriez une valeur supérieure à 0.

Notez également l’instruction ```"dataToExtract":"contentAndMetadata"``` dans les paramètres de configuration. Cette instruction indique à l’indexeur d’extraire automatiquement le contenu de fichiers de différents formats, ainsi que les métadonnées associées à chaque fichier. 

Lorsque le contenu est extrait, vous pouvez définir ```imageAction``` pour extraire le texte des images trouvées dans la source de données. La configuration ```"imageAction":"generateNormalizedImages"```, associée à la compétence de reconnaissance optique des caractères et à la compétence de fusion de texte, indique à l’indexeur d’extraire le texte des images (par exemple, le mot « stop » d’un panneau de signalisation Stop) et de l’incorporer dans le champ de contenu. Ce comportement s’applique aux images intégrées dans les documents (pensez à une image dans un fichier PDF), ainsi qu’aux images trouvées dans la source de données, par exemple un fichier JPG.

## <a name="check-indexer-status"></a>Vérifier l’état de l’indexeur

Une fois l’indexeur défini, il s’exécute automatiquement lorsque vous envoyez la demande. Selon les compétences cognitives que vous avez définies, l’indexation peut prendre plus de temps que prévu. Pour déterminer si l’indexeur est toujours en cours d’exécution, envoyez la demande suivante pour vérifier l’état de l’indexeur.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

La réponse indique si l’indexeur est en cours d’exécution. Une fois l’indexation terminée, utilisez une autre demande HTTP GET au point de terminaison STATUS (comme ci-dessus) pour afficher les rapports des erreurs et des avertissements éventuels qui se sont produits pendant l’enrichissement.  

Les avertissements sont courants avec certaines combinaisons de fichiers sources et de compétences et n’indiquent pas toujours un problème. Dans ce tutoriel, les avertissements sont sans gravité (par exemple, aucune entrée de texte à partir des fichiers JPEG). Vous pouvez examiner la réponse d’état pour rechercher des informations détaillées sur les avertissements émis lors de l’indexation.
 
## <a name="query-your-index"></a>Interroger votre index

Une fois l’indexation terminée, exécutez des requêtes qui retournent le contenu de champs individuels. Par défaut, le service Recherche Azure retourne les 50 meilleurs résultats. Les données d’exemple sont petites si bien que la configuration par défaut fonctionne correctement. Toutefois, lorsque vous travaillez avec des jeux de données plus volumineux, vous pouvez être amené à inclure des paramètres dans la chaîne de requête pour retourner plus de résultats. Pour obtenir des instructions, consultez [Guide pratique pour présenter les résultats dans Recherche Azure](search-pagination-page-layout.md).

Comme étape de vérification, interrogez l’index au sujet de tous les champs.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

La sortie est le schéma d’index, avec le nom, le type et les attributs de chaque champ.

Envoyez une deuxième requête pour `"*"` afin de retourner tout le contenu d’un champ individuel, tel que `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Répétez ces étapes pour les champs supplémentaires : content, languageCode, keyPhrases et organizations dans cet exercice. Vous pouvez retourner plusieurs champs via `$select` à l’aide d’une liste délimitée par des virgules.

Vous pouvez utiliser GET ou POST, en fonction de la longueur et de la complexité de la chaîne de requête. Pour plus d’informations, consultez [Exécuter des requêtes à l’aide de l’API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).



<a name="reset"></a>

## <a name="reset-and-rerun"></a>Réinitialiser et réexécuter

Dans les premières étapes expérimentales de développement de pipeline, l’approche la plus pratique pour les itérations de conception consiste à supprimer les objets de Recherche Azure et à autoriser votre code à les reconstruire. Les noms des ressources sont uniques. La suppression d’un objet vous permet de le recréer en utilisant le même nom.

Pour réindexer vos documents avec les nouvelles définitions :

1. Supprimez l’index pour supprimer les données persistantes. Supprimez l’indexeur pour le recréer sur votre service.
2. Modifiez un ensemble de compétences et la définition d’index.
3. Recréez un index et un indexeur sur le service pour exécuter le pipeline. 

Vous pouvez utiliser le portail pour supprimer des index, des indexeurs et des ensembles de compétences.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Le code d’état 204 est retourné lorsque la suppression réussit.

Lorsque votre code arrive à maturité, vous pouvez affiner une stratégie de regénération. Pour plus d’informations, consultez [Guide pratique pour regénérer un index](search-howto-reindex.md).

## <a name="takeaways"></a>Éléments importants à retenir

Ce tutoriel présente les étapes de base pour générer un pipeline d’indexation enrichie via la création de composants : une source de données, un ensemble de compétences, un index et un indexeur.

Les [compétences prédéfinies](cognitive-search-predefined-skills.md) ont été présentées, ainsi que la définition d’un ensemble de compétences et les mécanismes de chaînage des compétences via les entrées et sorties. Vous avez également appris que `outputFieldMappings` est requis dans la définition de l’indexeur pour acheminer les valeurs enrichies du pipeline dans un index de recherche, sur un service de Recherche Azure.

Enfin, vous avez appris à tester les résultats et réinitialiser le système pour des itérations ultérieures. Vous avez appris qu’émettre des requêtes par rapport à l’index retourne la sortie créée par le pipeline d’indexation enrichie. Dans cette version, il existe un mécanisme permettant d’afficher les constructions internes (documents enrichis créés par le système). Vous avez également appris à vérifier l’état de l’indexeur et quels objets supprimer avant de réexécuter un pipeline.

## <a name="clean-up-resources"></a>Supprimer des ressources

Le moyen le plus rapide de nettoyer après un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche Azure et le service Blob Azure. En supposant que vous avez placé ces deux services dans le même groupe, supprimez maintenant le groupe de ressources pour supprimer définitivement tout ce qu’il contient, y compris les services et tout le contenu stocké que vous avez créé pour ce tutoriel. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble de chaque service.

## <a name="next-steps"></a>Étapes suivantes

Personnalisez ou étendez le pipeline avec des compétences personnalisées. La création d’une compétence personnalisée et son ajout dans un ensemble de compétences vous permet d’intégrer une analyse de texte ou d’image que vous écrivez vous-même. 

> [!div class="nextstepaction"]
> [Exemple : créer une compétence personnalisée](cognitive-search-create-custom-skill-example.md)
