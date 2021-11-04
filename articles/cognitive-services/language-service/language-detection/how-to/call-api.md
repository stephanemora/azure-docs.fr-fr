---
title: Guide pratique pour effectuer une détection de langue
titleSuffix: Azure Cognitive Services
description: Cet article montre comment détecter la langue du texte écrit à l’aide de la détection de langue.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 55584c58ebba3faf3d9517cf65827fd784d331a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096983"
---
# <a name="how-to-use-language-detection"></a>Guide pratique pour utiliser la détection de langue

La fonctionnalité Détection de langue peut évaluer du texte et retourner un identificateur de langue qui indique la langue dans laquelle un document a été écrit.

La détection de la langue est utile pour les magasins de contenu qui collectent du texte arbitraire dont la langue est inconnue. Vous pouvez analyser les résultats de cette analyse pour déterminer la langue utilisée dans le document d’entrée. La réponse retourne également un score compris entre 0 et 1 qui reflète la confiance du modèle.

La fonctionnalité Détection de langue peut détecter une grande variété de langues, de variantes, de dialectes, et certaines langues régionales ou de culture.

> [!TIP]
> Si vous souhaitez commencer à utiliser cette fonctionnalité, vous pouvez suivre l’[article de démarrage rapide](../quickstart.md) pour bien démarrer. Vous pouvez également créer des exemples de requêtes à l’aide de [Language Studio](../../language-studio.md) sans avoir à écrire de code.

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-language-detection-model"></a>Spécifier le modèle de détection de langue

Par défaut, la détection de langue utilise le dernier modèle d’IA disponible sur votre texte. Vous pouvez également configurer vos requêtes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez sera utilisé pour effectuer des opérations de détection de langue.

| Versions prises en charge | version la plus récente |
|--|--|
|  `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |

### <a name="input-languages"></a>Langues d’entrée

Lorsque vous soumettez des documents à évaluer, la détection de langue tente de déterminer si le texte a été écrit dans l’une des [langues prises en charge](../language-support.md).  

Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez essayer la fonctionnalité Détection de langue pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `unknown`.

## <a name="submitting-data"></a>Envoi de données

> [!TIP]
> Vous pouvez utiliser un [conteneur Docker](use-containers.md) pour la détection de langue, afin de pouvoir utiliser l’API localement.

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez les limites de données ci-dessous.

L’utilisation de la fonctionnalité de détection de langue de façon synchrone est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

Lors de l’utilisation de cette fonctionnalité de manière asynchrone, les résultats de l’API sont disponibles pendant 24 heures à partir du moment où la requête a été ingérée, et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.


## <a name="getting-language-detection-results"></a>Obtention des résultats de la détection de langue

Lorsque vous recevez des résultats de la détection de langue, vous pouvez diffuser les résultats dans une application ou enregistrer la sortie dans un fichier sur le système local.

La détection de langue retourne une langue prédominante pour chaque document soumis, ainsi que son nom [ISO 639-1](https://www.iso.org/standard/22109.html), un nom convivial et un score de confiance. Un score positif de 1 indique le niveau de confiance possible le plus haut de l’analyse.

### <a name="ambiguous-content"></a>Contenu ambigu

Dans certains cas, il peut être difficile de lever toute ambiguïté sur les langues en fonction de l’entrée. Vous pouvez utiliser le paramètre `countryHint` pour spécifier un code de pays/région [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). Par défaut, l’API utilise « US » comme indicateur de pays par défaut. Pour supprimer ce comportement, vous pouvez réinitialiser ce paramètre en définissant cette valeur sur une chaîne vide `countryHint = ""`.

Par exemple, « impossible » est commun à l’anglais et au français ; si le contexte dans lequel il est utilisé est limité, la réponse sera basée sur l’indicateur de pays/région « US ». Si l’origine du texte est connue et qu’il provient de France, cette information peut être fournie par l’indicateur.

**Input**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

Le modèle de détection de langue dispose désormais d’un contexte supplémentaire pour améliorer le jugement : 

**Sortie**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

Si l’analyseur ne parvient pas à analyser l’entrée, il retourne `(Unknown)`. Cela se produit par exemple si vous envoyez une chaîne de texte composée uniquement de nombres.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": 0.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

### <a name="mixed-language-content"></a>Contenu en plusieurs langues

Un contenu en plusieurs langues dans un document retourne la langue la plus représentée dans le contenu, mais avec un score positif inférieur. Le score reflète la puissance marginale de cette évaluation. Dans l’exemple suivant, l’entrée est un mélange d’anglais, d’espagnol et de français. L’analyseur compte les caractères dans chaque segment afin de déterminer la langue prédominante.

**Input**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**Sortie**

Le résultat obtenu se compose de la langue prédominante, avec un score inférieur à 1,0, qui indique un faible niveau de confiance.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 0.88
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale d’un document (synchrone) | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Nombre maximal de caractères par demande (asynchrone)  | 125 000 caractères pour tous les documents envoyés, tels que mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo.  |
| Nombre maximal de documents par demande | 1 000 |

Si un document dépasse la limite de caractères, l’API se comporte différemment en fonction du point de terminaison que vous utilisez :

* Asynchrone : l’API rejette l’intégralité de la requête et retourne une erreur `400 bad request` si un document qu’elle contient dépasse la taille maximale.
* Synchrone : l’API ne traite pas un document qui dépasse la taille maximale et retourne une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble de la détection de langue](../overview.md)
