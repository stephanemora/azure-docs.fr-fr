---
title: Qu’est-ce que le service Analyse de texte ?
titleSuffix: Azure Cognitive Services
description: Analyse de texte dans Azure Cognitive Services pour analyser les sentiments, extraire les expressions clés, détecter la langue et lier des entités.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 0de4e0d750d8ae3061ed0b80d706dec545338a90
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242924"
---
# <a name="what-is-text-analytics"></a>Qu’est-ce que le service Analyse de texte ?

L’API Analyse de texte est un service cloud qui fournit un traitement en langage naturel avancé de texte brut. Elle inclut trois fonctions principales : analyse des sentiments, extraction de phrases clés, détection de la langue et liaison d’entités.

L’API fait partie d’[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), une collection d’algorithmes de machine learning et d’intelligence artificielle dans le cloud pour vos projets de développement.

## <a name="capabilities-in-text-analytics"></a>Fonctionnalités d’Analyse de texte

L’analyse de texte peut signifier différentes choses mais, dans Cognitive Services, l’API Analyse de texte fournit quatre types d’analyses, comme décrit dans le tableau suivant.

| Opérations| Description | API |
|-----------|-------------|------|
|[**Analyse des sentiments**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Découvrez ce que les clients pensent de votre marque ou thématique en analysant du texte brut pour trouver des indices sur les sentiments positifs ou négatifs. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif.<br /> Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](text-analytics-supported-languages.md), l’API peut analyser tout texte brut que vous fournissez, lui attribuer un score, et renvoyer directement les résultats à l’application appelante. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**extraction de phrases clés**](how-tos/text-analytics-how-to-keyword-extraction.md) | Extrayez automatiquement des phrases clés pour identifier rapidement les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ».  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Détection de la langue**](how-tos/text-analytics-how-to-language-detection.md) | Détectez parmi jusqu’à 120 langues celle dans laquelle le texte d’entrée est rédigé, et générez un code de langue unique pour chaque document soumis dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Reconnaissance d’entité (préversion)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifiez et classez les entités dans votre texte en tant que personnes, lieux, organisations, date/heure, quantités, pourcentages, devises et bien plus encore. Les entités bien connues sont également reconnues et liées pour des informations supplémentaires sur le web. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) |

## <a name="use-containers"></a>Utiliser des conteneurs

[Utilisez les conteneurs Analyse de texte](how-tos/text-analytics-how-to-install-containers.md) pour extraire les phrases clés, détecter la langue et analyser les sentiments localement, en installant des conteneurs Docker normalisés plus près de vos données.

## <a name="typical-workflow"></a>Flux de travail classique

Le flux de travail est simple : vous envoyez des données pour analyse et traitez les résultats dans votre code. Les analyseurs sont utilisés tels quels, sans configuration ou personnalisation supplémentaires.

1. [Inscrivez-vous](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour un [clé d’accès](how-tos/text-analytics-how-to-access-key.md). La clé doit être passée dans chaque demande.

2. [Formulez une demande](how-tos/text-analytics-how-to-call-api.md#json-schema) contenant vos données sous forme de texte brut non structuré en JSON.

3. Publiez la demande sur le point de terminaison établi pendant l’inscription, en ajoutant la ressource souhaitée : analyse des sentiments, extraction de phrases clés, détection de la langue ou identification d’entité.

4. Diffusez ou stockez la réponse localement. En fonction de la demande, les résultats sont un score de sentiment, une collection de phrases clés extraites ou un code de langue.

La sortie est renvoyée en tant que simple document JSON, avec des résultats pour chaque document texte que vous avez publié, en fonction de l’ID. Vous pouvez ensuite analyser, visualiser ou catégoriser les résultats en informations exploitables.

Les données ne sont pas stockées dans votre compte. Les opérations effectuées par l’API Analyse de texte sont sans état, ce qui signifie que le texte que vous fournissez est traité et que les résultats sont renvoyés immédiatement.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Langues prises en charge

Cette section a été déplacée vers un article distinct pour une meilleure détectabilité. Pour accéder à ce contenu, voir [Langues prises en charge dans l’API Analyse de texte](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de données

Tous les points de terminaison de l’API Analyse de texte acceptent des données en texte brut. La limite actuelle est de 5 000 caractères par document. Si vous avez besoin d’analyser des documents plus volumineux, vous pouvez les morceler en blocs plus petits. Si vous avez malgré tout besoin d’une limite supérieure, [contactez-nous](https://azure.microsoft.com/overview/sales-number/) afin que nous puissions en discuter.

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 000 caractères, tels que mesurés par [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo |
| Nombre maximal de documents par demande | 1 000 documents |

Le débit maximal est de 100 appels par minute. Notez que vous pouvez envoyer une grande quantité de documents dans un seul appel (jusqu’à 1 000 documents).

## <a name="unicode-encoding"></a>Codage Unicode

L’API Analyse de texte utilise un codage Unicode pour la représentation textuelle et les calculs de nombre de caractères. Les demandes peuvent être soumises en code UTF-8 ou UTF-16, sans différence mesurable de nombre de caractères. Des points de code Unicode sont utilisés en guise d’heuristique pour la longueur de caractères, et sont considérés comme équivalents en ce qui concerne les limites de données pour l’analyse de texte. Si vous utilisez [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) pour obtenir le nombre de caractères, vous recourez à la même méthode que nous pour mesurer la taille des données.

## <a name="next-steps"></a>Étapes suivantes

Commencez par essayer la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Vous pouvez coller une entrée de texte (5 000 caractères maximum) pour détecter la langue (parmi jusqu’à 120 langues), calculer un score de sentiment ou extraire des phrases clés. Aucune inscription n’est nécessaire.

Lorsque vous êtes prêt à appeler l’API directement :

+ [Inscrivez-vous](how-tos/text-analytics-how-to-signup.md) pour obtenir une clé d’accès et examinez la procédure d’[appel de l’API](how-tos/text-analytics-how-to-call-api.md).

+ Le [démarrage rapide](quickstarts/csharp.md) est une procédure pas à pas relative aux appels d’API REST, écrite en C#. Découvrez comment envoyer du texte, choisir une analyse et afficher les résultats avec un minimum de code.

+ La [documentation de référence de l’API](//go.microsoft.com/fwlink/?LinkID=759346) fournit des informations techniques sur l’API. La documentation prenant en charge les appels incorporés, vous pouvez appeler l’API à partir de chaque page.

+ Le [Contenu externe et de la communauté](text-analytics-resource-external-community.md) fournit la liste des billets de blog et des vidéos montrant comment utiliser l’Analyse de texte avec d’autres outils et technologies.

## <a name="see-also"></a>Voir aussi

 [Page Documentation Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)
