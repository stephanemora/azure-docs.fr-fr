---
title: Comment appeler l’API Extraction de phrases clés
titleSuffix: Azure Cognitive Services
description: Comment extraire des phrases clés à l’aide de l’API Extraction de phrases clés.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
ms.openlocfilehash: 72a7d077b5ee7752f7b91a2f5db5e1b5614c4ddd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096894"
---
# <a name="how-to-use-key-phrase-extraction"></a>Comment utiliser l’extraction de phrases clés 

La fonctionnalité d’extraction de phrases clés peut évaluer un texte non structuré puis, pour chaque document, retourner une liste de phrases clés.

Cette fonctionnalité est utile si vous avez besoin d’identifier rapidement les principaux points d’une collection de documents. Par exemple, pour le texte d’entrée « *The food was delicious and the staff was wonderful* » (La nourriture était délicieuse et le personnel adorable), le service retourne les principaux sujets : « *food* » (nourriture) et « *wonderful staff* » (personnel adorable).

> [!TIP]
> Si vous souhaitez commencer à utiliser cette fonctionnalité, vous pouvez suivre l’[article de démarrage rapide](../quickstart.md). Vous pouvez également créer des exemples de requêtes à l’aide de [Language Studio](../../language-studio.md) sans avoir à écrire de code.


## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-key-phrase-extraction-model"></a>Spécifier le modèle d’extraction de phrases clés

Par défaut, l’extraction de phrases clés utilise le dernier modèle d’IA disponible sur votre texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez est utilisé pour effectuer des opérations d’extraction de phrases clés.

| Versions prises en charge | version la plus récente |
|--|--|
| `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |

### <a name="input-languages"></a>Langues de saisie

Lorsque vous envoyez des documents à traiter par l’extraction de phrases clés, vous pouvez spécifier dans quelles [langues prises en charge](../language-support.md) ils sont écrits. À défaut, l’extraction de phrases clés prend par défaut la valeur Anglais. L’API peut retourner des décalages dans la réponse pour prendre en charge différents [encodages multilingues et d’émoji](../../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envoi de données

L’extraction de phrases clés fonctionne mieux avec une plus grande quantité de texte. Contrairement à l’analyse des sentiments, qui fonctionne mieux sur des petites quantités de texte. Pour obtenir des résultats optimaux pour ces deux opérations, envisagez de restructurer les entrées en conséquence.

Pour envoyer une demande d’API, vous avez besoin du point de terminaison et de la clé de votre ressource de langue.

> [!NOTE]
> Vous trouverez la clé et le point de terminaison de votre ressource Language dans le portail Azure. Ils sont dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez les limites de données ci-dessous.

L’utilisation de la fonctionnalité d’extraction de phrases clés de façon synchrone est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

Lors de l’utilisation de cette fonctionnalité de manière asynchrone, les résultats de l’API sont disponibles pendant 24 heures à partir du moment où la requête a été ingérée, et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.


## <a name="getting-key-phrase-extraction-results"></a>Obtention des résultats d’extraction de phrases clés

Lorsque vous recevez des résultats de l’API, l’ordre des phrases clés retournées est déterminé en interne par le modèle. Vous pouvez streamer les résultats vers une application ou enregistrer la sortie dans un fichier sur le système local.

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur | 
|------------------------|---------------|
| Taille maximale d’un document (synchrone) | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Nombre maximal de caractères par demande (asynchrone) | 125 000 caractères pour tous les documents envoyés, tels que mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo.  |
| Nombre maximal de documents par demande |  10 |

Si un document dépasse la limite de caractères, l’API se comporte différemment en fonction du point de terminaison que vous utilisez :

* Asynchrone : l’API rejette l’intégralité de la requête et retourne une erreur `400 bad request` si un document qu’elle contient dépasse la taille maximale.
* Synchrone : l’API ne traite pas un document qui dépasse la taille maximale et retourne une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |

## <a name="next-steps"></a>Étapes suivantes

[Présentation de l’extraction de phrases clés](../overview.md)
