---
title: Prise en charge linguistique - API de traduction de texte Translator Text
titleSuffix: Azure Cognitive Services
description: Liste des langages naturels pris en charge par l’API de traduction de texte Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 0b1187083c14fc7c536f6a32f3a41957f53f299b
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679713"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Langues et régions prises en charge par l’API de traduction de texte Translator Text

L’API de traduction de texte Translator Text prend en charge les langues ci-après pour la traduction de texte. La traduction automatique neuronale (NMT) constitue la nouvelle référence en matière de traductions automatiques de grande qualité reposant sur l’intelligence artificielle, et est proposée par défaut dans la version V3 de l’API de traduction de texte Translator Text lorsqu’un système neuronal est disponible. 

[En savoir plus sur le fonctionnement de la traduction automatique](https://www.microsoft.com/translator/mt.aspx)

**API Translator V2**

> [!NOTE]
> La V2 a été dépréciée le 30 avril 2018 et sera supprimée le 30 avril 2019.

* Statistique uniquement : Aucun système neural n’est disponible pour cette langue.
* Neural disponible : Un système neural est disponible. Utilisez le paramètre `category=generalnn` pour accéder au système neural.
* Neural par défaut : Neural est le système de traduction par défaut. Utilisez le paramètre `category=smt` pour accéder au système de statistiques en vue d’une utilisation avec Microsoft Translator Hub.
* Neural uniquement : Seule la traduction neurale est disponible.

L’**API Translator V3** est neurale par défaut et les systèmes statistiques ne sont disponibles qu’en l’absence d’un système neural. Custom Translator ne peut être utilisé qu’avec des langues neurales. 

|Langage|  Code de langue|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Statistique uniquement|  Neuronale|
|Arabe|    `ar`    |Neural disponible|  Neuronale|
|Arabe, levantin| `apc`   |Neural disponible|  Neuronale|
|Bangla|    `bn`    |Neural disponible|  Neuronale|
|Bosniaque (latin)|   `bs`    |Statistique uniquement|  Statistique|
|Bulgare| `bg`    |Neural disponible|  Neuronale|
|Cantonais (traditionnel)|   `yue`   |Statistique uniquement|  Statistique|
|Catalan|   `ca`    |Statistique uniquement|  Statistique|
|Chinois simplifié|    `zh-Hans`   |Neural par défaut |Neuronale|
|Chinois traditionnel|   `zh-Hant`   |Neural par défaut |Neuronale|
|Croate|  `hr`    |Neural disponible|  Neuronale|
|Tchèque| `cs`    |Neural disponible|  Neuronale|
|Danois|    `da`    |Neural disponible   |Neuronale|
|Néerlandais| `nl`    |Neural disponible|  Neuronale|
|Français|   `en`    |Neural disponible|  Neuronale|
|Estonien|  `et`    |Neural disponible|  Neuronale|
|Fidjien|    `fj`    |Statistique uniquement|  Statistique|
|Filipino|  `fil`   |Statistique uniquement|  Statistique|
|Finnois|   `fi`    |Neural disponible|  Neuronale|
|Français|    `fr`    |Neural disponible|  Neuronale|
|Allemand|    `de`    |Neural disponible|  Neuronale|
|Grec| `el`    |Neural disponible|  Neuronale|
|Créole haïtien|    `ht`    |Statistique uniquement   |Statistique|
|Hébreu |`he`   |Neural disponible   |Neuronale|
|Hindi| `hi`    |Neural par défaut|    Neuronale|
|Hmong blanc| `mww`   |Statistique uniquement|  Statistique|
|Hongrois| `hu`    |Neural disponible|  Neuronale|
|Islandais| `is`    |Neural uniquement|   Neuronale|
|Indonésien|    `id`    |Statistique uniquement|  Statistique|
|Italien|   `it`    |Neural disponible|  Neuronale|
|Japonais|  `ja`    |Neural disponible|  Neuronale|
|Kiswahili| `sw`    |Statistique uniquement|  Statistique|
|Klingon|   `tlh`   |Statistique uniquement|  Statistique|
|Klingon (plqaD)|   `tlh-Qaak`  |Statistique uniquement|  Statistique|
|Coréen |`ko`   |Neural disponible|  Neuronale|
|Letton|   `lv`    |Neural disponible|  Neuronale|
|Lituanien|    `lt`    |Neural disponible|  Neuronale|
|Malgache|  `mg`    |Statistique uniquement|  Statistique|
|Malais| `ms`    |Statistique uniquement   |Statistique|
|Maltais|   `mt`    |Statistique uniquement|  Statistique|
|Norvégien| `nb`    |Neural disponible|  Neuronale|
|Persan|   `fa`    |Statistique uniquement|  Statistique|
|Polonais|    `pl`    |Neural disponible|  Neuronale|
|Portugais|    `pt`    |Neural disponible|  Neuronale|
|Queretaro Otomi|   `otq`   |Statistique uniquement|  Statistique|
|Roumain|  `ro`    |Neural disponible|  Neuronale|
|Russe|   `ru`    |Neural disponible|  Neuronale|
|Samoan|    `sm`    |Statistique uniquement|  Statistique|
|Serbe (cyrillique)|    `sr-Cyrl`   |Statistique uniquement|  Statistique|
|Serbe (latin)|   `sr-Latn`   |Statistique uniquement   |Statistique|
|Slovaque|    `sk`    |Neural disponible|  Neuronale|
|Slovène| `sl`    |Neural disponible|  Neuronale|
|Espagnol|   `es`    |Neural disponible|  Neuronale|
|Suédois|   `sv`    |Neural disponible   |Neuronale|
|Tahitien|  `ty`    |Statistique uniquement|  Statistique|
|Tamoul| `ta`    |Statistique uniquement|  Statistique|
|Télougou|    `te`    |Neural uniquement|   Neuronale|
|Thaï|  `th`    |Neural disponible|  Neuronale|
|Tonguien|    `to`    |Statistique uniquement|  Statistique|
|Turc|   `tr`    |Neural disponible   |Neuronale|
|Ukrainien| `uk`    |Neural disponible|  Neuronale|
|Ourdou|  `ur`    |Statistique uniquement|  Statistique|
|Vietnamien|    `vi`    |Neural disponible|  Neuronale|
|Gallois| `cy`    |Neural disponible|  Neuronale|
|Yucatec Maya|  `yua`   |Statistique uniquement|  Statistique|

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
| Malayalam | ml | Malayalam | --> | Latin |
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
| Kiswahili      | `sw`          |
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
| Chinois (simplifié) |
| Chinois (traditionnel) |
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
| Norvégien (nynorsk) |
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
| Télougou |
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
