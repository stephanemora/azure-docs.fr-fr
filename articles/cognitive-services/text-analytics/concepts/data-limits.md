---
title: Limites de données pour l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Limitations de données pour l’API Analyse de texte à partir d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 79a12505ccc7cea709a88818513ba95710989954
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142365"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limites de données et de débit pour l’API Analyse de texte
<a name="data-limits"></a>

Découvrez dans cet article les limites de taille et de débit auxquelles vous êtes soumis pour l’envoi de données à l’API Analyse de texte. 

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo |

Le nombre maximal de documents que vous pouvez envoyer dans une même demande dépend de la version et de la fonctionnalité d’API que vous utilisez.

#### <a name="version-3"></a>[Version 3](#tab/version-3)

> [!NOTE]
> Si votre demande dépasse les limites de l’API v3, mais pas celles de l’API v2, un avertissement est retourné dans la réponse de l’API. Depuis le 15/07/2020, un code d’erreur 400 est retourné à la place. 

Les limites suivantes ont évolué dans l’API v3. Si vous dépassez les limites ci-dessous, un avertissement est généré dans la réponse de l’API.


| Fonctionnalité | Nombre maximal de documents par demande | 
|----------|-----------|
| Détection de la langue | 1 000 |
| Analyse des sentiments | 10 |
| Extraction d’expressions clés | 10 |
| Reconnaissance d’entité nommée | 5 |
| Liaison d’entités | 5 |

#### <a name="version-2"></a>[Version 2](#tab/version-2)

| Fonctionnalité | Nombre maximal de documents par demande | 
|----------|-----------|
| Détection de la langue | 1 000 |
| Analyse des sentiments | 1 000 |
| Extraction d’expressions clés | 1 000 |
| Reconnaissance d’entité nommée | 1 000 |
| Liaison d’entités | 1 000 |

---

## <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Ces limites sont les mêmes pour les deux versions de l’API.

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1 000                | 1 000                |

Les requêtes sont mesurées séparément pour chaque fonctionnalité de Text Analytics. Par exemple, vous pouvez envoyer le nombre maximal de requêtes pour votre niveau tarifaire pour chaque fonctionnalité en même temps.  


## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que l’API Analyse de texte ?](../overview.md)
* [Détails de la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
