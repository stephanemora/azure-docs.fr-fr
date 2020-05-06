---
title: Qu’est-ce que l’API Analyse de texte ? - Capacités -
titleSuffix: Azure Cognitive Services
description: Utiliser l’API Text Analytics dans Azure Cognitive Services pour analyser les sentiments, extraire les expressions clés, détecter la langue et reconnaître des entités.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78395732"
---
# <a name="what-is-the-text-analytics-api"></a>Qu’est-ce que l’API Analyse de texte ?

L’API Analyse de texte est un service cloud qui fournit un traitement en langage naturel avancé de texte brut. Elle inclut quatre fonctions principales : analyse des sentiments, extraction de phrases clés, détection de la langue et reconnaissance des entités nommées.

L’API fait partie d’[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), une collection d’algorithmes de machine learning et d’intelligence artificielle dans le cloud pour vos projets de développement.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

L’expression Text Analytics peut signifier différentes choses mais, dans Cognitive Services, l’API Text Analytics fournit quatre types d’analyses, comme décrit ci-dessous. Vous pouvez utiliser ces fonctionnalités avec l’[API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) ou la [bibliothèque de client](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Analyse des sentiments
Utilisez l’[analyse des sentiments](how-tos/text-analytics-how-to-sentiment-analysis.md) pour découvrir ce que les clients pensent de votre marque ou de votre thématique en analysant du texte brut pour trouver des indices sur les sentiments positifs ou négatifs. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif.<br /> Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](text-analytics-supported-languages.md), l’API peut analyser tout texte brut que vous fournissez, lui attribuer un score, et renvoyer directement les résultats à l’application appelante.

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés
[Extrayez automatiquement les expressions clés](how-tos/text-analytics-how-to-keyword-extraction.md) pour identifier rapidement les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ».

## <a name="language-detection"></a>Détection de la langue
Vous pouvez [détecter la langue dans laquelle le texte d’entrée est rédigé](how-tos/text-analytics-how-to-language-detection.md) et générer un code de langue unique pour chaque document envoyé dans la requête, ceci parmi une grande variété de langues, de variantes, de dialectes, et de quelques langues régionales ou de culture. Le code de langue est associé à un score indiquant la puissance de l’analyse.

## <a name="named-entity-recognition"></a>Reconnaissance d’entité nommée
[Identifiez et classez les entités](how-tos/text-analytics-how-to-entity-linking.md) dans votre texte en tant que personnes, lieux, organisations, date/heure, quantités, pourcentages, devises et bien plus encore. Les entités bien connues sont également reconnues et liées pour des informations supplémentaires sur le web.

## <a name="use-containers"></a>Utiliser des conteneurs

[Utilisez les conteneurs Text Analytics](how-tos/text-analytics-how-to-install-containers.md) pour extraire les phrases clés, détecter la langue et analyser les sentiments localement, en installant des conteneurs Docker normalisés plus près de vos données.

## <a name="typical-workflow"></a>Flux de travail classique

Le flux de travail est simple : vous envoyez des données pour analyse et traitez les résultats dans votre code. Les analyseurs sont utilisés tels quels, sans configuration ou personnalisation supplémentaires.

1. [Créez une ressource Azure](../cognitive-services-apis-create-account.md) pour Text Analytics. Ensuite, [récupérez la clé](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) générée pour authentifier vos demandes.

2. [Formulez une demande](how-tos/text-analytics-how-to-call-api.md#json-schema) contenant vos données sous forme de texte brut non structuré en JSON.

3. Publiez la demande sur le point de terminaison établi pendant l’inscription, en ajoutant la ressource souhaitée : analyse des sentiments, extraction de phrases clés, détection de la langue ou reconnaissance des entités nommées.

4. Diffusez ou stockez la réponse localement. En fonction de la demande, les résultats sont un score de sentiment, une collection de phrases clés extraites ou un code de langue.

La sortie est renvoyée en tant que simple document JSON, avec des résultats pour chaque document texte que vous avez publié, en fonction de l’ID. Vous pouvez ensuite analyser, visualiser ou catégoriser les résultats en informations exploitables.

Les données ne sont pas stockées dans votre compte. Les opérations effectuées par l’API Text Analytics sont sans état, ce qui signifie que le texte que vous fournissez est traité et que les résultats sont renvoyés immédiatement.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics pour plusieurs niveaux d’expérience en programmation

Vous pouvez commencer à utiliser l’API Text Analytics dans votre processus, même si vous n’avez pas beaucoup d’expérience en programmation. Utilisez ces tutoriels pour savoir comment recourir à l’API pour analyser un texte de différentes façons en fonction de votre niveau d’expérience. 

* Expérience de programmation minimale requise :
    * [Extraire des informations dans Excel avec Analyse de texte et Power Automate](tutorials/extract-excel-information.md)
    * [Utiliser l’API Text Analytics et MS Flow pour identifier les sentiments des commentaires dans un groupe Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Intégrer Power BI à l’API Text Analytics pour analyser les commentaires des clients](tutorials/tutorial-power-bi-key-phrases.md)
* Expérience de programmation recommandée :
    * [Analyse des sentiments sur des données de streaming à l’aide d’Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Créer une application Flask pour la traduction de texte, l’analyse de sentiments et la synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Langues prises en charge

Cette section a été déplacée vers un article distinct pour une meilleure détectabilité. Pour accéder à ce contenu, voir [Langues prises en charge dans l’API Text Analytics](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de données

Tous les points de terminaison de l’API Analyse de texte acceptent des données en texte brut. La limite actuelle est de 5 120 caractères par document. Si vous avez besoin d’analyser des documents plus volumineux, vous pouvez les morceler en blocs plus petits.

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 120 caractères, tels que mesurés par [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo |
| Nombre maximal de documents par demande | 1 000 documents |

Votre limite de débit varie selon votre niveau tarifaire.

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1 000                | 1 000                |

Les requêtes sont mesurées séparément pour chaque fonctionnalité de Text Analytics. Par exemple, vous pouvez envoyer le nombre maximal de requêtes pour votre niveau tarifaire pour chaque fonctionnalité en même temps.      

## <a name="unicode-encoding"></a>Codage Unicode

L’API Text Analytics utilise un codage Unicode pour la représentation textuelle et les calculs de nombre de caractères. Les demandes peuvent être soumises en code UTF-8 ou UTF-16, sans différence mesurable de nombre de caractères. Des points de code Unicode sont utilisés en guise d’heuristique pour la longueur de caractères, et sont considérés comme équivalents en ce qui concerne les limites de données pour Text Analytics. Si vous utilisez [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) pour obtenir le nombre de caractères, vous recourez à la même méthode que nous pour mesurer la taille des données.

## <a name="next-steps"></a>Étapes suivantes

+ [Créez une ressource Azure](../cognitive-services-apis-create-account.md) pour Text Analytics afin d’obtenir une clé et un point de terminaison pour vos applications.

+ Utilisez le [démarrage rapide](quickstarts/text-analytics-sdk.md) pour commencer à envoyer des appels d’API. Découvrez comment envoyer du texte, choisir une analyse et afficher les résultats avec un minimum de code.

+ Pour plus d’informations sur les nouvelles mises en production et fonctionnalités, voir [Nouveautés de l’API Text Analytics](whats-new.md).

+ Allez un peu plus loin avec ce [tutoriel d’analyse de sentiments](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) à l’aide d’Azure Databricks.

+ Découvrez notre liste de billets de blog et de vidéos montrant comment utiliser l’API Text Analytics avec d’autres outils et technologies dans notre page [Contenu externe et de la communauté](text-analytics-resource-external-community.md).
