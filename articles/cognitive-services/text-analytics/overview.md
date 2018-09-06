---
title: Vue d’ensemble d’Analyse de texte - Azure Cognitive Services | Microsoft Docs
description: Analyse de texte dans Azure Cognitive Services pour analyser les sentiments, extraire les expressions clés, détecter la langue et lier des entités.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 8/30/2018
ms.author: ashmaka
ms.openlocfilehash: 71af2bcbf58279681bbea66d4f76e951a2efce59
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341484"
---
# <a name="what-is-text-analytics"></a>Qu’est-ce que le service Analyse de texte ?

Le service Analyse de texte assure un traitement avancé du langage naturel pour le texte brut non structuré. Il comprend quatre fonctions principales : l’analyse des sentiments, l’extraction d’expression clés, la détection de la langue et la liaison d’entités.

## <a name="analyze-sentiment"></a>Analyser les sentiments

[Découvrez](how-tos/text-analytics-how-to-sentiment-analysis.md) ce que les clients pensent de votre marque ou thématique en analysant du texte brut pour trouver des indices sur les sentiments positifs ou négatifs. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif.<br />
Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](text-analytics-supported-languages.md), l’API peut analyser tout texte brut que vous fournissez et lui attribuer un score.

## <a name="extract-key-phrases"></a>Extraire des expressions clés

[Extrayez automatiquement les expressions clés](how-tos/text-analytics-how-to-keyword-extraction.md) pour identifier rapidement les points principaux. Par exemple, pour le texte d’entrée « The food was delicious and there were wonderful staff » (La nourriture était délicieuse et le personnel adorable), le service Analyse de texte retourne les principaux points de discussion : « food » (nourriture) et « wonderful staff » (personnel adorable).

## <a name="detect-language"></a>Détecter la langue

[Détectez](how-tos/text-analytics-how-to-language-detection.md) la langue (parmi 120) dans laquelle le texte d’entrée est rédigé et générez un code de langue unique pour chaque document envoyé dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse.

## <a name="identify-linked-entities-preview"></a>Identifier les entités liées (Préversion)

[Identifiez](how-tos/text-analytics-how-to-entity-linking.md) les entités bien connues dans votre texte et liez-les à des informations supplémentaires sur le web. La liaison d’entités reconnaît et lève l’ambiguïté quand un terme est utilisé comme l’une des entités, verbes et autres formes de mots, reconnaissables séparément.

## <a name="typical-workflow"></a>Flux de travail classique

Le flux de travail est simple : vous envoyez des données pour analyse et traitez les résultats dans votre code. Les analyseurs sont utilisés tels quels, sans configuration ou personnalisation supplémentaires.

1. [Inscrivez-vous](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour un [clé d’accès](how-tos/text-analytics-how-to-access-key.md). La clé doit être passée dans chaque demande.

2. [Créez une demande](how-tos/text-analytics-how-to-call-api.md#json-schema) en JSON qui contient vos données sous forme de texte brut non structuré.

3. Publiez la demande sur le point de terminaison établi pendant l’inscription en ajoutant l’API que vous voulez appeler : analyse des sentiments, extraction d’expressions clés, détection de langue ou identification d’entités.

4. Diffusez ou stockez la réponse localement. En fonction de la demande, les résultats sont un score de sentiment, une collection de phrases clés extraites ou un code de langue.

La sortie est renvoyée en tant que simple document JSON, avec des résultats pour chaque document texte que vous avez publié, en fonction de l’ID. Vous pouvez ensuite analyser, visualiser ou catégoriser les résultats en insights actionnables.

Les opérations effectuées par le service Analyse de texte sont sans état. Les données ne sont pas stockées dans votre compte.

<a name="data-limits"></a>

## <a name="specifications"></a>Spécifications

### <a name="supported-languages"></a>Langues prises en charge

Consultez [Langues prises en charge dans Analyse de texte](text-analytics-supported-languages.md).

### <a name="data-limits"></a>Limites de données

Tous les points de terminaison du service Analyse de texte acceptent des données en texte brut. La limite actuelle est de 5 000 caractères par document. Si vous avez besoin d’analyser des documents plus volumineux, vous pouvez les morceler en blocs plus petits. Si vous avez malgré tout besoin d’une limite supérieure, [contactez-nous](https://azure.microsoft.com/overview/sales-number/) afin que nous puissions en discuter.

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 000 caractères tels que mesurés par `String.Length`. |
| Taille maximale d’une demande | 1 Mo |
| Nombre maximal de documents par demande | 1 000 documents |

Le débit maximal est de 100 appels par minute. Notez que vous pouvez envoyer une grande quantité de documents dans un même appel (jusqu’à 1 000 documents).

### <a name="unicode-encoding"></a>Codage Unicode

Le service Analyse de texte utilise un codage Unicode pour la représentation textuelle et les calculs du nombre de caractères. Vous pouvez envoyer les demandes en UTF-8 ou UTF-16, sans aucune différence mesurable dans le nombre de caractères. Si vous utilisez `String.Length` pour obtenir le nombre de caractères, vous employez la même méthode que celle que nous utilisons pour mesurer la taille des données.

## <a name="next-steps"></a>Étapes suivantes

Commencez par essayer la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Vous pouvez coller une entrée de texte (5 000 caractères maximum) pour détecter la langue (parmi 120), calculer un score de sentiment, extraire des expressions clés ou identifier des entités liées. Aucune inscription n’est nécessaire.

Dès que vous êtes prêt à appeler directement le service Analyse de texte :

+ [Inscrivez-vous](how-tos/text-analytics-how-to-signup.md) pour obtenir une clé d’accès et examinez la procédure d’[appel de l’API](how-tos/text-analytics-how-to-call-api.md).

+ Le [démarrage rapide](quickstarts/csharp.md) est une procédure pas à pas relative aux appels d’API REST, écrite en C#. Découvrez comment envoyer du texte, choisir une analyse et afficher les résultats avec un minimum de code.

+ La [documentation de référence sur les API](//go.microsoft.com/fwlink/?LinkID=759346) fournit des informations techniques sur les API REST. La documentation prend en charge les appels incorporés, ce qui vous permet d’appeler l’API à partir de chaque page de documentation.

+ Le [Contenu externe et de la communauté](text-analytics-resource-external-community.md) fournit la liste des billets de blog et des vidéos montrant comment utiliser l’Analyse de texte avec d’autres outils et technologies.

## <a name="see-also"></a>Voir aussi

 [Page Documentation Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)
