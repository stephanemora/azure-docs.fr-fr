---
title: Prise en charge linguistique - API de traduction de texte Translator Text
titleSuffix: Azure Cognitive Services
description: L’API de traduction de texte prend en charge les langues suivantes pour la traduction de texte en texte par traduction automatique neuronale.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 12/02/2019
ms.author: swmachan
ms.openlocfilehash: 62c101751e07d8ee31789191ad45fbdd33a1bc4b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707964"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Langues et régions prises en charge par l’API de traduction de texte Translator Text

L’API de traduction de texte Translator Text prend en charge les langues ci-après pour la traduction de texte. La traduction automatique neuronale (NMT) constitue la nouvelle référence en matière de traductions automatiques de grande qualité reposant sur l’intelligence artificielle, et est proposée par défaut dans la version V3 de l’API de traduction de texte Translator Text lorsqu’un système neuronal est disponible.

[En savoir plus sur le fonctionnement de la traduction automatique](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Traduction

**API Translator V2**

> [!NOTE]
> La V2 a été déconseillée le 30 avril 2018. Migrez vos applications vers la V3 pour bénéficier des nouvelles fonctionnalités disponibles exclusivement dans la V3.

* Statistique uniquement : Aucun système neural n’est disponible pour cette langue.
* Neural disponible : Un système neural est disponible. Utilisez le paramètre `category=generalnn` pour accéder au système neural.
* Neural par défaut : Neural est le système de traduction par défaut. Utilisez le paramètre `category=smt` pour accéder au système de statistiques en vue d’une utilisation avec Microsoft Translator Hub.
* Neural uniquement : Seule la traduction neurale est disponible.

L’**API Translator V3** est neurale par défaut et les systèmes statistiques ne sont disponibles qu’en l’absence d’un système neural.

> [!NOTE]
> Actuellement, un sous-ensemble de langues neurales est disponible dans Custom Translator et nous les enrichissons progressivement. [Affichez les langues actuellement disponibles dans Custom Translator](#customization).

|Langage|  Code de langue|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Statistique uniquement|  Neuronale|
|Arabe|    `ar`    |Neural disponible|  Neuronale|
|Bangla|    `bn`    |Neural disponible|  Neuronale|
|Bosniaque (latin)|   `bs`    |Neural disponible|  Neuronale|
|Bulgare| `bg`    |Neural disponible|  Neuronale|
|Cantonais (traditionnel)|   `yue`   |Statistique uniquement|  Statistique|
|Catalan|   `ca`    |Statistique uniquement|  Statistique|
|Chinois simplifié|    `zh-Hans`   |Neural par défaut |Neuronale|
|Chinois traditionnel|   `zh-Hant`   |Neural par défaut |Neuronale|
|Croate|  `hr`    |Neural disponible|  Neuronale|
|Tchèque| `cs`    |Neural disponible|  Neuronale|
|Danois|    `da`    |Neural disponible   |Neuronale|
|Néerlandais| `nl`    |Neural disponible|  Neuronale|
|Anglais|   `en`    |Neural disponible|  Neuronale|
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
|Maori| `mi`  |Neural uniquement| Neuronale|
|Norvégien| `nb`    |Neural disponible|  Neuronale|
|Persan|   `fa`    |Neural disponible|  Neuronale|
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
|Tamoul| `ta`    |Neural disponible | Neuronale|
|Télougou|    `te`    |Neural uniquement|   Neuronale|
|Thaï|  `th`    |Neural disponible|  Neuronale|
|Tonga|    `to`    |Statistique uniquement|  Statistique|
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
| Arabe | `ar` | Arabe `Arab` | <--> | Latin `Latn` |
|Bangla  | `bn` | Bengali `Beng` | <--> | Latin `Latn` |
| Chinois (simplifié) | `zh-Hans` | Chinois (simplifié) `Hans`| <--> | Latin `Latn` |
| Chinois (simplifié) | `zh-Hans` | Chinois (simplifié) `Hans`| <--> | Chinois (traditionnel) `Hant`|
| Chinois (traditionnel) | `zh-Hant` | Chinois (traditionnel) `Hant`| <--> | Latin `Latn` |
| Chinois (traditionnel) | `zh-Hant` | Chinois (traditionnel) `Hant`| <--> | Chinois (simplifié) `Hans` |
| Goudjrati | `gu`  | Goudjrati `Gujr` | --> | Latin `Latn` |
| Hébreu | `he` | Hébreu `Hebr` | <--> | Latin `Latn` |
| Hindi | `hi` | Dévanâgarî `Deva` | <--> | Latin `Latn` |
| Japonais | `ja` | Japonais `Jpan` | <--> | Latin `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Latin `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | --> | Latin `Latn` |
| Marathi | `mr` | Dévanâgarî `Deva` | --> | Latin `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latin `Latn` |
| Pendjabi | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
| Serbe (cyrillique) | `sr-Cyrl` | Cyrillique `Cyrl`  | --> | Latin `Latn` |
| Serbe (latin) | `sr-Latn` | Latin `Latn` | --> | Cyrillique `Cyrl`|
| Tamoul | `ta` | Tamoul `Taml` | --> | Latin `Latn` |
| Télougou | `te` | Télougou `Telu` | --> | Latin `Latn` |
| Thaï | `th` | Thaï `Thai` | <--> | Latin `Latn` |

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

## <a name="detect"></a>Detect

L’API de traduction de texte détecte toutes les langues disponibles pour la traduction et la translittération.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Accéder par programme à la liste de langues de l'API de traduction de texte Translator Text

Vous pouvez utiliser la méthode Languages pour récupérer la liste des langues prises en charge pour l'API de traduction de texte Translator Text v3.0. Vous pouvez visualiser la liste par fonctionnalité, par code de langue, ainsi que par le nom de langue en anglais ou dans toute autre langue prise en charge. Cette liste est automatiquement mise à jour par le service Microsoft Translator lorsque de nouvelles langues deviennent disponibles.

[Visualiser la documentation de référence sur l’opération Languages](reference/v3-0-languages.md)

## <a name="customization"></a>Personnalisation

Les langues suivantes sont disponibles pour la personnalisation vers ou depuis l'anglais à l'aide de [Custom Translator](https://aka.ms/CustomTranslator).

| Langage    | Code de langue |
|:----------- |:-------------:|
| Arabe       | `ar`          |
| Bangla      | `bn`          |
| Bosniaque (latin)      | `bs`          |
| Bulgare      | `bg`          |
| Chinois simplifié      | `zh-Hans`          |
|Chinois traditionnel|   `zh-Hant`   |
| Croate      | `hr`          |
| Tchèque      | `cs`          |
| Danois      | `da`          |
| Néerlandais      | `nl`          |
| Anglais    | `en`     |
| Estonien      | `et`          |
| Finnois      | `fi`          |
| Français      | `fr`          |
| Allemand      | `de`          |
| Grec      | `el`          |
| Hébreu      | `he`          |
| Hindi      | `hi`          |
| Hongrois      | `hu`          |
| Islandais | `is` |
| Indonésien|   `id`    |
| Irlandais | `ga`  |
| Italien      | `it`          |
| Japonais      | `ja`          |
|Kiswahili| `sw`    |
| Coréen      | `ko`          |
| Letton      | `lv`          |
| Lituanien      | `lt`          |
|Malgache|  `mg`    |
|Maori| `mi`  |
| Norvégien      | `nb`          |
| Persan      | `fa`          |
| Polonais      | `pl`          |
| Portugais      | `pt`          |
| Roumain      | `ro`          |
| Russe      | `ru`          |
|Samoan|    `sm`    |
| Serbe (latin)      | `sr-Latn`          |
| Slovaque     | `sk`          |
| Slovène      | `sl`          |
| Espagnol      | `es`          |
| Suédois      | `sv`          |
| Thaï      | `th`          |
| Turc      | `tr`          |
| Ukrainien      | `uk`          |
| Vietnamien      | `vi`          |
| Gallois | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Accéder à la liste sur le site web de Microsoft Translator

Si vous souhaitez examiner rapidement les langues, le site web Microsoft Translator vous présente toutes les langues prises en charge par les API Traduction de texte Translator Text et Microsoft Speech. Cette liste ne comporte pas d’informations propres aux développeurs, comme les codes de langue.

[Voir la liste des langues](https://www.microsoft.com/translator/languages.aspx)
