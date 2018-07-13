---
title: Langues prises en charge dans l’API de traduction de conversation Translator Speech Microsoft | Microsoft Docs
description: Découvrez les langues prises en charge par l’API de traduction de conversation Translator Speech Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 0d33033442a012290baa78d80f1b8bde0499b3f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370693"
---
# <a name="languages-supported-by-the-microsoft-translator-speech-api"></a>Langues prises en charge par l’API de traduction de conversation Translator Speech Microsoft.
Les langues prises en charge pour la traduction vocale sont les suivantes. Si les deux langues sont prises en charge pour la traduction vocale, la traduction de parole en parole ou de parole en texte est disponible. Si la langue cible n’est pas prise en charge pour la traduction vocale, seule la traduction de parole en texte est disponible. 

| Langue de synthèse vocale    |
|:----------- |
| Arabe (standard moderne)      |
| Chinois (mandarin)      |
| Français      |
| Français      |
| Allemand      |
| Italien      |
| Japonais      |
| Portugais (Brésil)     |
| Russe      |
| Espagnol      | 

L’API de traduction de conversation Translator Speech prend en charge les langues cibles suivantes pour traduction de parole en texte. 

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
| Français      | `en`          |
| Estonien      | `et`          |
| Fidjien      | `fj`          |
| Filipino      | `fil`          |
| Finnois      | `fi`          |
| Français      | `fr`          |
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
| Swahili      | `sw`          |
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

Vous pouvez accéder à la liste des langues prises en charge par programmation en utilisant la ressource Langues. La liste fournit chaque code de langue, ainsi que le nom de la langue en anglais, ou dans toute autre langue prise en charge. Cette liste est automatiquement mise à jour par le service Microsoft Translator lorsque de nouvelles langues deviennent disponibles.

La ressource Langues renvoie la liste des langues prises en charge pour la parole, le texte et la synthèse vocale. La ressource Langues ne nécessite pas d’authentification.

[Consultez la documentation de référence sur l’API pour essayer la méthode languages](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accéder à la liste sur le site web de Microsoft Translator

Si vous souhaitez examiner rapidement les langues, le site web de Microsoft Translator présente toutes les langues prises en charge par les API de traduction de texte Translator Text et Speech. Cette liste ne contient pas d’informations spécifiques pour les développeurs, telles que des codes de langue.

[Voir la liste des langues](https://www.microsoft.com/translator/languages.aspx) 
