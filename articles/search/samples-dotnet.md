---
title: Exemples relatifs à .NET
titleSuffix: Azure Cognitive Search
description: Trouvez des exemples de code C# de démonstration de Recherche cognitive Azure qui utilisent les bibliothèques de client .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 32fbdae5719e2f8e379b39076f8557b3db0d61ca
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112020022"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Exemples de code (C#) .NET pour Recherche cognitive Azure

Découvrez les exemples de code C# qui illustrent les fonctionnalités et le flux de travail d’une solution Recherche cognitive Azure. Ils utilisent la [**bibliothèque de client Recherche cognitive Azure**](/dotnet/api/overview/azure/search) pour le [**SDK Azure pour .NET**](/dotnet/azure/), que vous pouvez explorer à l’aide des liens suivants.

| Cible | Lien |
|--------|------|
| Téléchargement de package | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| Informations de référence sur l'API | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Cas de test d’API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Code source | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

Les exemples de code de l’équipe de développement du SDK Azure illustrent l’utilisation de l’API. Vous les trouverez dans [**Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) sur GitHub.

| Exemples | Description |
|---------|-------------|
| [« Hello World », synchrone](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Montre comment créer un client, s’authentifier et gérer les erreurs en utilisant des méthodes synchrones.|
| [« Hello World », asynchrone](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Montre comment créer un client, s’authentifier et gérer les erreurs en utilisant des méthodes asynchrones.  |
| [Opérations au niveau du service](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Montre comment créer des index, des indexeurs, des sources de données, des ensembles de compétences et des mappages de synonymes. Cet exemple montre également comment obtenir des statistiques sur le service et comment interroger un index.  |
| [Opérations d’index](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Montre comment effectuer une action sur un index existant, en l’occurrence obtenir le nombre de documents stockés dans l’index.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Montre une technique permettant de travailler avec des types de données non pris en charge.  |
| [Indexation de documents (modèle push)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexation de modèle « push », où vous envoyez une charge utile JSON à un index sur un service.   |
| [Exemple de clé de chiffrement](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Montre comment utiliser une clé de chiffrement gérée par le client pour ajouter une couche supplémentaire de protection sur du contenu sensible.  |

## <a name="doc-samples"></a>Exemples de documentation

Les exemples de code de l’équipe Recherche cognitive illustrent les fonctionnalités et les flux de travail. La plupart sont référencés dans des tutoriels, démarrages rapides et articles pratiques. Vous trouverez ces exemples dans [**Azure-Samples/azure-search-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) et dans [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) sur GitHub.

| Exemples | Article  |
|---------|-------------|
| [démarrage rapide](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Code source pour [Démarrage rapide : Créer un index de recherche](search-get-started-dotnet.md). Traite du flux de travail de base pour la création, le chargement et l’interrogation d’un index de recherche à l’aide d’exemples de données. |
| [search-website](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website) | Code source pour le [Tutoriel : Ajouter une recherche à des applications web](tutorial-csharp-overview.md). Illustre une application de recherche de bout en bout qui comprend un client enrichi plus des composants permettant d’héberger l’application et de gérer les requêtes de recherche.|
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [Guide pratique pour utiliser la bibliothèque de client .NET](search-howto-dotnet-sdk.md). Flux de travail de base, tout en décrivant plus en détail l’utilisation des API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Code source pour [Exemple : Ajouter des synonymes en C#](search-synonyms-tutorial-sdk.md). Les listes de synonymes sont utilisées pour l’extension des requêtes, fournissant des termes externes à un index, qui peuvent être mis en correspondance. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Code source pour [Tutoriel : Indexer des données Azure SQL à l’aide du SDK .NET](search-indexer-tutorial.md). Cet article montre comment configurer un indexeur Azure SQL qui a une planification, des mappages de champs et des paramètres.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [Guide pratique pour configurer des clés gérées par le client pour le chiffrement des données](search-security-manage-encryption-keys.md). |
| [Créer votre première application en C#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Code source pour [Tutoriel : Créer votre première application de recherche](tutorial-csharp-create-first-app.md). Bien que la plupart des exemples soient des applications console, cet exemple MVC utilise une page web pour montrer l’exemple d’index Hotels avec la recherche de base, la pagination, l’autocomplétion, et les requêtes, facettes et filtres suggérés. |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Code source pour [Tutoriel : Indexer à partir de plusieurs sources de données](tutorial-multiple-data-sources.md). |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Code source pour [Tutoriel : Optimiser l’indexation avec l’API Push](tutorial-optimize-indexing-push-api.md).  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Code source pour [Tutoriel : Contenu permettant la recherche, généré par IA à partir de blobs Azure avec le SDK .NET](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Essayez le [navigateur d’exemples](/samples/browse/?languages=csharp&products=azure-cognitive-search) pour rechercher des exemples de code Microsoft dans GitHub, filtrés par produit, par service et par langage.

## <a name="other-samples"></a>Autres exemples

Les exemples suivants sont également publiés par l’équipe Recherche cognitive, mais la documentation n’y fait pas référence. Les fichiers Lisez-moi associés indiquent les instructions d’utilisation.

| Exemples | Description |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Code source pour des compétences personnalisées consommables que vous pouvez incorporer dans vos propres solutions.  |
| [Accélérateur de solution d’exploration des connaissances](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Inclut des modèles, des fichiers de support et des rapports analytiques pour vous aider à prototyper une solution d’exploration des connaissances de bout en bout.  |
| [Dépôt de l’application de recherche sur la Covid-19](https://github.com/liamca/covid19search) | Dépôt de code source pour l’[application de recherche sur la Covid-19](https://covid19search.azurewebsites.net/) basée sur Recherche cognitive |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | En savoir plus sur la [solution JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |
| [Accélérateur Recherche + QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | [Solution](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) qui combine la puissance de la recherche et de QnA Maker. Consultez le [site de démonstration](https://aka.ms/qnaWithAzureSearchDemo). |
