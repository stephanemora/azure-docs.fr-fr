---
title: Concepts de base de connaissances
titleSuffix: Azure Cognitive Search
description: Envoyez des documents enrichis à Stockage Azure afin de pouvoir les consulter, remodeler et utiliser des documents enrichis dans Recherche cognitive Azure et d’autres applications.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: baa4a78e23b83fa7c138e71b92dba12ba23a3ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524607"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Base de connaissances dans Recherche cognitive Azure

La base de connaissances est une fonctionnalité du service Recherche cognitive Azure. Elle conserve la sortie d’un [pipeline d’enrichissement par IA](cognitive-search-concept-intro.md) dans le service Stockage Azure pour une analyse indépendante ou un traitement en aval. Un *document enrichi* est la sortie d’un pipeline, créée à partir d’un contenu qui a été extrait, structuré et analysé à l’aide de processus IA. Dans un pipeline IA standard, les documents enrichis sont temporaires, utilisés uniquement pendant l’indexation, puis ignorés. La création d’une base de connaissances permet de conserver les documents enrichis à des fins d’inspection ou pour d’autres scénarios d’exploration des connaissances.

Si vous avez déjà utilisé des compétences cognitives par le passé, vous savez que des *ensembles de compétences* déplacent un document dans une séquence d’enrichissements. Le résultat peut être un index de recherche ou des projections dans une base de connaissances. Les deux sorties, index de recherche et base de connaissances, sont des produits du même pipeline. Elles sont dérivées des mêmes entrées mais elles ont pour résultat une sortie structurée, stockée et utilisée dans des applications distinctes.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Pipeline avec ensemble de compétences" border="false":::

<!-- previous version of the architecture diagram
:::image type="content" source="media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png" alt-text="Knowledge store in pipeline diagram" border="false"::: -->

Physiquement, une base de connaissances représente un [Stockage Azure](../storage/common/storage-account-overview.md), soit le Stockage Table Azure, soit le Stockage Blob Azure, ou les deux. Tout outil ou processus pouvant se connecter au Stockage Azure peut utiliser le contenu d’un magasin de connaissances.

Vue depuis l’Explorateur Stockage, une base de connaissances est juste une collection de tables, d’objets ou de fichiers. L’exemple suivant montre une base de connaissances composée de trois tables avec des champs provenant de la source de données ou créés via des enrichissements (consultez « score de sentiments » et « translated_text »).

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Lecture et écriture des compétences à partir de l’arborescence d’enrichissement" border="true":::

## <a name="benefits-of-knowledge-store"></a>Avantages de la base de connaissances

Une base de connaissances vous fournit une structure, un contexte et un contenu réel, extraits de fichiers de données non structurés et semi-structurés, tels que des objets blob, des fichiers image ayant fait l’objet d’une analyse, ou même des données structurées remodelées dans nouveaux formulaires. Une [procédure pas à pas](knowledge-store-create-rest.md) montre comment un document JSON dense de source sure est partitionné en sous-structures, reconstitué en nouvelles structures et mis à disposition pour des processus en aval, comme des charges de travail liées au Machine Learning et à la science des données.

Bien qu’il soit utile de voir ce qu’un pipeline d’enrichissement par l’IA peut produire, le véritable potentiel d’une base de connaissances réside dans sa capacité à remodeler les données. Vous pouvez commencer avec un ensemble de compétences de base, puis procéder à une itération sur celui-ci afin d’ajouter des niveaux de structure croissants, que vous pouvez ensuite combiner en nouvelles structures, utilisables dans des applications autres que la Recherche cognitive Azure.

La base de connaissances vous offre notamment les avantages suivants :

+ Utilisez des documents enrichis dans des [outils d'analyse et de génération d'états](#tools-and-apps) autres que des outils de recherche. Power BI avec Power Query est un excellent choix, mais tout outil ou application capable de se connecter à Stockage Azure peut puiser dans une base de connaissances que vous avez créée.

+ Affinez un pipeline d'indexation IA lors des étapes de débogage et de la définition des ensembles de compétences. Une base de connaissances vous présente le produit de la définition d'un ensemble de compétences dans un pipeline d'indexation IA. Vous pouvez utiliser ces résultats afin de concevoir un ensemble de compétences plus performant car vous voyez exactement à quoi ressemblent les enrichissements. Vous pouvez utiliser l’[Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) de Stockage Azure pour afficher le contenu d’une base de connaissances.

+ Modelez les données pour les transformer en nouveaux formulaires. La remodélisation est codifiée dans des ensembles de compétences, mais la [compétence Modélisateur](cognitive-search-skill-shaper.md) du service Recherche cognitive Azure offre un contrôle explicite et permet de créer plusieurs modélisations. La modélisation vous permet de définir une projection qui s’aligne sur l’utilisation prévue des données tout en conservant les relations.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>Définition de la base de connaissances

Au moment de l’exécution de l’indexeur, les bases de connaissances sont créées dans votre [compte Stockage Azure](../storage/common/storage-account-overview.md) à l’aide de Stockage Table Azure, Stockage Blob Azure, ou les deux. 

Les structures de données du service Stockage Azure sont spécifiées via l’élément `projections` d’une définition `knowledgeStore` dans un ensemble de compétences. Les [projections](knowledge-store-projection-overview.md) peuvent être articulées sous forme de tables, d’objets ou de fichiers. Vous pouvez également avoir plusieurs ensembles (ou *groupes de projections*) pour créer plusieurs structures de données à des fins distinctes.

```json
"knowledgeStore":{
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

Le type de projection que vous spécifiez dans cette structure détermine le type de stockage utilisé par la base de connaissances.

+ `tables` permet de projeter du contenu enrichi dans le service Stockage Table. Définissez une projection de table lorsque vous avez besoin de structures de rapports tabulaires pour des entrées dans des outils analytiques, ou les exporter en tant que trames de données dans d’autres magasins de données. Vous pouvez spécifier plusieurs `tables` dans le même groupe de projections pour obtenir un sous-ensemble ou une coupe transversale des documents enrichis. Dans le même groupe de projection, les relations entre tables sont conservées afin que vous puissiez les utiliser toutes.

+ `objects` permet de projeter un document JSON dans le service Stockage Blob. La représentation physique d’un `object` est une structure JSON hiérarchique représentant un document enrichi.

+ `files` permet de projeter des fichiers image dans le service Stockage Blob. Un `file` est une image extraite d’un document, transférée intacte vers un stockage Blob.

## <a name="create-a-knowledge-store"></a>Créer une base de connaissances

Pour créer une base de connaissances, utilisez le portail ou une API. Vous aurez besoin de [Stockage Azure](../storage/index.yml), d’un [ensemble de compétences](cognitive-search-working-with-skillsets.md) et d’un [indexeur](search-indexer-overview.md). Dans la mesure où les indexeurs nécessitent un index de recherche, vous devez également fournir une définition d’index.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/kstore-portal)

[Créez votre première base de connaissances en quatre étapes](knowledge-store-connect-power-bi.md) à l’aide de l’Assistant **Importation des données**. L’Assistant vous guide tout au long des tâches suivantes :

1. Sélectionnez une source de données prise en charge, qui fournit le contenu brut.

1. Spécifier l’enrichissement : attachez une ressource Cognitive Services, sélectionnez des compétences, puis spécifiez une base de connaissances. Au cours de cette étape, vous allez choisir un compte Stockage Azure et décider de créer des objets ou des tables, ou les deux.

1. Créez un schéma d’index. L’Assistant l’exige et peut en inférer un pour vous.

1. Exécutez l’Assistant. L’extraction, l’enrichissement et le stockage se produisent dans cette dernière étape.

Quand vous utilisez l’Assistant, plusieurs tâches supplémentaires sont gérées de manière interne, ce qui vous évite d’avoir à le faire dans le code. La modélisation et les projections (définitions des structures de données physiques dans le service Stockage Azure) sont créées automatiquement. Quand vous créez manuellement une base de connaissances, votre code doit couvrir ces étapes.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

Vous pouvez utiliser l’API REST version `2020-06-30` pour créer une base de connaissances via des ajouts à un ensemble de compétences.

+ [Créer un ensemble de compétences (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Mettre à jour un ensemble de compétences (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

Une `knowledgeStore` est définie au sein d’un [ensemble de compétences](cognitive-search-working-with-skillsets.md) qui est appelé à son tour par un [indexeur](search-indexer-overview.md). Pendant l’enrichissement, la Recherche cognitive Azure crée un espace dans votre compte de stockage Azure et projette les documents enrichis en tant qu’objets blob ou dans des tables, en fonction de votre configuration.

Tâches que votre code doit gérer :

+ Spécifier les projections à intégrer au service Stockage Azure (tables, objets, fichiers)
+ Incluez une compétence Modélisateur dans votre ensemble de compétences pour déterminer le schéma et le contenu de la projection
+ Affecter la modélisation nommée à une projection

Pour découvrir facilement comment procéder, vous pouvez [créer votre première base de connaissances à l’aide de Postman](knowledge-store-create-rest.md).

### <a name="net-sdk"></a>[**Kit de développement logiciel (SDK) .NET**](#tab/kstore-dotnet)

Pour les développeurs .NET, utilisez la [classe KnowledgeStore](/dotnet/api/azure.search.documents.indexes.models.knowledgestore) de la bibliothèque de client Azure.Search.Documents.

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>Se connecter avec des applications

Une fois les enrichissements disponibles dans le stockage, n'importe quel outil ou technologie capable de se connecter à Stockage Blob ou Stockage Table peut être utilisé pour explorer, analyser ou utiliser le contenu. La liste suivante est un début :

+ L'[Explorateur Stockage](knowledge-store-view-storage-explorer.md) permet d'afficher la structure et le contenu des documents enrichis. Considérez-le comme votre outil de référence pour afficher le contenu de la base de connaissances.

+ [Power BI](knowledge-store-connect-power-bi.md) pour la création de rapports et l’analyse. 

+ [Azure Data Factory](../data-factory/index.yml) permet d'effectuer d'autres manipulations.

## <a name="content-lifecycle"></a>Cycle de vie du contenu

Chaque fois que vous exécutez l’indexeur et l’ensemble de compétences, la base de connaissances est mise à jour si l’ensemble de compétences ou les données sources sous-jacentes ont changé. Les changements détectés par l’indexeur sont propagés via le processus d’enrichissement aux projections dans la base de connaissances, ce qui permet de garantir que les données projetées sont une représentation actualisée du contenu de la source de données d’origine. 

> [!Note]
> Bien que vous puissiez modifier les données dans les projections, les mises à jour sont remplacées au prochain appel du pipeline, en supposant que le document présent dans les données sources soit mis à jour. 

### <a name="changes-in-source-data"></a>Changements dans les données sources

Pour les sources de données qui prennent en charge le suivi des modifications, un indexeur traite les nouveaux documents ainsi que ceux auxquels des changements ont été apportés, tout en ignorant les documents existants déjà traités. Les informations d’horodatage varient en fonction de la source de données. Toutefois, dans un conteneur d’objets blob, l’indexeur examine la date `lastmodified` pour déterminer quels sont les objets blob à ingérer.

### <a name="changes-to-a-skillset"></a>Changements apportés à un ensemble de compétences

Si vous apportez des changements à un ensemble de compétences, vous devez [activer la mise en cache des documents enrichis](cognitive-search-incremental-indexing-conceptual.md) pour réutiliser les enrichissements existants dans la mesure du possible.

En l’absence de mise en cache incrémentielle, l’indexeur traite toujours les documents dans l’ordre de la limite supérieure, sans revenir en arrière. L’indexeur traite les objets blob triés par `lastModified`, quels que soient les changements apportés aux paramètres de l’indexeur ou à l’ensemble de compétences. Si vous changez un ensemble de compétences, les documents traités ne sont pas mis à jour pour refléter le nouvel ensemble de compétences. Les documents traités après l’ajout de changements à l’ensemble de compétences utilisent le nouvel ensemble de compétences. Ainsi, les documents d’index sont un mélange d’anciens et de nouveaux ensembles de compétences.

Avec la mise en cache incrémentielle et après la mise à jour d’un ensemble de compétences, l’indexeur réutilise les enrichissements non affectés par les changements apportés à l’ensemble de compétences. Les enrichissements en amont sont extraits du cache, tout comme les enrichissements indépendants et isolés de la compétence à laquelle des changements ont été apportés.

### <a name="deletions"></a>Suppressions

Bien qu’un indexeur crée et mette à jour des structures et du contenu dans le service Stockage Azure, il ne les supprime pas. Les projections continuent d’exister même quand l’indexeur ou l’ensemble de compétences est supprimé. En tant que propriétaire du compte de stockage, vous devez supprimer une projection si elle n’est plus nécessaire. 

## <a name="next-steps"></a>Étapes suivantes

La base de connaissances offre la persistance de documents enrichis, utile lors de la conception d’un ensemble de compétences, ou la création de structures et de contenu pour une consommation par les applications clientes qui peuvent accéder à un compte de stockage Azure.

L’approche la plus simple pour créer des documents enrichis consiste à [utiliser le portail](knowledge-store-create-portal.md). Toutefois, vous pouvez également utiliser Postman et l’API REST, ce qui est plus utile si vous souhaitez obtenir des insights sur la façon dont les objets sont créés et référencés par programmation.

> [!div class="nextstepaction"]
> [Créer une base de connaissances à l’aide de Postman et de REST](knowledge-store-create-rest.md)

Ou, examinez de plus près les [projections](knowledge-store-projection-overview.md). Pour accéder à un exemple illustrant les concepts de projections avancées tels que le découpage, la modélisation inline et les relations, commencez par [définir les projections dans une base de connaissances](knowledge-store-projections-examples.md).
