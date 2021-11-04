---
title: Résumé de texte avec l’API de résumé extractif
titleSuffix: Azure Cognitive Services
description: Cet article montre comment résumer du texte avec l’API de résumé extractif.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-summarization, ignite-fall-2021
ms.openlocfilehash: 21be719780b4633456ceea89c0cb6c6295480348
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098045"
---
# <a name="how-to-use-text-summarization-preview"></a>Guide pratique pour utiliser le résumé de texte (préversion)

> [!IMPORTANT] 
> La fonctionnalité de résumé extractif constitue une capacité en préversion fournie « EN L’ÉTAT » et « AVEC TOUS LES DÉFAUTS ». Par conséquent, elle ne doit pas être implémentée ni déployée dans le cadre d’une utilisation en production. Le client est seul responsable de tout usage du résumé extractif. 

En général, il existe deux approches pour le résumé automatique de texte : extractif et abstractif. Cette API fournit une fonctionnalité de résumé extractif,

qui permet de produire un résumé. Pour cela, elle extrait les phrases qui, ensemble, représentent les informations les plus importantes ou les plus pertinentes du contenu d’origine.

Cette fonctionnalité est conçue pour raccourcir le contenu que les utilisateurs considèrent comme trop long à lire. Le résumé extractif condense des articles, des publications ou des documents en phrases clés.

Les modèles IA utilisés par l’API sont fournis par le service ; vous n’avez qu’à envoyer le contenu à analyser.

## <a name="features"></a>Fonctionnalités

> [!TIP]
> Si vous souhaitez commencer à utiliser cette fonctionnalité, vous pouvez suivre l’[article de démarrage rapide](../quickstart.md). Vous pouvez également créer des exemples de demande à l’aide de [Language Studio](../../language-studio.md) sans avoir à écrire de code.

L’API de résumé extractif utilise des techniques de traitement automatique des langues pour localiser les phrases clés d’un document texte non structuré. Ces phrases transmettent collectivement l’idée principale du document.

Le résumé extractif renvoie un score de classement dans le cadre de la réponse du système, ainsi que les phrases extraites et leur position dans les documents d’origine. Un score de classement est un indicateur de la pertinence d’une phrase par rapport à l’idée principale d’un document. Le modèle attribue un score compris entre 0 et 1 (inclus) à chaque phrase et renvoie les phrases les mieux notées par requête. Par exemple, si vous demandez un résumé de trois phrases, le service renvoie les trois phrases les mieux notées.

Il existe une autre fonctionnalité d’Azure Cognitive Service for Language, [l’extraction de phrases clés](./../../key-phrase-extraction/how-to/call-api.md), qui permet d’extraire des informations essentielles. Pour choisir entre l’extraction de phrases clés et le résumé extractif, tenez compte des points suivants :
* L’extraction de phrases clés renvoie des expressions alors que le résumé extractif renvoie des phrases.
* Le résumé extractif renvoie des phrases avec un score de classement. Les phrases les mieux classées seront renvoyées par requête.

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-text-summarization-model"></a>Spécification du modèle de résumé de texte

Par défaut, le résumé de texte utilise le dernier modèle IA disponible sur le texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle spécifié sera utilisé pour effectuer des opérations de résumé de texte.

| Versions prises en charge | version la plus récente |
|--|--|
| `2021-08-01` | `2021-08-01` |

### <a name="input-languages"></a>Langues de saisie

Lorsque vous envoyez des documents à traiter par l’extraction de phrases clés, vous pouvez spécifier dans quelles [langues prises en charge](../language-support.md) ils sont écrits. À défaut, l’extraction de phrases clés prend par défaut la valeur Anglais. L’API peut retourner des décalages dans la réponse pour prendre en charge différents [encodages multilingues et d’émoji](../../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envoi de données

Les documents sont envoyés à l’API sous forme de chaînes de texte. L’analyse est effectuée à la réception de la demande. Étant donné que l’API est asynchrone, il peut y avoir un délai entre l’envoi d’une demande d’API et la réception des résultats.  Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez les limites de données ci-dessous.

Lorsque vous utilisez cette fonctionnalité, les résultats de l’API sont disponibles pendant 24 heures à partir du moment où la demande a été ingérée, et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.

Vous pouvez utiliser le paramètre `sentenceCount` pour spécifier le nombre de phrases à renvoyer, `3` étant la valeur par défaut. La plage est comprise entre 1 et 20.

Par ailleurs, le paramètre `sortby` permet de spécifier l’ordre dans lequel les phrases extraites sont retournées : `Offset` ou `Rank`, par défaut `Offset`. 


|Valeur du paramètre  |Description  |
|---------|---------|
|Rank    | Trie les phrases en fonction de leur pertinence pour le document d’entrée, déterminée par le service.        |
|Offset    | Conserve l’ordre d’origine dans lequel les phrases apparaissent dans le document d’entrée.        |

## <a name="getting-text-summarization-results"></a>Récupération des résultats du résumé de texte

Lorsque vous recevez des résultats de la détection de langue, vous pouvez diffuser les résultats dans une application ou enregistrer la sortie dans un fichier sur le système local.

Voici un exemple de contenu que vous pourriez soumettre pour résumé. Il est extrait de l’article de blog Microsoft [Une représentation holistique vers une IA intégrative](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/). Cet article ne constitue qu’un exemple. L’API peut accepter un texte d’entrée nettement plus long. Pour plus d’informations, consultez la section sur les limites de données.
 
*« Chez Microsoft, nous cherchons à faire progresser l’IA au-delà des techniques existantes, en adoptant une approche plus holistique et plus humaine de l’apprentissage et de la compréhension. En tant que directeur de la technologie d’Azure AI Cognitive Services, je travaille avec une équipe de scientifiques et d’ingénieurs extraordinaires pour faire de cette quête une réalité. Dans le cadre de mes fonctions, je bénéficie d’une perspective unique pour observer la relation entre trois attributs de la cognition humaine : le texte monolingue (X), les signaux sensoriels audio ou visuels (Y) et le texte multilingue (Z). La magie se produit à l’intersection de ces trois attributs : c’est ce que nous appelons le code XYZ, comme l’illustre la figure 1, une représentation conjointe permettant de créer une IA plus puissante, capable de mieux parler, entendre, voir et comprendre les humains. Nous pensons que le code XYZ nous permettra de réaliser notre vision à long terme : l’apprentissage de transfert interdomaines, couvrant différentes modalités et différentes langues. L’objectif est de disposer de modèles préentraînés capables d’apprendre conjointement des représentations pour prendre en charge un large éventail de tâches d’IA en aval, comme les humains le font aujourd’hui. Au cours des cinq dernières années, nous avons obtenu des résultats de l’ordre des performances humaines dans des tests d’évaluation portant sur la reconnaissance vocale du langage courant, la traduction automatique, les réponses aux questions du langage courant, la compréhension de la lecture automatique et le sous-titrage d’images. Ces cinq percées nous ont fourni des signaux forts en direction de notre aspiration plus ambitieuse : faire un bond en avant dans les capacités de l’IA, en réalisant un apprentissage multisensoriel et multilingue plus proche de la façon dont les humains apprennent et comprennent. Je pense que le code XYZ conjoint constitue un élément fondamental de cette aspiration, s’il est fondé sur des sources de connaissances externes dans les tâches d’IA en aval. »*

L’API de résumé extractif est exécutée à la réception de la demande en créant un travail pour le backend d’API. Si la tâche réussit, la sortie de l’API est retournée. Elle peut être récupérée pendant 24 heures, après quoi elle est vidée. En raison de la prise en charge multilingue et des émojis, la réponse peut contenir des décalages de texte. Pour plus d’informations, consultez le [guide pratique pour traiter les décalages](../../concepts/multilingual-emoji-support.md).

À partir de l’exemple ci-dessus, l’API pourrait retourner les phrases de résumé suivantes :

*« Chez Microsoft, nous cherchons à faire progresser l’IA au-delà des techniques existantes, en adoptant une approche plus holistique et plus humaine de l’apprentissage et de la compréhension. »*

*« Dans le cadre de mes fonctions, je bénéficie d’une perspective unique pour observer la relation entre trois attributs de la cognition humaine : le texte monolingue (X), les signaux sensoriels audio ou visuels (Y) et le texte multilingue (Z). »*

*« La magie se produit à l’intersection de ces trois attributs : c’est ce que nous appelons le code XYZ, comme l’illustre la figure 1, une représentation conjointe permettant de créer une IA plus puissante, capable de mieux parler, entendre, voir et comprendre les humains. »*


## <a name="data-limits"></a>Limites de données

Cette section décrit les limites de taille et les tarifs applicables à l’envoi de données à l’API Résumé de texte. Notez que les prix ne sont pas affectés par les limites de données ou les limites du taux de transfert. Les prix appliqués dépendent des [informations tarifaires](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) de votre ressource Langue.

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Nombre maximal de caractères par demande  | 125 000 caractères pour tous les documents envoyés, tels que mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Nombre maximal de documents par demande | 25 |

Si un document dépasse les limites de taille, l’API rejette l’intégralité de la demande et retourne une erreur `400 bad request`.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble de la synthèse du texte](../overview.md)
