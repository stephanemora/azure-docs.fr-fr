---
title: Prise en charge linguistique – Translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator prend en charge les langues suivantes pour la traduction de texte par traduction automatique neuronale.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: b114c4f3e6646f68e7a1068be8dd52778a26fb58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738138"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Langues et régions prises en charge pour la traduction de texte et vocale

Utilisez Traducteur pour traduire vers et depuis plus de 90 langues et dialectes de traduction de texte. La traduction automatique neuronale (NMT) constitue la nouvelle référence en matière de traductions automatiques de grande qualité reposant sur l’intelligence artificielle, et est proposée par défaut dans Translator v3 lorsqu’un système neuronal est disponible.

Vous pouvez aussi utiliser Translator en association avec Custom Translator pour créer des systèmes de traduction neuronaux qui comprennent la terminologie utilisée dans votre entreprise et votre secteur d’activité, et en association avec le service Microsoft Speech pour ajouter la traduction vocale à votre application.

[En savoir plus sur le fonctionnement de la traduction automatique](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Traduction de texte
La traduction de texte est disponible à l’aide de l’opération Translate vers ou à partir de l’une des langues disponibles dans Translator. L’API offre également la détection de langue à l’aide de l’opération Detect, la translittération à l’aide de l’opération Transliterate et des dictionnaires bilingues à l’aide des opérations Dictionary Lookup et Dictionary Examples. Les langues disponibles pour chacune de ces opérations sont répertoriées ci-dessous. 

### <a name="translate"></a>Translate

Translator prend en charge les langues suivantes pour la traduction de texte en texte. 

[Voir la documentation de référence de l’opération Translate](reference/v3-0-translate.md)

| Langage | Code langue |
|:-|:-:|
| Afrikaans | `af` |
| Albanais | `sq` |
| Amharique | `am` |
| Arabe | `ar` |
| Arménien | `hy` |
| Assamais | `as` |
| Azéri | `az` |
| Bangla | `bn` |
| Bosniaque (latin) | `bs` |
| Bulgare | `bg` |
| Cantonais (traditionnel) | `yue` |
| Catalan | `ca` |
| Chinois (simplifié) | `zh-Hans` |
| Chinois traditionnel | `zh-Hant` |
| Croate | `hr` |
| Tchèque | `cs` |
| Dari | `prs` |
| Danois | `da` |
| Néerlandais | `nl` |
| Anglais | `en` |
| Estonien | `et` |
| Fidjien | `fj` |
| Filipino | `fil` |
| Finnois | `fi` |
| Français | `fr` |
| Français (Canada) | `fr-ca` |
| Allemand | `de` |
| Grec | `el` |
| Goudjrati | `gu` |
| Créole haïtien | `ht` |
| Hébreu | `he` |
| Hindi | `hi` |
| Hmong blanc | `mww` |
| Hongrois | `hu` |
| Islandais | `is` |
| Indonésien | `id` |
| Inuktitut | `iu` |
| Irlandais | `ga` |
| Italien | `it` |
| Japonais | `ja` |
| Kannada | `kn` |
| Kazakh | `kk` |
| Khmer | `km` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Coréen | `ko` |
| Kurde (central) | `ku` |
| Kurde (Nord) | `kmr` |
| Lao | `lo` |
| Letton | `lv` |
| Lituanien | `lt` |
| Malgache | `mg` |
| Malais | `ms` |
| Malayalam | `ml` |
| Maltais | `mt` |
| Maori | `mi` |
| Marathi | `mr` |
| Myanmar | `my` |
| Népalais | `ne` |
| Norvégien | `nb` |
| Odia | `or` |
| Pachto | `ps` |
| Persan | `fa` |
| Polonais | `pl` |
| Portugais (Brésil) | `pt` |
| Portugais (Portugal) | `pt-pt` |
| Pendjabi | `pa` |
| Queretaro Otomi | `otq` |
| Roumain | `ro` |
| Russe | `ru` |
| Samoan | `sm` |
| Serbe (cyrillique) | `sr-Cyrl` |
| Serbe (latin) | `sr-Latn` |
| Slovaque | `sk` |
| Slovène | `sl` |
| Espagnol | `es` |
| Swahili | `sw` |
| Suédois | `sv` |
| Tahitien | `ty` |
| Tamoul | `ta` |
| Télougou | `te` |
| Thaï | `th` |
| Tigrigna | `ti` |
| Tonga | `to` |
| Turc | `tr` |
| Ukrainien | `uk` |
| Ourdou | `ur` |
| Vietnamien | `vi` |
| Gallois | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> Par défaut, le code de langue `pt` sera `pt-br`, Portugais (Brésil).

### <a name="detect"></a>Detect

Translator détecte les langues suivantes pour la traduction et la translittération.

[Voir la documentation de référence de l’opération Detect](reference/v3-0-detect.md)

| Langage | Code langue |
|:-|:-:|
| Afrikaans | `af` |
| Albanais | `sq` |
| Arabe | `ar` |
| Bulgare | `bg` |
| Catalan | `ca` |
| Chinois (simplifié) | `zh-Hans` |
| Chinois traditionnel | `zh-Hant` |
| Croate | `hr` |
| Tchèque | `cs` |
| Danois | `da` |
| Néerlandais | `nl` |
| Anglais | `en` |
| Estonien | `et` |
| Finnois | `fi` |
| Français | `fr` |
| Allemand | `de` |
| Grec | `el` |
| Goudjrati | `gu` |
| Créole haïtien | `ht` |
| Hébreu | `he` |
| Hindi | `hi` |
| Hongrois | `hu` |
| Islandais | `is` |
| Indonésien | `id` |
| Inuktitut | `iu` |
| Irlandais | `ga` |
| Italien | `it` |
| Japonais | `ja` |
| Klingon | `tlh-Latn` |
| Coréen | `ko` |
| Kurde (central) | `ku-Arab` |
| Letton | `lv` |
| Lituanien | `lt` |
| Malais | `ms` |
| Maltais | `mt` |
| Norvégien | `nb` |
| Pachto | `ps` |
| Persan | `fa` |
| Polonais | `pl` |
| Portugais | `pt` |
| Roumain | `ro` |
| Russe | `ru` |
| Serbe (cyrillique) | `sr-Cyrl` |
| Serbe (latin) | `sr-Latn` |
| Slovaque | `sk` |
| Slovène | `sl` |
| Espagnol | `es` |
| Swahili | `sw` |
| Suédois | `sv` |
| Tahitien | `ty` |
| Thaï | `th` |
| Turc | `tr` |
| Ukrainien | `uk` |
| Ourdou | `ur` |
| Vietnamien | `vi` |
| Gallois | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Transliterate

La méthode Transliterate prend en charge les langues ci-après. Dans la colonne « Vers/De », le symbole « <--> » indique que la langue peut être translittérée aussi bien à partir des scripts indiqués que vers ces derniers. Le symbole « --> » signifie que la langue peut uniquement être translittérée du premier script vers le second.

[Voir la documentation de référence de l’opération Transliterate](reference/v3-0-translate.md)


| Langage    | Code langue | Script | Vers/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabe | `ar` | Arabe `Arab` | <--> | Latin `Latn` |
| Assamais | `as` | Bengali `Beng` | <--> | Latin `Latn` |
| Bangla  | `bn` | Bengali `Beng` | <--> | Latin `Latn` |
|Biélorusse| `be` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
|Bulgare| `bg` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
| Chinois (simplifié) | `zh-Hans` | Chinois (simplifié) `Hans`| <--> | Latin `Latn` |
| Chinois (simplifié) | `zh-Hans` | Chinois (simplifié) `Hans`| <--> | Chinois (traditionnel) `Hant`|
| Chinois (traditionnel) | `zh-Hant` | Chinois (traditionnel) `Hant`| <--> | Latin `Latn` |
| Chinois (traditionnel) | `zh-Hant` | Chinois (traditionnel) `Hant`| <--> | Chinois (simplifié) `Hans` |
|Grec| `el` | Grec `Grek`  | <--> | Latin `Latn` |
| Goudjrati | `gu`  | Goudjrati `Gujr` | <--> | Latin `Latn` |
| Hébreu | `he` | Hébreu `Hebr` | <--> | Latin `Latn` |
| Hindi | `hi` | Dévanâgarî `Deva` | <--> | Latin `Latn` |
| Japonais | `ja` | Japonais `Jpan` | <--> | Latin `Latn` |
| Kannada | `kn` | Kannada `Knda` | <--> | Latin `Latn` |
|Kazakh| `kk` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
|Coréen| `ko` | Coréen `Kore`  | <--> | Latin `Latn` |
|Kirghiz| `ky` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
|Macédonien| `mk` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | <--> | Latin `Latn` |
| Marathi | `mr` | Dévanâgarî `Deva` | <--> | Latin `Latn` |
|Mongol| `mn` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latin `Latn` |
|Persan| `fa` | Arabe `Arab`  | <--> | Latin `Latn` |
| Pendjabi | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
|Russe| `ru` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
| Serbe (cyrillique) | `sr-Cyrl` | Cyrillique `Cyrl`  | --> | Latin `Latn` |
| Serbe (latin) | `sr-Latn` | Latin `Latn` | --> | Cyrillique `Cyrl`|
|Sindhi| `sd` | Arabe `Arab`  | <--> | Latin `Latn` |
|Cingalais| `si` | Cingalais `Sinh`  | <--> | Latin `Latn` |
|Tadjik| `tg` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
| Tamoul | `ta` | Tamoul `Taml` | <--> | Latin `Latn` |
|Tatar| `tt` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
| Télougou | `te` | Télougou `Telu` | <--> | Latin `Latn` |
| Thaï | `th` | Thaï `Thai` | --> | Latin `Latn` |
|Ukrainien| `uk` | Cyrillique `Cyrl`  | <--> | Latin `Latn` |
|Ourdou| `ur` | Arabe `Arab`  | <--> | Latin `Latn` |

### <a name="dictionary"></a>Dictionnaire

Le dictionnaire prend en charge les langues ci-après de ou vers l’anglais à l’aide des méthodes Lookup et Examples.

Consultez la documentation de référence pour les opérations [Dictionary Lookup](reference/v3-0-dictionary-lookup.md) et [Dictionary Examples](reference/v3-0-dictionary-examples.md).

| Langage    | Code langue |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabe       | `ar`          |
| Bangla      | `bn`          |
| Bosniaque (latin)      | `bs`          |
| Bulgare      | `bg`          |
| Catalan      | `ca`          |
| Chinois (simplifié)      | `zh-Hans`          |
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
| Klingon      | `tlh`          |
| Coréen      | `ko`          |
| Letton      | `lv`          |
| Lituanien      | `lt`          |
| Malais      | `ms`          |
| Maltais      | `mt`          |
| Norvégien      | `nb`          |
| Persan      | `fa`          |
| Polonais      | `pl`          |
| Portugais (Brésil)     | `pt`          |
| Roumain      | `ro`          |
| Russe      | `ru`          |
| Serbe (latin)      | `sr-Latn`          |
| Slovaque     | `sk`          |
| Slovène      | `sl`          |
| Espagnol      | `es`          |
| Swahili      | `sw`          |
| Suédois      | `sv`          |
| Tamoul      | `ta`          |
| Thaï      | `th`          |
| Turc      | `tr`          |
| Ukrainien      | `uk`          |
| Ourdou      | `ur`          |
| Vietnamien      | `vi`          |
| Gallois      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Accéder par programme à la liste de langues de Translator

Vous pouvez utiliser la méthode Languages pour récupérer la liste des langues prises en charge par Translator. Vous pouvez visualiser la liste par fonctionnalité, par code de langue, ainsi que par le nom de langue en anglais ou dans toute autre langue prise en charge. Cette liste est automatiquement mise à jour par le service Microsoft Translator lorsque de nouvelles langues deviennent disponibles.

[Visualiser la documentation de référence sur l’opération Languages](reference/v3-0-languages.md)

## <a name="customization"></a>Personnalisation

Les langues suivantes sont disponibles pour la personnalisation vers ou depuis l'anglais à l'aide de [Custom Translator](https://aka.ms/CustomTranslator).

| Langage    | Code langue |
|:----------- |:-------------:|
|Afrikaans| `af`|
| Arabe       | `ar`          |
| Bangla      | `bn`          |
| Bosniaque (latin)      | `bs`          |
| Bulgare      | `bg`          |
|Catalan|   `ca`    |
| Chinois (simplifié)      | `zh-Hans`          |
|Chinois traditionnel|   `zh-Hant`   |
| Croate      | `hr`          |
| Tchèque      | `cs`          |
| Danois      | `da`          |
| Néerlandais      | `nl`          |
| Anglais    | `en`     |
| Estonien      | `et`          |
|Fidjien|    `fj`    |
|Filipino|  `fil`   |
| Finnois      | `fi`          |
| Français      | `fr`          |
| Allemand      | `de`          |
| Grec      | `el`          |
| Goudjrati| `gu`    |
| Hébreu      | `he`          |
| Hindi      | `hi`          |
| Hongrois      | `hu`          |
| Islandais | `is` |
| Indonésien|   `id`    |
| Irlandais | `ga`  |
| Italien      | `it`          |
| Japonais      | `ja`          |
|Kannada|`kn`|
| Coréen      | `ko`          |
| Letton      | `lv`          |
| Lituanien      | `lt`          |
| Malgache| `mg`    |
| Malais|    `ms` |
|Maltais|   `mt`    |
| Maori| `mi`  |
| Marathi| `mr`  |
| Norvégien      | `nb`          |
| Persan      | `fa`          |
| Polonais      | `pl`          |
| Portugais (Brésil) | `pt` |
| Pendjabi|`pa`|
| Roumain      | `ro`          |
| Russe      | `ru`          |
| Samoan|   `sm`    |
| Serbe (latin)      | `sr-Latn`          |
| Slovaque     | `sk`          |
| Slovène      | `sl`          |
| Espagnol      | `es`          |
| Swahili|  `sw`    |
| Suédois      | `sv`          |
|Tahitien|  `ty`    |
| Thaï      | `th`          |
|Tonga|    `to`    |
| Turc      | `tr`          |
| Ukrainien      | `uk`          |
| Ourdou| `ur`    |
| Vietnamien      | `vi`          |
| Gallois | `cy` |

## <a name="speech-translation"></a>Traduction vocale
La traduction vocale est disponible à l’aide de Translator avec le service Speech de Cognitive Services. Consultez la [documentation du service Speech](../speech-service/index.yml) pour en savoir plus sur l’utilisation de la traduction vocale et pour afficher toutes les [options de langues disponibles](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Reconnaissance vocale
Convertissez la parole en texte afin d’effectuer la traduction dans la langue textuelle de votre choix. La reconnaissance vocale est utilisée pour la traduction de la voix en texte, ou pour traduire la voix en voix lorsqu’elle est utilisée conjointement avec la synthèse vocale.

| Langage    |
|:----------- |
|Arabe|
|Cantonais (traditionnel)|
|Catalan|
|Chinois (simplifié)|
|Chinois traditionnel|
|Danois|
|Néerlandais|
|Anglais|
|Finnois|
|Français|
|Français (Canada)|
|Allemand|
|Goudjrati|
|Hindi|
|Italien|
|Japonais|
|Coréen|
|Marathi|
|Norvégien|
|Polonais|
|Portugais (Brésil)|
|Portugais (Portugal)|
|Russe|
|Espagnol|
|Suédois|
|Tamoul|
|Télougou|
|Thaï|
|Turc|

### <a name="text-to-speech"></a>Synthèse vocale
Convertir du texte en parole. La synthèse vocale est utilisée pour ajouter une sortie audible des résultats de la traduction, ou pour traduire la voix en voix lorsqu’elle est utilisée avec la reconnaissance vocale. 

| Langage |
|:-|
| Arabe |
| Bulgare |
| Cantonais (traditionnel) |
| Catalan |
| Chinois (simplifié) |
| Chinois traditionnel |
| Croate |
| Tchèque |
| Danois |
| Néerlandais |
| Anglais |
| Finnois |
| Français |
| Français (Canada) |
| Allemand |
| Grec |
| Hébreu |
| Hindi |
| Hongrois |
| Indonésien |
| Italien |
| Japonais |
| Coréen |
| Malais |
| Norvégien |
| Polonais |
| Portugais (Brésil) |
| Portugais (Portugal) |
| Roumain |
| Russe |
| Slovaque |
| Slovène |
| Espagnol |
| Suédois |
| Tamoul |
| Télougou |
| Thaï |
| Turc |
| Vietnamien |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Afficher la liste des langues sur le site web de Microsoft Translator

Si vous souhaitez examiner rapidement les langues, le site web de Microsoft Translator présente toutes les langues prises en charge par Translator pour la traduction de texte et par le service Speech pour la traduction vocale. Cette liste ne comporte pas d’informations propres aux développeurs, comme les codes de langue.

[Voir la liste des langues](https://www.microsoft.com/translator/languages.aspx)
