---
title: Langues prises en charge dans l’API Microsoft Translator | Microsoft Docs
description: Découvrez les langues prises en charge par l’API de traduction de texte Microsoft Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: v-jansko
ms.openlocfilehash: 5542c1c0eee5e4c7c85c566908dc8999b118f06e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37032434"
---
# <a name="supported-languages-in-the-microsoft-translator-text-api"></a>Langues prises en charge dans l’API de traduction de texte Microsoft Translator Text

L’API de traduction de texte Microsoft Translator Text prend en charge les langues ci-après pour la traduction de texte. La traduction automatique neuronale (NMT) constitue la nouvelle référence en matière de traductions automatiques de grande qualité reposant sur l’intelligence artificielle, et est proposée par défaut dans la version V3 de l’API de traduction de texte Translator Text lorsqu’un système neuronal est disponible. La traduction automatique neuronale est disponible dans la version V2 par le biais de la catégorie « generalnn ».

[En savoir plus sur le fonctionnement de la traduction automatique](https://www.microsoft.com/translator/mt.aspx)

| Langage    | Type de traduction |Code de langue |
|:----------- |:-------:|:-------------:|
| Afrikaans      | Statistique |`af`          |
| Arabe      | Neuronale | `ar`          |
| Arabe, levantin    | Neuronale | `apc`
| Bangla      | Neuronale |`bn`          |
| Bosniaque (latin)      | Statistique |`bs`          |
| Bulgare     |  Neuronale |`bg`          |
| Cantonais (traditionnel)      | Statistique |`yue`          |
| Catalan      | Statistique |`ca`          |
| Chinois simplifié        |  Neuronale |`zh-Hans`          | 
| Chinois traditionnel        |  Neuronale |`zh-Hant`          |
| Croate      | Neuronale |`hr`          |
| Tchèque        |  Neuronale |`cs`          |
| Danois        |  Neuronale |`da`          |
| Néerlandais        |  Neuronale |`nl`          |
| Français       |  Neuronale |`en`          |
| Estonien      | Neuronale |`et`          |
| Fidjien      | Statistique |`fj`          |
| Filipino      | Statistique |`fil`          |
| Finnois      | Neuronale |`fi`          |
| Français        |  Neuronale |`fr`          |
| Allemand       |  Neuronale |`de`          |
| Grec      | Neuronale |`el`          |
| Créole haïtien      | Statistique |`ht`          |
| Hébreu      | Neuronale |`he`          |
| Hindi        |  Neuronale |`hi`          |
| Hmong blanc      | Statistique |`mww`          |
| Hongrois      | Neuronale |`hu`          |
| Islandais      |  Neuronale |`is`           |
| Indonésien      | Statistique |`id`          |
| Italien        |  Neuronale |`it`          |
| Japonais        |  Neuronale |`ja`          |
| Swahili      | Statistique |`sw`          |
| Klingon      | Statistique |`tlh`          |
| Klingon (plqaD)      | Statistique |`tlh-Qaak`          |
| Coréen        |  Neuronale |`ko`          |
| Letton      | Neuronale |`lv`          |
| Lituanien      | Neuronale |`lt`          |
| Malgache      | Statistique |`mg`          |
| Malais      | Statistique |`ms`          |
| Maltais      | Statistique |`mt`          |
| Norvégien        |  Neuronale |`nb`          |
| Persan      | Statistique |`fa`          |
| Polonais        |  Neuronale |`pl`          |
| Portugais        |  Neuronale |`pt`          |
| Queretaro Otomi      | Statistique |`otq`          |
| Roumain        |  Neuronale |`ro`          |
| Russe        |  Neuronale |`ru`          |
| Samoan      | Statistique |`sm`          |
| Serbe (cyrillique)      | Statistique |`sr-Cyrl`          |
| Serbe (latin)      | Statistique |`sr-Latn`          |
| Slovaque     | Neuronale |`sk`          |
| Slovène      | Neuronale |`sl`          |
| Espagnol        |  Neuronale |`es`          |
| Suédois        |  Neuronale |`sv`          |
| Tahitien      | Statistique |`ty`          |
| Tamoul      | Statistique |`ta`          |
| Thaï      | Neuronale |`th`          |
| Tonguien      | Statistique |`to`          |
| Turc       |  Neuronale |`tr`          |
| Ukrainien      | Neuronale |`uk`          |
| Ourdou      | Statistique |`ur`          |
| Vietnamien      | Neuronale |`vi`          |
| Gallois      | Neuronale |`cy`          |
| Yucatec Maya      | Statistique |`yua`          |

## <a name="transliteration"></a>Translittération

La méthode Transliterate prend en charge les langues ci-après. Dans la colonne « Vers/De », le symbole « <--> » indique que la langue peut être translittérée aussi bien à partir des scripts indiqués que vers ces derniers. Le symbole « --> » signifie que la langue peut uniquement être translittérée du premier script vers le second.

| Langage    | Code de langue | Script | Vers/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabe | ar | Arabe | <--> | Latin |
|Bangla  | bn | Bengali | <--> | Latin |
| Chinois (simplifié) | zh-Hans | Chinois simplifié | <--> | Latin |
| Chinois (simplifié) | zh-Hans | Chinois simplifié | <--> | Chinois traditionnel |
| Chinois (traditionnel) | zh-Hant | Chinois traditionnel | <--> | Latin |
| Chinois (traditionnel) | zh-Hant | Chinois traditionnel | <--> | Chinois simplifié |
| Goudjrati | gu  | Goudjrati | --> | Latin |
| Hébreu | he | Hébreu | <--> | Latin |
| Hindi | hi | Dévanâgarî | <--> | Latin |
| Japonais | ja | Japonais | <--> | Latin |
| Kannada | kn | Kannada | --> | Latin |
| Malais | ml | Malayalam | --> | Latin |
| Marathi | mr | Dévanâgarî | --> | Latin |
| Oriya | or | Oriya | <--> | Latin |
| Pendjabi | pa | Gurmukhi | <--> | Latin  |
| Serbe (cyrillique) | sr-Cyrl | Cyrillique  | --> | Latin |
| Serbe (latin) | sr-Latn | Latin | --> | Cyrillique |
| Tamoul | ta | Tamoul | --> | Latin |
| Télougou | te | Télougou | --> | Latin |
| Thaï | th | Thaï | <--> | Latin |

## <a name="dictionary"></a>Dictionnaire

Le dictionnaire prend en charge les langues ci-après de ou vers l’anglais à l’aide des méthodes Lookup et Examples.

| Langage    | Code de langue |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabe       | `ar`          |
| Bangla      | `bn`          |
| Bosniaque (latin)      | `bs`          |
| Bulgare      | `bg`          |
| Catalan      | `ca`          |
| Chinois simplifié      | `zh-Hans`          | 
| Croate      | `hr`          |
| Tchèque      | `cs`          |
| Danois      | `da`          |
| Néerlandais      | `nl`          |
| Estonien      | `et`          |
| Finnois      | `fi`          |
| Français      | `fr`          |
| Allemand      | `de`          |
| Grec      | `el`          |
| Créole haïtien      | `ht`          |
| Hébreu      | `he`          |
| Hindi      | `hi`          |
| Hmong blanc      | `mww`          |
| Hongrois      | `hu`          |
| Islandais    | `is`  |
| Indonésien      | `id`          |
| Italien      | `it`          |
| Japonais      | `ja`          |
| Swahili      | `sw`          |
| Klingon      | `tlh`          |
| Coréen      | `ko`          |
| Letton      | `lv`          |
| Lituanien      | `lt`          |
| Malais      | `ms`          |
| Maltais      | `mt`          |
| Norvégien      | `nb`          |
| Persan      | `fa`          |
| Polonais      | `pl`          |
| Portugais      | `pt`          |
| Roumain      | `ro`          |
| Russe      | `ru`          |
| Serbe (latin)      | `sr-Latn`          |
| Slovaque     | `sk`          |
| Slovène      | `sl`          |
| Espagnol      | `es`          |
| Suédois      | `sv`          |
| Tamoul      | `ta`          |
| Thaï      | `th`          |
| Turc      | `tr`          |
| Ukrainien      | `uk`          |
| Ourdou      | `ur`          |
| Vietnamien      | `vi`          |
| Gallois      | `cy`          |

## <a name="languages-detected-by-the-detect-method"></a>Langues détectées par la méthode Detect

La méthode Detect peut détecter les langues ci-après. Detect peut détecter des langues que Microsoft Translator ne peut pas traduire.

| Langage    |
|:----------- |
| Afrikaans |
| Albanais |
| Arabe |
| Basque |
| Biélorusse |
| Bulgare |
| Catalan |
| Chinois |
| Chinois simplifié |
| Chinois traditionnel |
| Croate |
| Tchèque |
| Danois |
| Néerlandais |
| Français |
| Espéranto |
| Estonien |
| Finnois |
| Français |
| Galicien |
| Allemand |
| Grec |
| Créole haïtien |
| Hébreu |
| Hindi |
| Hongrois |
| Islandais |
| Indonésien |
| Irlandais |
| Italien |
| Japonais |
| Coréen |
| Kurde (arabe) |
| Kurde (latin) |
| Latin |
| Letton |
| Lituanien |
| Macédonien |
| Malais |
| Maltais |
| Norvégien |
| Norvégien nynorsk |
| Pachto |
| Persan |
| Polonais |
| Portugais |
| Roumain |
| Russe |
| Serbe (cyrillique) |
| Serbe (latin) |
| Slovaque |
| Slovène |
| Somali |
| Espagnol |
| Swahili |
| Suédois |
| Tagalog |
| Thaï |
| Turc |
| Ukrainien |
| Ourdou |
| Ouzbek (cyrillique) |
| Ouzbek (latin) |
| Vietnamien |
| Gallois |
| Yiddish |

## <a name="access-the-list-programmatically"></a>Accéder à la liste par programme

Vous pouvez accéder à la liste des langues prises en charge par programme en utilisant l’opération Languages de l’API de traduction de texte V3.0. Vous pouvez visualiser la liste par fonctionnalité, par code de langue, ainsi que par le nom de langue en anglais ou dans toute autre langue prise en charge. Cette liste est automatiquement mise à jour par le service Microsoft Translator lorsque de nouvelles langues deviennent disponibles.

[Visualiser la documentation de référence sur l’opération Languages](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accéder à la liste sur le site web de Microsoft Translator

Si vous souhaitez examiner rapidement les langues, le site web Microsoft Translator vous présente toutes les langues prises en charge par les API Traduction de texte Translator Text et Microsoft Speech. Cette liste ne comporte pas d’informations propres aux développeurs, comme les codes de langue.

[Voir la liste des langues](https://www.microsoft.com/translator/languages.aspx)
