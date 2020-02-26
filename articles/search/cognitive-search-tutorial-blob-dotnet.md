---
title: 'Tutoriel : Créer un ensemble de compétences en C# en utilisant .NET'
titleSuffix: Azure Cognitive Search
description: Parcourez un exemple de code montrant une extraction de données, un langage naturel et un traitement d’images par IA dans le pipeline d’indexation d’enrichissement de la Recherche cognitive Azure.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efd4a9333b5fb02c18b2f6a6d0f8ce58bfb8f220
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472381"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-c-and-the-net-sdk"></a>Tutoriel : Créer un pipeline d’enrichissement par IA en utilisant C# et le SDK .NET

Dans ce tutoriel, vous découvrez les mécanismes de programmation de l’enrichissement des données dans la Recherche cognitive Azure avec des *compétences cognitives*. Les compétences sont secondées par des fonctions d’analyse des images et de traitement en langage naturel (NLP) dans Cognitive Services. Par le biais de la configuration et de la composition de compétences, vous pouvez extraire du texte et des représentations sous forme de texte d’un fichier image ou document analysé. Vous pouvez également détecter la langue, les entités, les expressions clés et bien plus encore. Le résultat final est un contenu supplémentaire riche dans un index de recherche, créé par un pipeline d’indexation par intelligence artificielle.

Dans ce tutoriel, vous utilisez le kit de développement logiciel (SDK) .NET pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un pipeline d’indexation qui enrichit les exemples de données destinés à un index
> * Appliquer des compétences intégrés : reconnaissance optique de caractères, fusion de texte, détection de langue, fractionnement de texte, reconnaissance d’entité, extraction de phrases clés
> * Découvrir comment chaîner des compétences en mappant les entrées aux sorties dans un ensemble de compétences
> * Exécuter des demandes et passer en revue les résultats
> * Réinitialiser l’index et les indexeurs pour un développement ultérieur

La sortie obtenue est un index de recherche en texte intégral sur Recherche cognitive Azure. Vous pouvez améliorer cet index avec d’autres fonctionnalités standard, telles que des [synonymes](search-synonyms.md), des [profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), des [analyseurs](search-analyzers.md) et des [filtres](search-filters.md).

Ce tutoriel s’exécute sur le service gratuit. Toutefois, le nombre de transactions gratuites est limité à 20 documents par jour. Si vous souhaitez suivre ce tutoriel plusieurs fois le même jour, supprimez l’indexeur pour réinitialiser le compteur.

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant plusieurs algorithmes d’IA, vous devez attacher une ressource Cognitive Services facturable. Des frais s’appliquent durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images dans le cadre de la phase de craquage de document de la Recherche cognitive Azure. L’extraction de texte à partir des documents est gratuite.
>
> L’exécution de qualifications intégrées est facturée selon le [paiement à l’utilisation existant de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l’extraction d’images sont présentés sur la [page de tarification du service Recherche cognitive Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Voici les services, outils et données utilisés dans ce tutoriel. 

+ [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données. Vérifiez que le compte de stockage se trouve dans la même région que Recherche cognitive Azure.

+ Les [exemples de données](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) se composent d’un petit ensemble de fichiers de types différents. 

+ [Installez Visual Studio](https://visualstudio.microsoft.com/) à utiliser comme IDE.

+ [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Pour interagir avec votre service Recherche cognitive Azure, vous devrez disposer de l’URL du service et d’une clé d’accès. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

   ![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-postman/get-url-key.png "Obtenir un point de terminaison et une clé d’accès HTTP")

L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="prepare-sample-data"></a>Préparer l’exemple de données

Le pipeline d’enrichissement extrait des données des sources de données Azure. Les données sources doivent provenir d’un type de source de données pris en charge pour un [indexeur de Recherche cognitive Azure](search-indexer-overview.md). Pour cet exercice, nous utilisons le stockage d’objets blob pour présenter plusieurs types de contenu.

1. [Connectez-vous au portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) pour contenir des exemples de données. Vous pouvez définir le niveau d’accès public sur l’une de ses valeurs valides. Ce tutoriel part du principe que vous le nom du conteneur est « basic-demo-data-pr ».

1. Une fois le conteneur créé, ouvrez-le et sélectionnez **Charger** dans la barre de commandes pour charger les [exemples de données](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Fichiers source sur le Stockage Blob Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Une fois que les fichiers d’exemple sont chargés, obtenez le nom du conteneur et une chaîne de connexion pour votre stockage d’objets Blob. Pour ce faire, accédez à votre compte de stockage dans le portail Azure, sélectionnez **Clés d’accès**, puis copiez le champ **Chaîne de connexion**.

   La chaîne de connexion doit être une URL similaire à l’exemple suivant :

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Il existe d’autres manières de spécifier la chaîne de connexion, par exemple en fournissant une signature d’accès partagé. Pour en savoir plus sur les informations d’identification de source de données, consultez [Indexation du stockage d’objets blob Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Configurer votre environnement

Commencez par ouvrir Visual Studio et créer un nouveau projet d’application console pouvant s’exécuter sur .NET Core.

### <a name="install-nuget-packages"></a>Installer les packages NuGet

Le [SDK .NET de la Recherche cognitive Azure](https://aka.ms/search-sdk) se compose de quelques bibliothèques clientes qui vous permettent de gérer vos index, sources de données, indexeurs et compétences, ainsi que de charger et gérer des documents et d’exécuter des requêtes, sans avoir à gérer les détails de HTTP et de JSON. Ces bibliothèques clientes sont distribuées sous la forme de packages NuGet.

Pour ce projet, vous devez installer la version 9 du package NuGet `Microsoft.Azure.Search`, ainsi que le dernier package NuGet `Microsoft.Extensions.Configuration.Json`.

Installez le package NuGet `Microsoft.Azure.Search` à l'aide de la console du Gestionnaire de package dans Visual Studio. Pour ouvrir la console du Gestionnaire de package, sélectionnez **Outils** > **Gestionnaire de Package NuGet** > **Console du Gestionnaire de package**. Pour exécuter la commande, accédez à la [page du package NuGet Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search), sélectionnez la version 9, puis copiez la commande du Gestionnaire de package. Dans la console du Gestionnaire de package, exécutez cette commande.

Pour installer le package NuGet `Microsoft.Extensions.Configuration.Json` dans Visual Studio, sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Gérer les packages NuGet pour la solution...** . Sélectionnez Parcourir et recherchez le package NuGet `Microsoft.Extensions.Configuration.Json`. Une fois le package trouvé, sélectionnez-le, sélectionnez votre projet, vérifiez que la version correspond à la dernière version stable, puis sélectionnez Installer.

## <a name="add-azure-cognitive-search-service-information"></a>Ajouter des informations relatives au service Recherche cognitive Azure

Pour vous connecter à votre service Recherche cognitive Azure, vous devez ajouter les informations correspondantes à votre projet. Cliquez avec le bouton droit sur votre projet dans l'Explorateur de solutions et sélectionnez **Ajouter** > **Nouvel élément...** . Nommez le fichier `appsettings.json` et sélectionnez **Ajouter**. 

Ce fichier devra être inclus dans votre répertoire de sortie. Pour ce faire, cliquez avec le bouton droit sur `appsettings.json` et sélectionnez **Propriétés**. Remplacez la valeur **Copier dans le répertoire de sortie** par **Copie du plus récent**.

Copiez le code JSON ci-dessous dans votre nouveau fichier JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Ajoutez les informations relatives à votre service de recherche, ainsi qu'à votre compte de stockage d'objets blob.

Les informations relatives à votre service de recherche sont disponible à partir de la page de recherche de compte dans le portail Azure. Le nom du compte se trouve sur la page principale et les clés sont accessibles en sélectionnant **Clés**.

Pour obtenir la chaîne de connexion blob, accédez à votre compte de stockage dans le portail Azure, sélectionnez **Clés d’accès**, puis copiez le champ **Chaîne de connexion**.

## <a name="add-namespaces"></a>Ajouter des espaces de noms

Ce tutoriel utilise différents types issus de divers espaces de noms. Pour utiliser ces différents types, ajoutez ce qui suit à `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Créer un client

Créez une instance de la classe `SearchServiceClient`.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` crée un `SearchServiceClient` à l’aide de valeurs stockées dans le fichier config de l’application (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> La classe `SearchServiceClient` gère les connexions à votre service de recherche. Pour éviter l'ouverture d'un trop grand nombre de connexions, essayez de partager une seule instance de `SearchServiceClient` dans votre application, si possible. Ses méthodes sont thread-safe pour activer le partage.
> 
> 

## <a name="create-a-data-source"></a>Création d'une source de données

Créer une nouvelle instance `DataSource` en appelant `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` implique la spécification du nom de la source de données, de la chaîne de connexion, ainsi que du nom du conteneur d’objets blob.

Bien qu’une telle stratégie ne soit pas utilisée dans ce tutoriel, une stratégie de suppression réversible est également définie et utilisée afin d'identifier les objets blob supprimés en fonction de la valeur d’une colonne de suppression réversible. La stratégie suivante considère qu’un objet blob est supprimé s’il présente une propriété de métadonnées `IsDeleted` avec la valeur `true`.

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Une fois l'objet `DataSource` initialisé, créez la source de données. `SearchServiceClient` a une propriété `DataSources`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour créer, afficher, mettre à jour ou supprimer des sources de données de la Recherche cognitive Azure.

Pour une requête réussie, la méthode renvoie la source de données créée. En cas de problème lié à la requête, comme un paramètre non valide, la méthode lève une exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Dans la mesure où il s’agit de votre première demande, vérifiez dans le portail Azure que la source de données a bien été créée dans la Recherche cognitive Azure. Dans la page de tableau de bord du service de recherche, vérifiez que la vignette Sources de données a un nouvel élément. Vous devrez peut-être attendre quelques minutes que la page du portail soit actualisée.

  ![Vignette Sources de données dans le portail](./media/cognitive-search-tutorial-blob/data-source-tile.png "Vignette Sources de données dans le portail")

## <a name="create-a-skillset"></a>Créer un ensemble de compétences

Dans cette section, vous définissez un ensemble d’étapes d’enrichissement que vous souhaitez appliquer à vos données. Chaque étape d’enrichissement est appelée *compétence* et l’ensemble des étapes d’enrichissement, *ensemble de compétences*. Ce tutoriel utilise des [compétences cognitives prédéfinies](cognitive-search-predefined-skills.md) pour l’ensemble de compétences :

+ [Reconnaissance optique de caractères](cognitive-search-skill-ocr.md) pour reconnaître le texte imprimé et manuscrit dans des fichiers image.

+ [Fusion de texte](cognitive-search-skill-textmerger.md) pour consolider en un champ unique du texte issu d’une collection de champs.

+ [Détection de la langue](cognitive-search-skill-language-detection.md) pour identifier la langue du contenu.

+ [Fractionnement de texte](cognitive-search-skill-textsplit.md) pour découper un texte long en plus petits morceaux avant d’appeler les compétences d’extraction de phrases clés et de reconnaissance d’entités. Les compétences d’extraction de phrases clés et de reconnaissance d’entités acceptent les entrées de 50 000 caractères maximum. Certains fichiers d’exemple doivent être fractionnés pour satisfaire cette limite.

+ [Reconnaissance d’entité](cognitive-search-skill-entity-recognition.md) pour extraire les noms d’organisations du contenu dans le conteneur d’objets blob.

+ [Extraction de phrases clés](cognitive-search-skill-keyphrases.md) pour extraire les principales expressions clés.

Lors du traitement initial, la Recherche cognitive Azure interprète chaque document pour lire le contenu de fichiers de différents formats. Le texte trouvé originaire du fichier source est placé dans un champ ```content``` généré, un pour chaque document. Par conséquent, définissez l’entrée pour ```"/document/content"``` de manière à utiliser ce texte. 

Les sorties peuvent être mappées à un index, utilisées comme entrée d’une compétence en aval, ou les deux, comme c’est le cas avec le code de langue. Dans l’index, un code de langue est utile pour le filtrage. En tant qu’entrée, le code de langue est utilisé par les compétences d’analyse de texte pour informer les règles linguistiques en matière de césure de mots.

Pour plus d’informations sur les principes de base des ensembles de compétences, consultez [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Compétence de reconnaissance optique des caractères

La compétence **OCR** extrait le texte des images. Cette compétence suppose l’existence d’un champ normalized_images. Pour générer ce champ, plus loin dans ce didacticiel, nous définirons la configuration ```"imageAction"``` dans la définition de l’indexeur sur ```"generateNormalizedImages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>Compétence de fusion de texte

Dans cette section, vous allez créer une compétence **Fusion** qui fusionne le champ de contenu de document avec le texte généré par la compétence de reconnaissance optique des caractères.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>Compétence Détection de langue

La compétence **Détection de langue** détecte la langue du texte d’entrée et renvoie un code de langue unique pour chaque document soumis dans la requête. Nous allons utiliser la sortie de la compétence **Détection de langue** en tant qu'entrée pour la compétence **Fractionnement de texte**.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>Compétence Fractionnement de texte

La compétence **Fractionnement** ci-dessous fractionnera le texte par pages et limitera la longueur de la page à 4 000 caractères comme indiqué par `String.Length`. L’algorithme essaiera de fractionner le texte en blocs dont la taille n'excède pas `maximumPageLength`. Dans ce cas, l’algorithme fera de son mieux pour arrêter la phrase sur une limite de phrase, de sorte que la taille du bloc peut être légèrement inférieure à `maximumPageLength`.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>Compétence de reconnaissance d’entités

Cette instance `EntityRecognitionSkill` est configurée pour reconnaître le type de catégorie `organization`. La compétence **Reconnaissance d’entités** peut également reconnaître les types de catégories `person` et `location`.

Notez que le champ « contexte » contient la valeur ```"/document/pages/*"``` avec un astérisque, ce qui signifie que l’étape d’enrichissement est appelée pour chaque page sous ```"/document/pages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>Compétence Extraction de phrases clés

Comme l'instance `EntityRecognitionSkill` précédemment créée, la compétence **Extraction de phrases clés** est appelée pour chaque page du document.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "keyPhrases",
    targetName: "keyPhrases"));

KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
    description: "Extract the key phrases",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="build-and-create-the-skillset"></a>Générer et créer l'ensemble de compétences

Générez l'`Skillset` en utilisant les compétences que vous avez créées.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Créez l'ensemble de compétences dans votre service de recherche.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Création d'un index

Dans cette section, vous définissez le schéma d’index en spécifiant les champs à inclure dans l’index de recherche et les attributs de recherche pour chaque champ. Les champs ont un type et peuvent prendre des attributs qui déterminent la façon dont le champ est utilisé (pour la recherche, le tri, etc.). Les noms des champs dans un index ne sont pas tenus de correspondre exactement aux noms des champs dans la source. Dans une étape ultérieure, vous ajoutez des mappages de champs dans un indexeur pour connecter les champs sources et de destination. Pour cette étape, définissez l’index à l’aide de conventions d’affectation de noms de champs appropriées à votre application de recherche.

Cet exercice utilise les champs et les types de champ suivants :

| Noms de champs : | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Types de champ : | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Créer la classe DemoIndex

Les champs de cet index sont définis à l’aide d’une classe de modèle. Chaque propriété de la classe de modèle comporte des attributs qui déterminent les comportements liés à la recherche du champ d’index correspondant. 

Nous allons ajouter la classe de modèle à un nouveau fichier C#. Cliquez avec le bouton droit sur votre projet, sélectionnez **Ajouter** > **Nouvel élément...** , sélectionnez « Classe » et nommez le fichier `DemoIndex.cs`, puis sélectionnez **Ajouter**.

Veillez à indiquer que vous souhaitez utiliser des types à partir des espaces de noms `Microsoft.Azure.Search` et `Microsoft.Azure.Search.Models`.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Ajoutez la définition de classe de modèle ci-dessous à `DemoIndex.cs` et incluez-la dans le même espace de noms que celui où vous créerez l’index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
```

Maintenant que vous avez défini une classe de modèle, de retour dans `Program.cs`, vous pouvez créer facilement une définition d’index. Le nom de cet index sera « demoindex ».

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Pendant le test, vous serez peut-être amené à tenter de créer l’index plusieurs fois. Dès lors, vérifiez si l’index que vous vous apprêtez à créer existe avant de tenter de le créer.

```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```

Pour en savoir plus sur la définition d’un index, consultez [Créer un index (API REST de la Recherche cognitive Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Créer un indexeur, mapper les champs et exécuter des transformations

À ce stade, vous avez créé une source de données, un ensemble de compétences et un index. Ces trois composants deviennent partie intégrante d’un [indexeur](search-indexer-overview.md) qui extrait chaque élément pour l’insérer dans une opération unique à plusieurs phases. Pour lier ces éléments en un indexeur, vous devez définir des mappages de champs.

+ Les fieldMappings sont traités avant l’ensemble de compétences, en mappant les champs sources de la source de données sur des champs cibles dans un index. Si les noms et types de champ sont identiques aux deux extrémités, aucun mappage n’est nécessaire.

+ Les outputFieldMappings sont traités après l’ensemble de compétences, en référençant les sourceFieldNames qui n’existent pas tant que le décodage de document ou l’enrichissement ne les ont pas créés. targetFieldName est un champ dans un index.

En plus de lier des entrées à des sorties, vous pouvez également utiliser les mappages de champs pour aplatir les structures de données. Pour plus d’informations, consultez [Guide pratique pour mapper des champs enrichis sur un index pouvant faire l’objet d’une recherche](cognitive-search-output-field-mapping.md).

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

Indexer indexer = new Indexer(
    name: "demoindexer",
    dataSourceName: dataSource.Name,
    targetIndexName: index.Name,
    description: "Demo Indexer",
    skillsetName: skillSet.Name,
    parameters: new IndexingParameters(
        maxFailedItems: -1,
        maxFailedItemsPerBatch: -1,
        configuration: config),
    fieldMappings: fieldMappings,
    outputFieldMappings: outputMappings);

try
{
    bool exists = serviceClient.Indexers.Exists(indexer.Name);

    if (exists)
    {
        serviceClient.Indexers.Delete(indexer.Name);
    }

    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

Notez que la création de l’indexeur requiert un certain temps. Même si le jeu de données est petit, les compétences analytiques exigent des calculs intensifs. Certaines compétences, telles que l’analyse d’image, sont des opérations longues.

> [!TIP]
> La création d’un indexeur appelle le pipeline. En cas de problèmes pour atteindre les données, mapper les entrées et les sorties, ou ordonner les opérations, ceux-ci apparaissent à ce stade.

### <a name="explore-creating-the-indexer"></a>Explorer la création de l’indexeur

Le code affecte à ```"maxFailedItems"``` la valeur -1, ce qui indique au moteur d’indexation d’ignorer les erreurs au cours de l’importation des données. Cela est utile car très peu de documents figurent dans la source de données de démonstration. Pour une source de données plus volumineuse, vous définiriez une valeur supérieure à 0.

Notez également la définition de ```"dataToExtract"``` sur ```"contentAndMetadata"```. Cette instruction indique à l’indexeur d’extraire automatiquement le contenu de fichiers de différents formats, ainsi que les métadonnées associées à chaque fichier.

Lorsque le contenu est extrait, vous pouvez définir `imageAction` pour extraire le texte des images trouvées dans la source de données. La configuration ```"imageAction"``` définie sur ```"generateNormalizedImages"```, associée à la compétence de reconnaissance optique des caractères et à la compétence de fusion de texte, indique à l’indexeur d’extraire le texte des images (par exemple, le mot « stop » d’un panneau de signalisation Stop) et de l’incorporer dans le champ de contenu. Ce comportement s’applique aux images intégrées dans les documents (pensez à une image dans un fichier PDF), ainsi qu’aux images trouvées dans la source de données, par exemple un fichier JPG.

## <a name="check-indexer-status"></a>Vérifier l’état de l’indexeur

Une fois l’indexeur défini, il s’exécute automatiquement lorsque vous envoyez la demande. Selon les compétences cognitives que vous avez définies, l’indexation peut prendre plus de temps que prévu. Pour savoir si l’indexeur est toujours en cours d’exécution, utilisez la méthode `GetStatus`.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
            break;
        case IndexerStatus.Running:
            Console.WriteLine("Indexer is running");
            break;
        case IndexerStatus.Unknown:
            Console.WriteLine("Indexer status is unknown");
            break;
        default:
            Console.WriteLine("No indexer information");
            break;
    }
}
catch (Exception e)
{
    // Handle exception
}
```

`IndexerExecutionInfo` correspond à l'historique actuel d'état et d'exécution d'un indexeur.

Les avertissements sont courants avec certaines combinaisons de fichiers sources et de compétences et n’indiquent pas toujours un problème. Dans ce tutoriel, les avertissements sont sans gravité (par exemple, aucune entrée de texte à partir des fichiers JPEG).
 
## <a name="query-your-index"></a>Interroger votre index

Une fois l’indexation terminée, vous pouvez exécuter des requêtes qui renvoient le contenu de champs individuels. Par défaut, le service Recherche cognitive Azure retourne les 50 meilleurs résultats. Les données d’exemple sont petites si bien que la configuration par défaut fonctionne correctement. Toutefois, lorsque vous travaillez avec des jeux de données plus volumineux, vous pouvez être amené à inclure des paramètres dans la chaîne de requête pour retourner plus de résultats. Pour obtenir des instructions, consultez [Guide pratique pour présenter les résultats dans la Recherche cognitive Azure](search-pagination-page-layout.md).

Comme étape de vérification, interrogez l’index au sujet de tous les champs.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` crée un `SearchIndexClient` à l’aide de valeurs stockées dans le fichier config de l’application (appsettings.json). Notez que la clé de l’API de requête du service de recherche est utilisée, et pas la clé d’administration.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

La sortie est le schéma d’index, avec le nom, le type et les attributs de chaque champ.

Envoyez une deuxième requête pour `"*"` afin de retourner tout le contenu d’un champ individuel, tel que `organizations`.

```csharp
SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

Répétez ces étapes pour les champs supplémentaires : content, languageCode, keyPhrases et organizations dans cet exercice. Vous pouvez retourner plusieurs champs via `$select` à l’aide d’une liste délimitée par des virgules.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Réinitialiser et réexécuter

Dans les premières étapes expérimentales de développement, l’approche la plus pratique pour les itérations de conception consiste à supprimer les objets de la Recherche cognitive Azure et à autoriser votre code à les reconstruire. Les noms des ressources sont uniques. La suppression d’un objet vous permet de le recréer en utilisant le même nom.

Ce tutoriel a pris soin de vérifier les indexeurs et index existants, et de les supprimer, le cas échéant, pour vous permettre de réexécuter votre code.

Vous pouvez également utiliser le portail pour supprimer des index, des indexeurs et des ensembles de compétences.

Lorsque votre code arrive à maturité, vous pouvez affiner une stratégie de regénération. Pour plus d’informations, consultez [How to rebuild an Azure Search index](search-howto-reindex.md) (Régénérer un index Recherche Azure).

## <a name="takeaways"></a>Éléments importants à retenir

Ce tutoriel présente les étapes de base pour générer un pipeline d’indexation enrichie via la création de composants : une source de données, un ensemble de compétences, un index et un indexeur.

Les [compétences intégrées](cognitive-search-predefined-skills.md) ont été introduites, ainsi que la définition d’un ensemble de compétences et les mécanismes de chaînage de compétences via des entrées et des sorties. Vous avez également appris que `outputFieldMappings` est requis dans la définition de l’indexeur pour acheminer les valeurs enrichies du pipeline dans un index de recherche, sur un service de Recherche cognitive Azure.

Enfin, vous avez appris à tester les résultats et réinitialiser le système pour des itérations ultérieures. Vous avez appris qu’émettre des requêtes par rapport à l’index retourne la sortie créée par le pipeline d’indexation enrichie. Vous avez également appris à vérifier l’état de l’indexeur et quels objets supprimer avant de réexécuter un pipeline.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Le moyen le plus rapide de procéder à un nettoyage après un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche cognitive Azure et le service Blob Azure. En supposant que vous avez placé ces deux services dans le même groupe, supprimez maintenant le groupe de ressources pour supprimer définitivement tout ce qu’il contient, y compris les services et tout le contenu stocké que vous avez créé pour ce tutoriel. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble de chaque service.

## <a name="next-steps"></a>Étapes suivantes

Personnalisez ou étendez le pipeline avec des compétences personnalisées. La création d’une compétence personnalisée et son ajout dans un ensemble de compétences vous permet d’intégrer une analyse de texte ou d’image que vous écrivez vous-même.

> [!div class="nextstepaction"]
> [Exemple : Création d’une compétence personnalisée pour l’enrichissement par l’IA](cognitive-search-create-custom-skill-example.md)
