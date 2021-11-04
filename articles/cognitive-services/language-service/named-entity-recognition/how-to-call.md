---
title: Comment effectuer une reconnaissance d’entité nommée (NER)
titleSuffix: Azure Cognitive Services
description: Cet article vous explique comment extraire des entités nommées à partir du texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: cd6901e4ac1650f6739d24697fab5abafe8409a5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097962"
---
# <a name="how-to-use-named-entity-recognitionner"></a>Comment utiliser Reconnaissance d’entité nommée (NER)

La fonctionnalité NER peut évaluer du texte non structuré et extraire des entités nommées à partir de texte dans plusieurs catégories prédéfinies, par exemple : personne, localisation, événement, produit et organisation.  

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-ner-model"></a>Spécifier le modèle NER

Par défaut, cette fonctionnalité utilise le dernier modèle IA disponible sur votre texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez est utilisé pour effectuer des opérations NER.

| Versions prises en charge | version la plus récente |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |


### <a name="input-languages"></a>Langues de saisie

Quand vous envoyez des documents à traiter, vous pouvez spécifier les [langues prises en charge](language-support.md) dans lesquelles ils sont écrits. Si vous ne le faites pas, l’extraction de phrases clés utilise l’anglais par défaut. L’API peut retourner des décalages dans la réponse pour prendre en charge différents [encodages multilingues et d’émoji](../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envoi de données

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de demandes que vous pouvez envoyer par minute et par seconde, consultez la section sur les limites des données ci-dessous.

L’utilisation de la fonctionnalité NER de façon synchrone est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

Lors de l’utilisation asynchrone de ces fonctionnalités, les résultats de l’API sont disponibles pendant 24 heures après l’ingestion de la demande et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.

L’API tente de détecter les [catégories d’entité définies](concepts/named-entity-categories.md) pour une langue de document donnée. 

## <a name="getting-ner-results"></a>Obtention des résultats NER

Quand vous recevez des résultats de NER, vous pouvez les diffuser en streaming dans une application ou enregistrer la sortie dans un fichier sur le système local. La réponse de l’API comprend les [entités reconnues](concepts/named-entity-categories.md), y compris leurs catégories et sous-catégories, ainsi que les scores de confiance. 

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale d’un document (synchrone) | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Nombre maximal de caractères par demande (asynchrone)  | 125 000 caractères pour tous les documents envoyés, tels que mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements).  |
| Taille maximale d’une demande | 1 Mo. |
| Nombre maximal de documents par demande | 5 |

Si un document dépasse la limite de caractères, l’API se comporte différemment suivant la fonctionnalité que vous utilisez :

* Asynchrones :
  * L’API rejette l’intégralité de la demande et retourne une erreur `400 bad request` si un document qu’elle contient dépasse la taille maximale.
* Synchrone :  
  * L’API ne traite pas un document qui dépasse la taille maximale et retourne une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

Si vous dépassez le nombre maximal de documents que vous pouvez envoyer dans une seule demande, un code d’erreur HTTP 400 est généré.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Reconnaissance d’entité nommée](overview.md)
