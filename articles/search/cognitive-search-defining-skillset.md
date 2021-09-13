---
title: Créer un ensemble de compétences
titleSuffix: Azure Cognitive Search
description: Définissez des étapes d’extraction des données, de traitement du langage naturel ou d’analyse de l’image pour enrichir et extraire des informations structurées à partir de vos données pour les utiliser dans la Recherche cognitive Azure.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 5f47eef6d3aeecd64cb3777b1d0858b4904d137d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122563699"
---
# <a name="create-a-skillset-in-azure-cognitive-search"></a>Créer un ensemble de compétences dans la Recherche cognitive Azure

![Étapes de l'indexeur](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "étapes de l'indexeur")

Un ensemble de compétences définit les opérations qui extraient et enrichissent des données afin qu’elles puissent faire l’objet de recherches. Un ensemble de compétences est exécuté après le craquage de document, quand le contenu du texte et de l’image est extrait des documents sources et une fois que les champs du document source sont (éventuellement) mappés aux champs de destination d’un index ou d’une base de connaissances.

Dans cet article, vous allez découvrir les étapes de création d’un ensemble de compétences. Pour référence, cet article utilise l’opération [Créer un ensemble de compétences (API REST)](/rest/api/searchservice/create-skillset). 

Voici certaines règles d’utilisation des ensembles de compétences :

+ Un ensemble de compétences est une ressource de niveau supérieur, ce qui signifie qu’il peut être créé une seule fois et référencé par de nombreux indexeurs.
+ Un ensemble de compétences doit contenir au moins une compétence.
+ Un ensemble de compétences peut répéter les compétences de même type (par exemple, plusieurs compétences de modélisation).

Rappelez-vous que les indexeurs pilotent l’exécution des compétences, ce qui signifie que vous devez également créer un [indexeur](search-howto-create-indexers.md), une [source de données](search-data-sources-gallery.md) et un [index de recherche](search-what-is-an-index.md) avant de pouvoir tester votre ensemble de compétences.

> [!TIP]
> Activez la [mise en cache de l’enrichissement](cognitive-search-incremental-indexing-conceptual.md) pour réutiliser le contenu que vous avez déjà traité et réduire le coût du développement.

## <a name="skillset-definition"></a>Définition du jeu de compétences

Commencez par la structure de base. Dans l’[API REST](/rest/api/searchservice/create-skillset), un ensemble de compétences est créé au format JSON et contient les sections suivantes :

```json
{
   "name":"skillset-template",
   "description":"A description makes the skillset self-documenting (comments aren't allowed in JSON itself)",
   "skills":[
      
   ],
   "cognitiveServices":{
      "@odata.type":"#Microsoft.Azure.Search.CognitiveServicesByKey",
      "description":"A Cognitive Services resource in the same region as Azure Cognitive Search",
      "key":"<Your-Cognitive-Services-Multiservice-Key>"
   },
   "knowledgeStore":{
      "storageConnectionString":"<Your-Azure-Storage-Connection-String>",
      "projections":[
         {
            "tables":[ ],
            "objects":[ ],
            "files":[ ]
         }
      ]
    },
    "encryptionKey":{ }
}
```

Après le nom et la description, un ensemble de compétences a quatre propriétés principales :

+ Tableau `skills`, [collection non triée de compétences](cognitive-search-predefined-skills.md), dont la séquence d’exécution est déterminée par le service de recherche en fonction des entrées requises pour chaque compétence. Si les compétences sont indépendantes, elles s’exécutent en parallèle. Les compétences peuvent être utilitaires (comme la division de texte), transformationnelles (basées sur l’intelligence artificielle de Cognitive Services) ou il peut s’agir de compétences personnalisées que vous fournissez. Vous trouverez un exemple de tableau de compétences dans la section suivante.

+ `cognitiveServices` est utilisé pour les [compétences facturables](cognitive-search-predefined-skills.md) qui appellent les API Cognitive Services. Supprimez cette section si vous n’utilisez pas de compétences facturables ou de recherche d’entité personnalisée. [Attachez une ressource](cognitive-search-attach-cognitive-services.md) dans le cas contraire.

+ `knowledgeStore`, (facultatif) spécifie un compte Stockage Azure et les paramètres de la projection de la sortie de l’ensemble de compétences dans des tables, des objets blob et des fichiers au sein de Stockage Azure. Supprimez cette section si vous n’en avez pas besoin, sinon [spécifiez une base de connaissances](knowledge-store-create-rest.md).

+ `encryptionKey`, (facultatif) spécifie un coffre de clés Azure et des [clés gérées par le client](search-security-manage-encryption-keys.md) utilisées pour chiffrer le contenu sensible dans la définition d’un ensemble de compétences. Supprimez cette propriété si vous n’utilisez pas le chiffrement géré par le client.

## <a name="add-a-skills-array"></a>Ajouter un tableau de compétences

Dans la définition d’un ensemble de compétences, le tableau de compétences spécifie les compétences à exécuter. L’exemple suivant vous présente sa composition en montrant deux [compétences intégrées](cognitive-search-predefined-skills.md) non liées. Notez que chaque compétence a un type, un contexte, des entrées et des sorties. 

```json
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "score",
        "targetName": "mySentiment"
      }
    ]
  }
]
```

> [!NOTE]
> Vous pouvez créer des ensembles de compétences complexes, avec des boucles et des branches, à l’aide de la [compétence conditionnelle](cognitive-search-skill-conditional.md) pour créer des expressions. La syntaxe se base sur la notation du [pointeur JSON](https://tools.ietf.org/html/rfc6901) pour les chemins, légèrement modifiée afin d’identifier les nœuds dans l’arborescence d’enrichissements. Un `"/"` fait passer à un niveau inférieur dans l’arborescence et `"*"` est utilisé comme un opérateur for-each dans le contexte. De nombreux exemples de cet article illustrent la syntaxe. 

### <a name="how-built-in-skills-are-structured"></a>Structure des compétences intégrées

Chaque compétence est unique du point de vue de ses valeurs d’entrée et des paramètres qu’elle prend. La [documentation d’une compétence](cognitive-search-predefined-skills.md) décrit tous les paramètres et les propriétés de cette compétence. Même s’il y a des différences, la plupart des compétences partagent un ensemble commun et sont modélisées de la même façon. Pour illustrer plusieurs points, la [compétence de reconnaissance d’entité](cognitive-search-skill-entity-recognition-v3.md) fournit un exemple :

```json
{
  "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
  "context": "/document",
  "categories": [ "Organization" ],
  "defaultLanguageCode": "en",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "organizations",
      "targetName": "orgs"
    }
  ]
}
```

Les paramètres courants incluent « odata.type », « inputs » et « outputs ». Les autres paramètres, à savoir « categories » et « defaultLanguageCode », sont des exemples de paramètres propres à la reconnaissance d’entité. 

+ **« odata.type »** identifie de manière unique chaque compétence. Vous pouvez trouver le type dans la [documentation de référence sur les compétences](cognitive-search-predefined-skills.md).

+ **« context »** est un nœud dans une arborescence d’enrichissement et représente le niveau auquel les opérations ont lieu. Toutes les compétences ont cette propriété. Si le champ « context » n’est pas défini explicitement, le contexte par défaut est `"/document"`. Dans l’exemple, le contexte représente l’ensemble du document, ce qui implique que la compétence de reconnaissance d’entité est appelée une fois par document.

  Le contexte détermine également où les sorties sont produites dans l’arborescence d’enrichissement. Dans cet exemple, la compétence retourne une propriété appelée `"organizations"`, capturée sous la forme `orgs`, qui est ajoutée en tant que nœud enfant de `"/document"`. Dans les compétences en aval, le chemin de ce nouveau nœud d’enrichissement est `"/document/orgs"`. Pour un document particulier, la valeur de `"/document/orgs"` est un tableau des organisations extraites du texte (par exemple : `["Microsoft", "LinkedIn"]`). Pour plus d’informations sur la syntaxe des chemins, consultez [Référencement d’annotations dans un ensemble de compétences](cognitive-search-concept-annotations-syntax.md).

+ **« inputs »** spécifie l’origine des données entrantes et la façon dont elles sont utilisées. Dans le cas de la reconnaissance d’entité, l’une des entrées est `"text"`, qui est le contenu à analyser à la recherche d’entités. Le contenu provient du nœud `"/document/content"` dans une arborescence d’enrichissement. Dans une arborescence d’enrichissement, `"/document"` est le nœud racine. Pour les documents récupérés avec un indexeur d’objets blob Azure, le champ `content` de chaque document est un champ standard créé par l’indexeur. 

+ **« outputs »** représente la sortie de la compétence. Chaque compétence est conçue pour émettre des types spécifiques de sortie, qui sont référencés par nom dans l’ensemble de compétences. Dans le cas de la reconnaissance d’entité, `"organizations"` est l’une des sorties qu’elle prend en charge. La documentation de chaque compétence décrit les sorties qu’elle peut produire.

Les sorties existent uniquement pendant le traitement. Pour chaîner cette sortie à l’entrée d’une compétence en aval, référencez la sortie en tant que `"/document/orgs"`. Pour envoyer la sortie à un champ dans un index de recherche, [créez un mappage de champs de sortie](cognitive-search-output-field-mapping.md) dans un indexeur. Pour envoyer la sortie à une base de connaissances, [créez une projection](knowledge-store-projection-overview.md).

Les sorties d’une compétence peuvent être en conflit avec les sorties d’une autre compétence. Si plusieurs compétences retournent la même sortie, utilisez `"targetName"` pour la désambiguïsation de nom dans les chemins de nœud d’enrichissement.

Certaines situations demandent de référencer chaque élément d’un tableau séparément. Par exemple, vous souhaitez transmettre *chaque élément* de `"/document/orgs"` séparément à une autre compétence. Pour ce faire, ajoutez un astérisque au chemin : `"/document/orgs/*"` 

La deuxième compétence correspondant à l’analyse des sentiments suit le même modèle que le premier enrichisseur. Elle dispose de l’entrée `"/document/content"`, et retourne un score de tendance pour chaque instance de contenu. Comme vous n’avez pas défini le champ « context » explicitement, la sortie (mySentiment) est maintenant un enfant de `"/document"`.

```json
{
  "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "score",
      "targetName": "mySentiment"
    }
  ]
}
```

## <a name="adding-a-custom-skill"></a>Ajout d’une compétence personnalisée

Vous trouverez ci-dessous un exemple de [compétence personnalisée](cognitive-search-custom-skill-web-api.md). L’URI pointe vers une fonction Azure, qui à son tour appelle le modèle ou la transformation que vous fournissez. Pour plus d’informations, consultez [Définir une interface personnalisée](cognitive-search-custom-skill-interface.md).

Même si la compétence personnalisée exécute du code qui est externe au pipeline, dans un tableau de compétences, il s’agit simplement d’une autre compétence. Comme les compétences intégrées, elle a un type, un contexte, des entrées et des sorties. De plus, elle lit et écrit dans une arborescence d’enrichissement, tout comme les compétences intégrées. Notez que le champ « contexte » contient la valeur `"/document/orgs/*"` avec un astérisque, ce qui signifie que l’étape d’enrichissement est appelée *pour chaque* organisation sous `"/document/orgs"`.

La sortie, dans ce cas une description de société, est générée pour chaque organisation identifiée. Lorsque vous faites référence à la description d’une étape en aval (par exemple, dans l’extraction d’expressions clés), vous utilisez le chemin d’accès `"/document/orgs/*/companyDescription"` pour ce faire. 

```json
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "This skill calls an Azure function, which in turn calls custom code",
  "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeCode?code=foo",
  "httpHeaders": {
      "Ocp-Apim-Subscription-Key": "foobar"
  },
  "context": "/document/orgs/*",
  "inputs": [
    {
      "name": "query",
      "source": "/document/orgs/*"
    }
  ],
  "outputs": [
    {
      "name": "description",
      "targetName": "companyDescription"
    }
  ]
}
```

## <a name="send-output-to-an-index"></a>Envoyer la sortie à un index

À mesure que chaque compétence s’exécute, sa sortie est ajoutée en tant que nœuds dans l’arborescence d’enrichissement d’un document. Les documents enrichis existent dans le pipeline en tant que structures de données temporaires. Pour créer une structure de données permanente et obtenir une visibilité complète sur ce qu’une compétence produit réellement, vous devez envoyer la sortie à un index de recherche ou à une [base de connaissances](knowledge-store-concept-intro.md).

Dans les premières étapes de l’évaluation d’un ensemble de compétences, vous voudrez vérifier les résultats préliminaires avec un minimum d’effort. Nous vous recommandons d’utiliser l’index de recherche, car il est plus simple à configurer. Pour chaque sortie de compétence, [définissez un mappage de champs de sortie](cognitive-search-output-field-mapping.md) dans l’indexeur et un champ dans l’index.

:::image type="content" source="media/cognitive-search-defining-skillset/skillset-indexer-index-combo.png" alt-text="Diagramme d’objet montrant comment une entité persons est définie dans une sortie de la compétence, un mappage de champs de l’indexeur et un champ d’index.":::

Après avoir exécuté l’indexeur, vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) pour retourner des documents à partir de l’index et vérifier le contenu de chaque champ afin de déterminer ce que l’ensemble de compétences a détecté ou créé.

L’exemple suivant montre les résultats d’une compétence de reconnaissance d’entité qui a détecté persons, locations, organizations et d’autres entités dans un segment de texte. L’affichage des résultats dans l’Explorateur de recherche peut vous aider à déterminer si une compétence ajoute de la valeur à votre solution.

:::image type="content" source="media/cognitive-search-defining-skillset/doc-in-search-explorer.png" alt-text="Capture d’écran d’un document dans l’Explorateur de recherche":::

## <a name="next-steps"></a>Étapes suivantes

Les champs de source d’entrée et de contexte sont des chemins de nœuds dans une arborescence d’enrichissement. En guise d’étape suivante, découvrez plus en détail la syntaxe de la configuration des chemins des nœuds dans une arborescence d’enrichissement.

> [!div class="nextstepaction"]
> [Référencement d’annotations dans un ensemble de compétences](cognitive-search-concept-annotations-syntax.md)
