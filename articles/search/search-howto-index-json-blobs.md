---
title: Indexer les objets blob JSON à partir de l’indexeur Azure Blob pour une recherche en texte intégral - Recherche Azure
description: Analyser les objets blob Azure JSON pour le contenu de texte à l’aide de l’indexeur d’objets blob Recherche Azure. Les indexeurs automatisent l’ingestion des données pour certaines sources de données, comme le Stockage Blob Azure.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: cafb48f28e38794ce0757d50a5d87432b237e17c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467160"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexation d’objets blob JSON avec un indexeur d’objets blob Recherche Azure
Cet article explique comment configurer un indexeur d’objets blob Recherche Azure pour extraire le contenu structuré d’objets blob JSON dans Stockage Blob Azure.

Vous pouvez utiliser le [portail](#json-indexer-portal), l’[API REST](#json-indexer-rest) ou le [SDK .NET](#json-indexer-dotnet) pour indexer du contenu JSON. Dans toutes les approches, les documents JSON sont généralement situés dans un conteneur d’objets blob, dans un compte de stockage Azure. Pour obtenir des conseils sur l’envoi (push) de documents JSON depuis d’autres plateformes qu’Azure, consultez [Importation de données dans Recherche Azure](search-what-is-data-import.md).

Les objets blob JSON dans Stockage Blob Azure se composent généralement d’un seul document JSON ou d’un tableau JSON. L’indexeur d’objets blob dans Recherche Azure peut analyser l’une ou l’autre de ces constructions selon la définition du paramètre **parsingMode** sur la requête.

> [!IMPORTANT]
> L'indexation d'objets blob JSON est généralement disponible, mais l'analyse JsonArray n'est accessible qu'en préversion publique et ne doit pas être utilisée dans des environnements de production. Pour plus d’informations, consultez [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md). 

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Utiliser le portail 

La méthode la plus simple pour l’indexation de documents JSON consiste à utiliser un Assistant dans le [portail Azure](https://portal.azure.com/). En analysant les métadonnées dans le conteneur d’objets blob Azure, l’Assistant [**Importation de données**](search-import-data-portal.md) peut créer un index par défaut, mapper des champs sources aux champs d’index cibles et charger l’index en une seule opération. Selon la taille et la complexité de la source de données, vous pouvez obtenir un index de recherche en texte intégral opérationnel en quelques minutes.

### <a name="1---prepare-source-data"></a>1 - Préparez les données sources

Vous devez avoir un compte de stockage Azure avec Stockage Blob et un conteneur de documents JSON. Si vous n’êtes pas familiarisé avec une de ces tâches, passez en revue les conditions préalables de « Configurer le service Blob Azure et charger les données d’exemple » dans [Recherche cognitive - Démarrage rapide](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 - Démarrez l’Assistant Importation de données

Vous pouvez [démarrer l’Assistant](search-import-data-portal.md) à partir de la barre de commandes dans la page du service Recherche Azure, ou en cliquant sur **Ajouter Recherche Azure** dans la section **Service Blob** du volet de navigation gauche de votre compte de stockage.

### <a name="3---set-the-data-source"></a>3 - Définissez la source de données

Dans la page **Source de données**, la source doit être **Stockage Blob Azure**, avec les spécifications suivantes :

+ **Données à extraire** doit être *Contenu et métadonnées*. Le choix de cette option permet à l’Assistant d’inférer un schéma d’index et de mapper les champs pour l’importation.
   
+ **Mode d’analyse** doit être défini sur *JSON* ou sur *Tableau JSON*. 

  *JSON* considère chaque objet blob comme un seul document de recherche, qui apparaît comme élément indépendant dans les résultats de la recherche. 

  *Tableau JSON* concerne les objets blob composés de plusieurs éléments, où vous voulez que chaque élément soit considéré comme un document de recherche autonome et indépendant. Si les objets blob sont complexes et que vous ne choisissez pas *Tableau JSON*, l’objet blob tout entier est ingéré sous la forme d’un seul document.
   
+ **Conteneur de stockage** doit spécifier votre compte de stockage et votre conteneur, ou une chaîne de connexion dont la résolution aboutit au conteneur. Vous pouvez obtenir des chaînes de connexion sur la page du portail Service Blob.

   ![Définition de la source de données des objets blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Passez la page « Ajouter la recherche cognitive » dans l’Assistant

L’ajout de compétences cognitives n’est pas nécessaire pour l’importation de documents JSON. Sauf si vous avez un besoin spécifique d’[inclure des API et des transformations Cognitive Services](cognitive-search-concept-intro.md) à votre pipeline d’indexation, vous devez ignorer cette étape.

Pour ignorer l’étape, cliquez sur la page suivante **Personnaliser l’index cible**.

### <a name="5---set-index-attributes"></a>5 - Définissez les attributs de l’index

Dans la page **Index**, vous devez voir une liste de champs avec un type de données et une série de cases à cocher permettant de définir les attributs de l’index. L’Assistant peut générer un index par défaut basé sur les métadonnées et en échantillonnant les données sources. 

Les valeurs par défaut produisent souvent une solution utilisable, en sélectionnant un champ (casté en chaîne) pour servir de clé ou d’ID de document pour identifier de façon univoque chaque document, ainsi que des champs qui sont de bons candidats pour la recherche en texte intégral et récupérables dans un jeu de résultats. Pour les objets blob, le champ `content` est le meilleur candidat pour du contenu avec possibilité de recherche.

Vous pouvez accepter les valeurs par défaut, ou passer en revue la description des [attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) et les [analyseurs linguistiques](https://docs.microsoft.com/rest/api/searchservice/language-support) pour remplacer ou compléter les valeurs initiales. 

Prenez un moment pour passer en revue vos sélections. Une fois que vous exécutez l’Assistant, des structures de données physiques sont créées : vous ne pourrez donc plus modifier ces champs sans supprimer et recréer tous les objets.

   ![Définition de l’index d’objets blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Créez un indexeur

Une fois que tout est spécifié, l’Assistant crée trois objets distincts dans votre service de recherche. Un objet source de données et un objet index sont enregistrés comme ressources nommées dans votre service Recherche Azure. La dernière étape crée un objet indexeur. Le fait de nommer l’indexeur lui permet d’exister comme ressource autonome, que vous pouvez planifier et gérer indépendamment de l’objet index et de l’objet source de données, créés dans la même séquence de l’Assistant.

Si vous n’êtes pas familiarisé avec les indexeurs, en voici une définition : un *indexeur* est une ressource dans Recherche Azure qui analyse une source de données externe et son contenu avec possibilité de recherche. La sortie de l’Assistant **Importation de données** est un indexeur qui analyse votre source de données JSON, extrait le contenu avec possibilité de recherche et l’importe dans un index sur Recherche Azure.

   ![Définition de l’indexeur d’objets blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Cliquez sur **OK** pour exécuter l’Assistant et créer tous les objets. L’indexation commence immédiatement.

Vous pouvez surveiller l’importation des données dans les pages du portail. Des notifications de l’avancement indiquent l’état de l’indexation et le nombre de documents chargés. Quand l’indexation est terminée, vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) pour interroger votre index.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utiliser les API REST

L’indexation d’objets blob JSON est similaire à l’extraction de documents standard dans un workflow en trois parties commun à tous les indexeurs dans Recherche Azure : créer une source de données, créer un index, créer un indexeur.

Pour une indexation JSON effectuée avec du code, vous pouvez utiliser l’API REST avec des API pour les [indexeurs](https://docs.microsoft.com/rest/api/searchservice/create-indexer), les [sources de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) et les [index](https://docs.microsoft.com/rest/api/searchservice/create-index). Contrairement à l’Assistant du portail, une approche par le code nécessite d’avoir un index en place, prêt à accepter les documents JSON quand vous envoyez la demande **Créer un indexeur**.

Les objets blob JSON dans Stockage Blob Azure se composent généralement d’un seul document JSON ou d’un tableau JSON. L’indexeur d’objets blob dans Recherche Azure peut analyser l’une ou l’autre de ces constructions selon la définition du paramètre **parsingMode** sur la requête.

| Document JSON | parsingMode | Description | Disponibilité |
|--------------|-------------|--------------|--------------|
| Un seul par objet blob | `json` | Analyse les objets blob JSON comme un bloc de texte unique. Chaque objet blob JSON devient un document Recherche Azure unique. | Généralement disponible dans les API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) et [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |
| Plusieurs par objet blob | `jsonArray` | Analyse un tableau JSON dans l’objet blob, où chaque élément du tableau devient un document Recherche Azure distinct.  | Généralement disponible dans les API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) et [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |


### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

La première étape consiste à fournir les informations de connexion à la source de données utilisées par l’indexeur. Le type de source de données, spécifié ici sous la forme `azureblob`, détermine les comportements d’extraction de données appelés par l’indexeur. Pour l’indexation d’objets blob JSON, la définition de la source de données est identique pour les documents et les tableaux JSON. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Étape 2 : Créer un index de recherche cible 

Les indexeurs sont couplés à un schéma d’index. Si vous utilisez l’API (à la place du portail), préparez un index à l’avance pour pouvoir le spécifier sur l’opération de l’indexeur.

L’index stocke le contenu avec possibilité de recherche dans Recherche Azure. Pour créer un index, fournissez un schéma qui spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche. Si vous créez un index qui a les mêmes noms de champs et les mêmes types de données que la source, l’indexeur met en correspondance les champs sources et de destination, ce qui vous évite de devoir mapper explicitement les champs.

L’exemple suivant montre une demande [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index). L’index aura un champ `content` avec possibilité de recherche pour stocker le texte extrait d’objets blob :   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>Étape 3 : Configurer et exécuter l’indexeur

Jusqu’à présent, la source de données et l’index ont été définis indépendamment du paramètre parsingMode. Toutefois, dans cette étape de configuration de l’indexeur, le processus varie selon la façon dont vous souhaitez analyser et structurer le contenu des objets blob JSON dans un index Recherche Azure. Les choix sont `json` ou `jsonArray` :

+ Définissez **parsingMode** sur `json` pour indexer chaque objet blob comme un seul document.

+ Définissez **parsingMode** sur `jsonArray` si vos objets blobs sont constitués de tableaux JSON et s’il faut que chaque élément du tableau devienne un document distinct dans Recherche Azure. Vous pouvez considérer un document comme un élément individuel dans les résultats de la recherche. Si vous voulez que chaque élément du tableau apparaisse dans les résultats de la recherche comme élément indépendant, utilisez l’option `jsonArray`.

Dans la définition de l’indexeur, vous pouvez utiliser des **mappages de champs** pour choisir les propriétés du document JSON source à utiliser pour remplir votre index de recherche cible. Pour les tableaux JSON, si le tableau existe en tant que propriété de plus bas niveau, vous pouvez définir une racine de document qui indique l’emplacement du tableau dans l’objet blob.

> [!IMPORTANT]
> Lorsque vous utilisez le mode d’analyse `json` ou `jsonArray`, Recherche Azure suppose que tous les objets blob dans votre source de données contiennent JSON. Si vous devez prendre en charge une combinaison d’objets blob JSON et autres dans la même source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Comment analyser des objets blob JSON uniques

Par défaut, [l’indexeur d’objets blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyse les objets blob JSON comme un bloc de texte unique. Vous souhaitez généralement conserver la structure de vos documents JSON. En guise d’exemple, prenons le document JSON suivant dans Stockage Blob Azure :

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

L’indexeur d’objets blob analyse le document JSON en un seul document Recherche Azure. L’indexeur charge un index en mettant en correspondance les champs « text », « datePublished » et « tags » de la source avec les champs cibles de même nom et de même type.

La configuration est fournie dans le corps d’une opération de l’indexeur. Rappelez-vous que l’objet source de données, défini précédemment, spécifie le type de source de donnée et les informations de connexion. En outre, l’index cible doit également exister comme conteneur vide dans votre service. La planification et les paramètres sont facultatifs. Toutefois, si vous les omettez, l’indexeur s’exécute immédiatement en mode d’analyse `json`.

Une requête complète peut se présenter comme suit :

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Comme indiqué, les mappages de champs ne sont pas nécessaires. Étant donné un index avec les champs « text », « datePublished » et « tags », l’indexeur d’objets blob peut déduire le mappage correct sans qu’un mappage de champs ne soit présent dans la requête.

### <a name="how-to-parse-json-arrays"></a>Comment analyser les tableaux JSON

Vous pouvez également opter pour la fonctionnalité des tableaux JSON. Cette fonctionnalité est utile quand les objets blob contiennent un *tableau d’objets JSON* et que vous souhaitez que chaque élément devienne un document Recherche Azure distinct. Prenons par exemple l’objet blob JSON suivant. Vous pouvez remplir l’index Recherche Azure avec trois documents distincts contenant chacun les champs « id » et « text ».  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Pour un tableau JSON, la définition de l’indexeur doit être similaire à l’exemple suivant. Notez que le paramètre parsingMode spécifie l’analyseur `jsonArray`. Spécifier l’analyseur correct et avoir les bonnes entrées de données sont les deux seules conditions spécifiques aux tableaux pour l’indexation d’objets blob JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Là encore, notez que les mappages des champs peuvent être omis. En supposant un index avec des champs nommés de façon identique « id » et « text », l’indexeur d’objets blob peut inférer le mappage correct sans une liste de mappages des champs explicites.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Tableaux JSON imbriqués
Que se passe-t-il si vous souhaitez indexer un tableau d’objets JSON, mais que ce tableau est imbriqué quelque part dans le document ? Vous pouvez choisir la propriété qui contient le tableau à l’aide de la propriété de configuration `documentRoot` . Par exemple, si vos objets blob ressemblent à ceci :

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Utilisez cette configuration pour indexer le tableau contenu dans la propriété `level2` :

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="using-field-mappings-to-build-search-documents"></a>Utilisation des mappages de champ pour créer des documents de recherche

Quand les champs sources et cibles ne sont pas parfaitement alignés, vous pouvez définir une section de mappage de champs dans le corps de la requête pour des associations champ à champ explicites.

Actuellement, Azure Search ne peut pas indexer des documents JSON arbitraires directement, car il ne prend en charge que les types de données primitifs, les tableaux de chaînes et les points GeoJSON. Toutefois, vous pouvez utiliser les **mappages de champ** pour sélectionner des parties de votre document JSON et les intégrer dans les champs de niveau supérieur du document de recherche. Pour en savoir plus sur les principes de base des mappages de champs, consultez [Mappages de champs dans les indexeurs Recherche Azure](search-indexer-field-mappings.md).

Revenons à notre exemple de document JSON :

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Prenons un index de recherche avec les champs suivants : `text` de type `Edm.String`, `date` de type `Edm.DateTimeOffset` et `tags` de type `Collection(Edm.String)`. Notez la différence entre « datePublished » dans la source et le champ `date` dans l’index. Pour mapper votre document JSON à la forme souhaitée, utilisez les mappages de champ suivants :

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Les noms de champ source dans les mappages sont spécifiés selon la notation de [pointeur JSON](https://tools.ietf.org/html/rfc6901) . Vous débutez par une barre oblique pour faire référence à la racine de votre document JSON, puis sélectionnez la propriété souhaitée (au niveau arbitraire de l’imbrication) en utilisant un chemin d’accès séparé par des barres obliques avant.

Vous pouvez également faire référence à des éléments de tableau en utilisant un index de base zéro. Par exemple, pour sélectionner le premier élément du tableau « tags » dans l’exemple ci-dessus, utilisez un mappage de champ similaire au suivant :

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Si un nom de champ source dans un chemin de mappage de champ fait référence à une propriété qui n’existe pas dans JSON, ce mappage est ignoré sans erreur. Cela nous permet de prendre en charge les documents avec un schéma différent (cas fréquent). Comme il n’y a aucune validation, vous devez veiller à éviter les fautes de frappe dans la spécification du mappage de champ.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>Exemple REST : Requête d’indexeur avec mappages de champs

L’exemple suivant est une charge utile d’indexeur complète, avec notamment des mappages de champs :

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Utilisation du Kit de développement logiciel (SDK) .NET

Le SDK .NET est entièrement pareil que l’API REST. Nous vous recommandons de consulter la section précédente de l’API REST pour découvrir les concepts, les workflows et les exigences. Vous pouvez alors vous référer à la documentation de référence des API .NET suivante pour implémenter un indexeur JSON dans du code managé.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Voir aussi

+ [Indexeurs dans Recherche Azure](search-indexer-overview.md)
+ [Indexation de Stockage Blob Azure avec Recherche Azure](search-howto-index-json-blobs.md)
+ [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
+ [Tutoriel : Rechercher des données semi-structurées à partir de Stockage Blob Azure](search-semi-structured-data.md)
