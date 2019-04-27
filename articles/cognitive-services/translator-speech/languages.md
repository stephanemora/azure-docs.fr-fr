---
title: Langues prises en charge - API de traduction de conversation Translator Speech
titlesuffix: Azure Cognitive Services
description: Découvrez les langues prises en charge par l’API de traduction de conversation Translator Speech.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 63592a0afc7e5da0a37c25c226b92b587aa5f886
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827513"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Langues prises en charge par l’API de traduction de conversation Translator Speech

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Les langues prises en charge pour la traduction vocale sont les suivantes. Si les deux langues sont prises en charge pour la traduction vocale, la traduction de parole en parole ou de parole en texte est disponible. Si la langue cible n’est pas prise en charge pour la traduction vocale, seule la traduction de parole en texte est disponible.

| Langue de synthèse vocale    |
|:----------- |
| Arabe (standard moderne)      |
| Chinois (mandarin)      |
| Anglais      |
| Anglais      |
| Allemand      |
| Italien      |
| Japonais      |
| Portugais (Brésil)     |
| Russe      |
| Espagnol      |

L’API de traduction de conversation Translator Speech prend en charge les langues cibles suivantes pour la reconnaissance vocale.

| Langue de texte    | Code de langue |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabe       | `ar`          |
| Bangla      | `bn`          |
| Bosniaque (latin)      | `bs`          |
| Bulgare      | `bg`          |
| Cantonais (traditionnel)      | `yue`          |
| Catalan      | `ca`          |
| Chinois simplifié      | `zh-Hans`          |
| Chinois traditionnel      | `zh-Hant`          |
| Croate      | `hr`          |
| Tchèque      | `cs`          |
| Danois      | `da`          |
| Néerlandais      | `nl`          |
| Anglais      | `en`          |
| Estonien      | `et`          |
| Fidjien      | `fj`          |
| Filipino      | `fil`          |
| Finnois      | `fi`          |
| Anglais      | `fr`          |
| Allemand      | `de`          |
| Grec      | `el`          |
| Créole haïtien      | `ht`          |
| Hébreu      | `he`          |
| Hindi      | `hi`          |
| Hmong blanc      | `mww`          |
| Hongrois      | `hu`          |
|Islandais|`is`          |
| Indonésien      | `id`          |
| Italien      | `it`          |
| Japonais      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coréen      | `ko`          |
| Letton      | `lv`          |
| Lituanien      | `lt`          |
| Malgache      | `mg`          |
| Malais      | `ms`          |
| Maltais      | `mt`          |
| Norvégien      | `nb`          |
| Persan      | `fa`          |
| Polonais      | `pl`          |
| Portugais      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Roumain      | `ro`          |
| Russe      | `ru`          |
| Samoan      | `sm`          |
| Serbe (cyrillique)      | `sr-Cyrl`          |
| Serbe (latin)      | `sr-Latn`          |
| Slovaque     | `sk`          |
| Slovène      | `sl`          |
| Espagnol      | `es`          |
| Suédois      | `sv`          |
| Tahitien      | `ty`          |
| Tamoul      | `ta`          |
| Thaï      | `th`          |
| Tonguien      | `to`          |
| Turc      | `tr`          |
| Ukrainien      | `uk`          |
| Ourdou      | `ur`          |
| Vietnamien      | `vi`          |
| Gallois      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Accéder à la liste par programmation

Vous pouvez accéder à la liste des langues prises en charge par programmation en utilisant la ressource Langues. La liste fournit chaque code de langue, ainsi que le nom de la langue en anglais, ou dans toute autre langue prise en charge. Cette liste est automatiquement mise à jour par le service Translator Speech quand de nouvelles langues sont disponibles.

La ressource Langues renvoie la liste des langues prises en charge pour la parole, le texte et la synthèse vocale. La ressource Langues ne nécessite pas d’authentification.

[Consultez la documentation de référence sur l’API pour essayer la méthode languages](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accéder à la liste sur le site web de Microsoft Translator

Si vous souhaitez examiner rapidement les langues, le site web de Microsoft Translator présente toutes les langues prises en charge par les API de traduction de texte Translator Text et Speech. Cette liste ne contient pas d’informations spécifiques pour les développeurs, telles que des codes de langue.

[Voir la liste des langues](https://www.microsoft.com/translator/languages.aspx)
