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
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 9ba9fe7ca73e874fb55c228e22b884a86de736cf
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661457"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limites de données et de débit pour l’API Analyse de texte
<a name="data-limits"></a>

Découvrez dans cet article les limites de taille et de débit auxquelles vous êtes soumis pour l’envoi de données à l’API Analyse de texte. Notez que les prix ne sont pas affectés par les limites de données ou les limites du taux de transfert. Les prix sont soumis aux [détails des prix](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) de votre ressource Analyse de texte.

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). S’applique également à Analyse de texte pour la santé. |
| Taille maximale d’un document (point de terminaison `/analyze`)  | 125 000 caractères, mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Ne s’applique pas à Analyse de texte pour la santé. |
| Taille maximale d’une demande | 1 Mo. S’applique également à Analyse de texte pour la santé. |


Si un document dépasse la limite de caractères, l’API se comporte différemment en fonction du point de terminaison que vous utilisez :

* Point de terminaison `/analyze` :
  * L’API rejette l’intégralité de la demande et retourne une erreur `400 bad request` si un document qu’elle contient dépasse la taille maximale.
* Tous les autres points de terminaison :  
  * L’API ne traite pas un document qui dépasse la taille maximale et retourne une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

Le nombre maximal de documents que vous pouvez envoyer dans une même demande dépend de la version et de la fonctionnalité d’API que vous utilisez, qui sont décrites dans le tableau ci-dessous.

#### <a name="version-3"></a>[Version 3](#tab/version-3)

Les limites suivantes concernent pour l’API v3 actuelle. Le dépassement des limites ci-dessous aura pour effet de générer un code d’erreur HTTP 400.


| Fonctionnalité | Nombre maximal de documents par demande | 
|----------|-----------|
| Détection de la langue | 1 000 |
| Analyse des sentiments | 10 |
| Exploration des opinions | 10 |
| Extraction d’expressions clés | 10 |
| Reconnaissance d’entité nommée | 5 |
| Liaison d’entités | 5 |
| Analyse de texte pour la santé  | 10 pour l’API web, 1 000 pour le conteneur. |
| Point de terminaison d’analyse | 25 pour toutes les opérations. |

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

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Ces limites sont les mêmes pour les deux versions de l’API. Ces limites du taux de transfert ne s’appliquent pas à l’Analyse de texte pour le conteneur d’intégrité démuni de limite définie du taux d’intégrité.

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1 000                | 1 000                |

Les débits en demandes sont mesurées séparément pour chaque fonctionnalité d’Analyse de texte. Vous pouvez envoyer le nombre maximal de demandes correspondant à votre niveau tarifaire pour chaque fonctionnalité en même temps. Par exemple, si vous êtes dans le niveau `S` et que vous envoyez 1 000 demandes à la fois, vous ne pouvez pas envoyer une autre demande pendant 59 secondes.


## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que l’API Analyse de texte ?](../overview.md)
* [Détails de la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
