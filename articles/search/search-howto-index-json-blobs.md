---
title: Indexer les objets blob JSON à partir de l’indexeur Azure Blob pour une recherche en texte intégral - Recherche Azure
description: Analyser les objets blob Azure JSON pour le contenu de texte à l’aide de l’indexeur d’objets blob Recherche Azure. Les indexeurs automatisent l’ingestion des données pour certaines sources de données, comme le Stockage Blob Azure.
ms.date: 05/02/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4f3fb624f5e6137c9edb0be97adc16d8c808ebd9
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523067"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Comment indexer des objets BLOB JSON à l’aide d’indexeur d’objets Blob Azure Search
Cet article vous montre comment configurer un objet blob Azure Search [indexeur](search-indexer-overview.md) pour extraire le contenu structuré à partir de documents JSON dans stockage Blob Azure et les rendre détectables dans Azure Search. Ce flux de travail crée un index Azure Search et la charge de texte existant extrait des objets BLOB JSON. 

Vous pouvez utiliser le [portail](#json-indexer-portal), l’[API REST](#json-indexer-rest) ou le [SDK .NET](#json-indexer-dotnet) pour indexer du contenu JSON. Communes à toutes les approches est que les documents JSON sont trouvent dans un conteneur d’objets blob dans un compte de stockage Azure. Pour obtenir des conseils sur l’envoi (push) de documents JSON depuis d’autres plateformes qu’Azure, consultez [Importation de données dans Recherche Azure](search-what-is-data-import.md).

Objets BLOB JSON dans stockage Blob Azure sont généralement un seul document JSON ou une collection d’entités JSON. Pour les collections de JSON, l’objet blob peut avoir un **tableau** d’éléments JSON bien formés. Objets BLOB peut également être composé de plusieurs entités JSON individuelles séparées par un saut de ligne. L’indexeur d’objets blob dans Azure Search peut analyser n’importe quel telle construction, en fonction de la façon dont vous définissez la **parsingMode** paramètre sur la demande.

JSON de tous les modes d’analyse (`json`, `jsonArray`, `jsonLines`) sont désormais disponibles. 

> [!NOTE]
> Suivez les recommandations de configuration d’indexeur de [l’indexation un-à-plusieurs](search-howto-index-one-to-many-blobs.md) pour générer plusieurs documents de recherche à partir d’un objet blob Azure.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Utiliser le portail 

La méthode la plus simple pour l’indexation de documents JSON consiste à utiliser un Assistant dans le [portail Azure](https://portal.azure.com/). En analysant les métadonnées dans le conteneur d’objets blob Azure, l’Assistant [**Importation de données**](search-import-data-portal.md) peut créer un index par défaut, mapper des champs sources aux champs d’index cibles et charger l’index en une seule opération. Selon la taille et la complexité de la source de données, vous pouvez obtenir un index de recherche en texte intégral opérationnel en quelques minutes.

Nous vous recommandons d’utiliser le même abonnement Azure pour Azure Search et de stockage Azure, de préférence dans la même région.

### <a name="1---prepare-source-data"></a>1 - Préparez les données sources

1. [Connectez-vous au portail Azure](https://portal.azure.com/).

1. [Créer un conteneur d’objets Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) pour accueillir vos données. Le niveau d’accès Public peut être défini à un de ses valeurs valides.

Vous aurez besoin du nom de compte de stockage, nom du conteneur et une clé d’accès pour récupérer vos données dans le **importer des données** Assistant.

### <a name="2---start-import-data-wizard"></a>2 - Démarrez l’Assistant Importation de données

Dans la page Vue d’ensemble de votre service Azure Search, vous pouvez [démarrer l’Assistant](search-import-data-portal.md) à partir de la barre de commandes, ou en cliquant sur **ajouter recherche Azure** dans le **service Blob** section de votre volet de navigation gauche de compte de stockage.

   ![Commande Importer des données dans le portail](./media/search-import-data-portal/import-data-cmd2.png "Démarrer l’Assistant Importation de données")

### <a name="3---set-the-data-source"></a>3 - Définissez la source de données

Dans la page **Source de données**, la source doit être **Stockage Blob Azure**, avec les spécifications suivantes :

+ **Données à extraire** doit être *Contenu et métadonnées*. Le choix de cette option permet à l’Assistant d’inférer un schéma d’index et de mapper les champs pour l’importation.
   
+ **Mode d’analyse** doit être défini sur *JSON*, *tableau JSON* ou *lignes JSON*. 

  *JSON* considère chaque objet blob comme un seul document de recherche, qui apparaît comme élément indépendant dans les résultats de la recherche. 

  *Tableau JSON* est pour les objets BLOB qui contiennent des données JSON bien formées - le JSON bien formé correspond à un tableau d’objets, ou a une propriété qui est un tableau d’objets et vous souhaitez que chaque élément d’être articulée sous la forme d’un autonome, indépendant de recherche dans le document. Si les objets blob sont complexes et que vous ne choisissez pas *Tableau JSON*, l’objet blob tout entier est ingéré sous la forme d’un seul document.

  *Lignes JSON* est pour les objets BLOB composé de plusieurs entités JSON séparées par une nouvelle ligne, où vous souhaitez que chaque entité être articulée sous la forme d’un document de recherche indépendant autonome. Si les objets BLOB est complexes, et vous ne choisissez pas *lignes JSON* l’analyse de mode, puis le blob entier est reçue sous la forme d’un document unique.
   
+ **Conteneur de stockage** doit spécifier votre compte de stockage et votre conteneur, ou une chaîne de connexion dont la résolution aboutit au conteneur. Vous pouvez obtenir des chaînes de connexion sur la page du portail Service Blob.

   ![Définition de la source de données des objets blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Passez la page « Ajouter la recherche cognitive » dans l’Assistant

L’ajout de compétences cognitives n’est pas nécessaire pour l’importation de documents JSON. Sauf si vous avez un besoin spécifique d’[inclure des API et des transformations Cognitive Services](cognitive-search-concept-intro.md) à votre pipeline d’indexation, vous devez ignorer cette étape.

Pour ignorer cette étape, accédez d’abord à la page suivante.

   ![Bouton de page suivante pour la recherche cognitive](media/search-get-started-portal/next-button-add-cog-search.png)

À partir de cette page vous pouvez passer directement à la personnalisation de l’index.

   ![Ignorer l’étape des compétences cognitives](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - Définissez les attributs de l’index

Dans la page **Index**, vous devez voir une liste de champs avec un type de données et une série de cases à cocher permettant de définir les attributs de l’index. L’Assistant peut générer une liste de champs en fonction de métadonnées et en échantillonnant les données source. 

Vous pouvez sélectionner les attributs en cliquant sur la case à cocher en haut de la colonne d’attribut. Choisissez **récupérable** et **possibilité de recherche** pour chaque champ qui doit être retourné à une application cliente et de traitement de recherche de texte intégral. Vous remarquerez que les entiers ne sont pas recherche en texte intégral ou floues interrogeables (numéros sont évaluées textuellement et sont souvent utiles dans les filtres).

Passez en revue la description de [attributs d’index](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) et [analyseurs de langage](https://docs.microsoft.com/rest/api/searchservice/language-support) pour plus d’informations. 

Prenez un moment pour passer en revue vos sélections. Une fois que vous exécutez l’Assistant, des structures de données physiques sont créées : vous ne pourrez donc plus modifier ces champs sans supprimer et recréer tous les objets.

   ![Définition de l’index d’objets blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Créez un indexeur

Une fois que tout est spécifié, l’Assistant crée trois objets distincts dans votre service de recherche. Un objet source de données et un objet index sont enregistrés comme ressources nommées dans votre service Recherche Azure. La dernière étape crée un objet indexeur. Le fait de nommer l’indexeur lui permet d’exister comme ressource autonome, que vous pouvez planifier et gérer indépendamment de l’objet index et de l’objet source de données, créés dans la même séquence de l’Assistant.

Si vous n’êtes pas familiarisé avec les indexeurs, en voici une définition : un *indexeur* est une ressource dans Recherche Azure qui analyse une source de données externe et son contenu avec possibilité de recherche. La sortie de l’Assistant **Importation de données** est un indexeur qui analyse votre source de données JSON, extrait le contenu avec possibilité de recherche et l’importe dans un index sur Recherche Azure.

   ![Définition de l’indexeur d’objets blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Cliquez sur **OK** pour exécuter l’Assistant et créer tous les objets. L’indexation commence immédiatement.

Vous pouvez surveiller l’importation des données dans les pages du portail. Des notifications de l’avancement indiquent l’état de l’indexation et le nombre de documents chargés. 

Quand l’indexation est terminée, vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) pour interroger votre index.

> [!NOTE]
> Si vous ne voyez pas les données que vous attendez, vous devrez peut-être définir d’autres attributs sur plusieurs champs. Supprimer l’index et l’indexeur que vous venez de créer et exécutez l’Assistant à nouveau, modifier vos sélections pour les attributs d’index à l’étape 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utiliser les API REST

Vous pouvez utiliser l’API REST pour indexer les objets BLOB JSON, suivant un flux de travail en trois parties commun à tous les indexeurs dans recherche Azure : créer une source de données, de créer un index, de créer un indexeur. Extraction de données à partir du stockage d’objets blob se produit lorsque vous envoyez la demande de créer un indexeur. Une fois cette demande est terminée, vous aurez un index peut être interrogé. 

Vous pouvez consulter [exemple de code REST](#rest-example) à la fin de cette section qui montre comment créer tous les objets de trois. Cette section contient également des détails sur [modes d’analyse de JSON](#parsing-modes), [unique des objets BLOB](#parsing-single-blobs), [tableaux JSON](#parsing-arrays), et [imbriqués tableaux](#nested-json-arrays).

Pour en code JSON l’indexation, utilisez [Postman](search-fiddler.md) et l’API REST pour créer ces objets :

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Ordre des opérations nécessite que vous créez et que vous appelez des objets dans cet ordre. Par opposition avec le flux de travail de portail, une approche de code nécessite un index disponible pour accepter les documents JSON envoyées via la **créer un indexeur** demande.

Objets BLOB JSON dans stockage Blob Azure sont généralement un seul document JSON ou JSON « tableau ». L’indexeur d’objets blob dans Recherche Azure peut analyser l’une ou l’autre de ces constructions selon la définition du paramètre **parsingMode** sur la requête.

| Document JSON | parsingMode | Description  | Disponibilité |
|--------------|-------------|--------------|--------------|
| Un seul par objet blob | `json` | Analyse les objets blob JSON comme un bloc de texte unique. Chaque objet blob JSON devient un document Recherche Azure unique. | La disposition générale dans les deux [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API et [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Plusieurs par objet blob | `jsonArray` | Analyse un tableau JSON dans l’objet blob, où chaque élément du tableau devient un document Recherche Azure distinct.  | La disposition générale dans les deux [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API et [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Plusieurs par objet blob | `jsonLines` | Analyse un objet blob qui contient plusieurs entités JSON (« tableau ») séparées par un saut de ligne, où chaque entité devient un document recherche Azure distinct. | La disposition générale dans les deux [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API et [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - assembler des entrées pour la demande

Pour chaque demande, vous devez fournir le nom du service et la clé d’administration pour Azure Search (dans l’en-tête POST) et le nom de compte de stockage et la clé pour le stockage blob. Vous pouvez utiliser [Postman](search-fiddler.md) pour envoyer des requêtes HTTP à recherche Azure.

Copiez les quatre valeurs suivantes dans le bloc-notes afin que vous pouvez les coller dans une demande :

+ Nom du service recherche Azure
+ Clé d’administration de recherche Azure
+ Nom du compte de stockage Azure
+ Clé de compte de stockage Azure

Vous pouvez trouver ces valeurs dans le portail :

1. Dans les pages du portail pour la recherche Azure, copiez l’URL de service de recherche à partir de la page de présentation.

2. Dans le volet de navigation gauche, cliquez sur **clés** puis copiez la clé primaire ou secondaire (ils sont équivalents).

3. Basculer vers les pages du portail pour votre compte de stockage. Dans le volet de navigation de gauche, sous **paramètres**, cliquez sur **clés d’accès**. Cette page fournit le nom du compte et la clé. Copiez le nom de compte de stockage et d’une des clés dans le bloc-notes.

### <a name="2---create-a-data-source"></a>2 - créer une source de données

Cette étape fournit les informations de connexion de source de données utilisées par l’indexeur. La source de données est un objet nommé dans Azure Search qui rend persistantes les informations de connexion. La source de données, `azureblob`, détermine les comportements d’extraction de données sont appelées par l’indexeur. 

Remplacez les valeurs valides pour le nom du service, clé d’administration, compte de stockage et des espaces réservés de clé de compte.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - créer un index de recherche cible 

Les indexeurs sont couplés à un schéma d’index. Si vous utilisez l’API (à la place du portail), préparez un index à l’avance pour pouvoir le spécifier sur l’opération de l’indexeur.

L’index stocke le contenu avec possibilité de recherche dans Recherche Azure. Pour créer un index, fournissez un schéma qui spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche. Si vous créez un index qui a les mêmes noms de champs et les mêmes types de données que la source, l’indexeur met en correspondance les champs sources et de destination, ce qui vous évite de devoir mapper explicitement les champs.

L’exemple suivant montre une demande [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index). L’index aura un champ `content` avec possibilité de recherche pour stocker le texte extrait d’objets blob :   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - configurer et exécuter l’indexeur

Comme avec un index et une données source et indexeur est également portant un nom de l’objet que vous créez et réutilisez sur un service Azure Search. Une requête complète pour créer un indexeur peut se présenter comme suit :

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Configuration de l’indexeur est dans le corps de la demande. Elle nécessite une source de données et un index cible vide qui existe déjà dans Azure Search. 

Planification et des paramètres sont facultatifs. Si vous les omettez, l’indexeur s’exécute immédiatement, à l’aide de `json` en tant que le mode d’analyse.

Cet indexeur particulier n’inclut pas les mappages de champs. Dans la définition de l’indexeur, vous pouvez laisser **mappages de champs** si les propriétés du document JSON source correspondent aux champs de votre index de recherche cible. 


### <a name="rest-example"></a>Exemple REST

Cette section est un récapitulatif de toutes les requêtes utilisées pour la création d’objets. Pour une présentation des composants, consultez les sections précédentes de cet article.

### <a name="data-source-request"></a>Requête de source de données

Tous les indexeurs nécessitent un objet de source de données qui fournit des informations de connexion aux données existantes. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Demande de l’index

Tous les indexeurs nécessitent un index cible qui reçoit les données. Le corps de la demande définit le schéma d’index, composée de champs, attribués pour prendre en charge les comportements souhaités dans un index de recherche. Cet index doit être vide lorsque vous exécutez l’indexeur. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Requête de l’indexeur

Cette requête montre un indexeur complètement spécifié. Il inclut des mappages de champs qui ont été omis dans les exemples précédents. Rappelez-vous que « schedule », « paramètres », et « fieldMappings » sont facultatifs tant qu’il existe une valeur par défaut disponible. L’omission de « planifier » provoque l’indexeur pour une exécution immédiate. L’omission de « parsingMode » provoque l’index à utiliser la valeur par défaut « json ».

Création de l’indexeur recherche Azure déclenche l’importation des données. Il s’exécute immédiatement et ensuite selon une planification si vous avez fourni un.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

Le kit SDK .NET offre une parité complète avec l’API REST. Nous vous recommandons de consulter la section précédente de l’API REST pour découvrir les concepts, les workflows et les exigences. Vous pouvez alors vous référer à la documentation de référence des API .NET suivante pour implémenter un indexeur JSON dans du code managé.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modes d’analyse

Objets BLOB JSON peuvent supposer que plusieurs formulaires. Le **parsingMode** paramètre sur l’indexeur JSON détermine l’analyse de contenu d’objet blob JSON et structuré dans un index Azure Search :

| parsingMode | Description  |
|-------------|-------------|
| `json`  | Indexer chaque objet blob en tant qu’un seul document. Il s’agit de la valeur par défaut. |
| `jsonArray` | Choisissez ce mode si vos objets BLOB sont constitués de tableaux JSON, et que vous avez besoin de chaque élément du tableau pour devenir un document distinct dans Azure Search. |
|`jsonLines` | Choisissez ce mode si vos objets BLOB sont constitués de plusieurs entités JSON, qui sont séparées par une nouvelle ligne, et que vous avez besoin de chaque entité à devenir un document distinct dans Azure Search. |

Vous pouvez considérer un document comme un élément individuel dans les résultats de la recherche. Si vous souhaitez que chaque élément du tableau s’affichent dans les résultats de la recherche comme un élément indépendant, puis utiliser le `jsonArray` ou `jsonLines` option appropriée.

Dans la définition de l’indexeur, vous pouvez utiliser des [mappages de champs](search-indexer-field-mappings.md) pour choisir les propriétés du document JSON source à utiliser pour remplir votre index de recherche cible. Pour `jsonArray` mode, d’analyse si le tableau existe sous la forme d’une propriété de niveau inférieur, vous pouvez définir une racine de document qui indique où le tableau est placé dans l’objet blob.

> [!IMPORTANT]
> Lorsque vous utilisez `json`, `jsonArray` ou `jsonLines` mode d’analyse, recherche Azure suppose que tous les objets BLOB dans votre source de données contiennent JSON. Si vous devez prendre en charge une combinaison d’objets blob JSON et autres dans la même source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analyser les objets BLOB JSON uniques

Par défaut, [l’indexeur d’objets blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyse les objets blob JSON comme un bloc de texte unique. Vous souhaitez généralement conserver la structure de vos documents JSON. En guise d’exemple, prenons le document JSON suivant dans Stockage Blob Azure :

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

L’indexeur d’objets blob analyse le document JSON en un seul document Recherche Azure. L’indexeur charge un index en mettant en correspondance les champs « text », « datePublished » et « tags » de la source avec les champs cibles de même nom et de même type.

Comme indiqué, les mappages de champs ne sont pas nécessaires. Étant donné un index avec les champs « text », « datePublished » et « tags », l’indexeur d’objets blob peut déduire le mappage correct sans qu’un mappage de champs ne soit présent dans la requête.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analyser des tableaux JSON

Vous pouvez également utiliser l’option de tableau JSON. Cette option est utile lorsque les objets BLOB contiennent un *tableau d’objets JSON bien formés*, et vous souhaitez que chaque élément devienne un document recherche Azure distinct. Prenons par exemple l’objet blob JSON suivant. Vous pouvez remplir l’index Recherche Azure avec trois documents distincts contenant chacun les champs « id » et « text ».  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Pour un tableau JSON, la définition de l’indexeur doit être similaire à l’exemple suivant. Notez que le paramètre parsingMode spécifie l’analyseur `jsonArray`. Spécification de l’Analyseur de droite et avoir les bonnes données entrée sont uniquement deux exigences spécifiques de tableau pour l’indexation d’objets BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
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

## <a name="parse-nested-arrays"></a>Analyser les tableaux imbriqués
Pour les tableaux JSON ayant des éléments imbriqués, vous pouvez spécifier un `documentRoot` pour indiquer une structure à plusieurs niveaux. Par exemple, si vos objets blob ressemblent à ceci :

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

## <a name="parse-blobs-separated-by-newlines"></a>Analyser les objets BLOB séparés par des sauts de ligne

Si votre objet blob contient plusieurs entités JSON séparées par un saut de ligne et que vous souhaitez que chaque élément devienne un document recherche Azure distinct, vous pouvez opter pour l’option de lignes JSON. Par exemple, étant donné l’objet blob suivant (où il existe trois différentes entités JSON), vous pouvez remplir votre index Azure Search avec trois documents distincts, chacun avec des champs « id » et « text ».

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Pour les lignes JSON, la définition de l’indexeur doit ressembler à l’exemple suivant. Notez que le paramètre parsingMode spécifie l’analyseur `jsonLines`. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Là encore, notez que les mappages de champs peuvent être omis, similaire à la `jsonArray` mode d’analyse.

## <a name="add-field-mappings"></a>Ajouter des mappages de champs

Quand les champs sources et cibles ne sont pas parfaitement alignés, vous pouvez définir une section de mappage de champs dans le corps de la requête pour des associations champ à champ explicites.

Actuellement, Azure Search ne peut pas indexer des documents JSON arbitraires directement, car il prend en charge uniquement les types de données primitifs, les tableaux de chaînes et les points GeoJSON. Toutefois, vous pouvez utiliser les **mappages de champ** pour sélectionner des parties de votre document JSON et les intégrer dans les champs de niveau supérieur du document de recherche. Pour en savoir plus sur les principes de base des mappages de champs, consultez [Mappages de champs dans les indexeurs Recherche Azure](search-indexer-field-mappings.md).

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

## <a name="see-also"></a>Voir aussi

+ [Indexeurs dans Recherche Azure](search-indexer-overview.md)
+ [Indexation de Stockage Blob Azure avec Recherche Azure](search-howto-index-json-blobs.md)
+ [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
+ [Tutoriel : Rechercher des données semi-structurées à partir de Stockage Blob Azure](search-semi-structured-data.md)
