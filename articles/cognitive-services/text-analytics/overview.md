---
title: Qu’est-ce que l’API Analyse de texte ? - Capacités -
titleSuffix: Azure Cognitive Services
description: Utiliser l’API Analyse de texte dans Azure Cognitive Services pour analyser les sentiments, extraire les expressions clés, détecter la langue et reconnaître des entités.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: ee4551f6a31436ef2322fcea3a0c479b45036993
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697617"
---
# <a name="what-is-the-text-analytics-api"></a>Qu’est-ce que l’API Analyse de texte ?

L’API Analyse de texte est un service cloud qui fournit un traitement en langage naturel avancé de texte brut. Elle inclut trois fonctions principales : analyse des sentiments, extraction de phrases clés, détection de la langue et la reconnaissance d’entités.

L’API fait partie d’[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), une collection d’algorithmes de machine learning et d’intelligence artificielle dans le cloud pour vos projets de développement.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

L’analyse de texte peut signifier différentes choses mais, dans Cognitive Services, l’API Analyse de texte fournit quatre types d’analyses, comme décrit ci-dessous. Vous pouvez utiliser ces fonctionnalités avec l' [API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)REST ou une bibliothèque de client pour [.NET](quickstarts/csharp.md), [Python](quickstarts/python-sdk.md), [Node. js](quickstarts/nodejs-sdk.md), [Go](quickstarts/go-sdk.md), ou [Ruby](quickstarts/ruby-sdk.md).

## <a name="sentiment-analysis"></a>Analyse des sentiments
Utilisez l’[analyse des sentiments](how-tos/text-analytics-how-to-sentiment-analysis.md) pour découvrir ce que les clients pensent de votre marque ou de votre thématique en analysant du texte brut pour trouver des indices sur les sentiments positifs ou négatifs. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif.<br /> Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](text-analytics-supported-languages.md), l’API peut analyser tout texte brut que vous fournissez, lui attribuer un score, et renvoyer directement les résultats à l’application appelante.

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés
[Extrayez automatiquement les expressions clés](how-tos/text-analytics-how-to-keyword-extraction.md) pour identifier rapidement les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ».

## <a name="language-detection"></a>Détection de la langue
Vous pouvez [détecter la langue dans laquelle le texte d’entrée est rédigé](how-tos/text-analytics-how-to-language-detection.md) et générer un code de langue unique pour chaque document envoyé dans la requête, ceci parmi une grande variété de langues, de variantes, de dialectes, et de quelques langues régionales ou de culture. Le code de langue est associé à un score indiquant la puissance de l’analyse.

## <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée
[Identifiez et classez les entités](how-tos/text-analytics-how-to-entity-linking.md) dans votre texte en tant que personnes, lieux, organisations, date/heure, quantités, pourcentages, devises et bien plus encore. Les entités bien connues sont également reconnues et liées pour des informations supplémentaires sur le web.

## <a name="use-containers"></a>Utiliser des conteneurs

[Utilisez les conteneurs Analyse de texte](how-tos/text-analytics-how-to-install-containers.md) pour extraire les phrases clés, détecter la langue et analyser les sentiments localement, en installant des conteneurs Docker normalisés plus près de vos données.

## <a name="typical-workflow"></a>Flux de travail classique

Le flux de travail est simple : vous envoyez des données pour analyse et traitez les résultats dans votre code. Les analyseurs sont utilisés tels quels, sans configuration ou personnalisation supplémentaires.

1. [Créez une ressource Azure](../cognitive-services-apis-create-account.md) pour Analyse de texte. Ensuite, [récupérez la clé](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) générée pour authentifier vos demandes.

2. [Formulez une demande](how-tos/text-analytics-how-to-call-api.md#json-schema) contenant vos données sous forme de texte brut non structuré en JSON.

3. Publiez la demande sur le point de terminaison établi pendant l’inscription, en ajoutant la ressource souhaitée : analyse des sentiments, extraction de phrases clés, détection de la langue ou identification d’entité.

4. Diffusez ou stockez la réponse localement. En fonction de la demande, les résultats sont un score de sentiment, une collection de phrases clés extraites ou un code de langue.

La sortie est renvoyée en tant que simple document JSON, avec des résultats pour chaque document texte que vous avez publié, en fonction de l’ID. Vous pouvez ensuite analyser, visualiser ou catégoriser les résultats en informations exploitables.

Les données ne sont pas stockées dans votre compte. Les opérations effectuées par l’API Analyse de texte sont sans état, ce qui signifie que le texte que vous fournissez est traité et que les résultats sont renvoyés immédiatement.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analyse de texte pour plusieurs niveaux d’expérience en programmation

Vous pouvez commencer à utiliser l’API Analyse de texte dans votre processus, même si vous n’avez pas beaucoup d’expérience en programmation. Utilisez ces tutoriels pour savoir comment recourir à l’API pour analyser un texte de différentes façons en fonction de votre niveau d’expérience. 

* Expérience de programmation minimale requise :
    * [Utiliser l’API Analyse de texte et MS Flow pour identifier les sentiments des commentaires dans un groupe Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Intégrer Power BI à l’API Analyse de texte pour analyser les commentaires des clients](tutorials/tutorial-power-bi-key-phrases.md)
* Expérience de programmation recommandée :
    * [Analyse des sentiments sur des données de streaming à l’aide d’Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Créer une application Flask pour la traduction de texte, l’analyse de sentiments et la synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Langues prises en charge

Cette section a été déplacée vers un article distinct pour une meilleure détectabilité. Pour accéder à ce contenu, voir [Langues prises en charge dans l’API Analyse de texte](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de données

Tous les points de terminaison de l’API Analyse de texte acceptent des données en texte brut. La limite actuelle est de 5 120 caractères par document. Si vous avez besoin d’analyser des documents plus volumineux, vous pouvez les morceler en blocs plus petits. Si vous avez malgré tout besoin d’une limite supérieure, [contactez-nous](https://azure.microsoft.com/overview/sales-number/) afin que nous puissions en discuter.

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 120 caractères, tels que mesurés par [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo |
| Nombre maximal de documents par demande | 1 000 documents |

Votre limite de débit varie selon votre niveau tarifaire.

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| Multiservice | 1 000                | 1 000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1 000                | 1 000                |

Les requêtes sont mesurées séparément pour chaque fonctionnalité d’Analyse de texte. Par exemple, vous pouvez envoyer le nombre maximal de requêtes pour votre niveau tarifaire pour chaque fonctionnalité en même temps.      

## <a name="unicode-encoding"></a>Codage Unicode

L’API Analyse de texte utilise un codage Unicode pour la représentation textuelle et les calculs de nombre de caractères. Les demandes peuvent être soumises en code UTF-8 ou UTF-16, sans différence mesurable de nombre de caractères. Des points de code Unicode sont utilisés en guise d’heuristique pour la longueur de caractères, et sont considérés comme équivalents en ce qui concerne les limites de données pour l’analyse de texte. Si vous utilisez [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) pour obtenir le nombre de caractères, vous recourez à la même méthode que nous pour mesurer la taille des données.

## <a name="next-steps"></a>Étapes suivantes

+ [Créez une ressource Azure](../cognitive-services-apis-create-account.md) pour Analyse de texte afin d’obtenir une clé et un point de terminaison pour vos applications.

+ Le [démarrage rapide](quickstarts/csharp.md) est une procédure pas à pas relative aux appels d’API REST, écrite en C#. Découvrez comment envoyer du texte, choisir une analyse et afficher les résultats avec un minimum de code. Si vous préférez, vous pouvez commencer par le [démarrage rapide Python](quickstarts/python.md).

+ Pour plus d’informations sur les nouvelles mises en production et fonctionnalités, voir [Nouveautés de l’API Analyse de texte](whats-new.md).

+ Allez un peu plus loin avec ce [tutoriel d’analyse de sentiments](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) à l’aide d’Azure Databricks.

+ Découvrez notre liste de billets de blog et de vidéos montrant comment utiliser l’API Analyse de texte avec d’autres outils et technologies dans notre page [Contenu externe et de la communauté](text-analytics-resource-external-community.md).
