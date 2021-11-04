---
title: Détecter les informations d’identification personnelle
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment extraire des informations d’identification personnelle et médicales du texte et comment détecter les informations identifiables.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 93f232233da0310881539441fdb296ac4ae39b62
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097763"
---
# <a name="how-to-detect-and-redact-personally-identifying-information-pii"></a>Comment détecter et masquer les informations d’identification personnelle

La fonctionnalité de détection des informations d’identification personnelle peut évaluer du texte non structuré, extraire des informations sensibles et médicales dans du texte sur plusieurs catégories prédéfinies.

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-pii-detection-model"></a>Spécifier le modèle de détection des informations d’identification personnelle

Par défaut, cette fonctionnalité utilise le dernier modèle IA disponible sur votre texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez est utilisé pour effectuer des opérations de reconnaissance d’entité nommée et sur les informations d'identification personnelle.

| Versions prises en charge | version la plus récente |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |

### <a name="input-languages"></a>Langues de saisie

Quand vous envoyez des documents à traiter, vous pouvez spécifier les [langues prises en charge](language-support.md) dans lesquelles ils sont écrits. Si vous ne le faites pas, l’extraction de phrases clés utilise l’anglais par défaut. L’API peut retourner des décalages dans la réponse pour prendre en charge différents [encodages multilingues et d’émoji](../concepts/multilingual-emoji-support.md). 

## <a name="submitting-data"></a>Envoi de données

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de demandes que vous pouvez envoyer par minute et par seconde, consultez la section sur les limites des données ci-dessous.

L’utilisation de la fonctionnalité de détection des informations d’identification personnelle de façon synchrone est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

Lors de l’utilisation asynchrone de ces fonctionnalités, les résultats de l’API sont disponibles pendant 48 heures après l’ingestion de la demande et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.

L’API tente de détecter les [catégories d’entité définies](concepts/entity-categories.md) pour une langue de document donnée. Si vous voulez spécifier les entités qui seront détectées et retournées, utilisez le paramètre facultatif `piiCategories` avec les catégories d’entités appropriées. Ce paramètre peut également vous permettre de détecter les entités qui ne sont pas activées par défaut pour la langue de votre document. L’exemple d’URL suivant va détecter un numéro de permis de conducteur français qui peut se trouver dans du texte en anglais ainsi que les entités en anglais par défaut.

> [!TIP]
> Si vous n’incluez pas `default` lors de la spécification des catégories d’entités, l’API retourne seulement les catégories d’entités que vous spécifiez.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=default,FRDriversLicenseNumber`

## <a name="getting-pii-results"></a>Obtention des résultats sur les informations d’identification personnelle

Lorsque vous recevez des résultats de la détection des informations d’identification personnelle, vous pouvez diffuser les résultats dans une application ou enregistrer la sortie dans un fichier sur le système local. La réponse de l’API comprend les [entités reconnues](concepts/entity-categories.md), y compris leurs catégories et sous-catégories, ainsi que les scores de confiance. La chaîne de texte avec les entités d’informations d’identification personnelle floutées est également retournée.

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
