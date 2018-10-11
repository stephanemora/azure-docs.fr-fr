---
title: Prise en charge linguistique - API du service de reconnaissance vocale
description: Liste des langages naturels pris en charge par le service de reconnaissance vocale.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jerkin
ms.openlocfilehash: 9a867289cffa17030e397ef170e9055451057410
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237736"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Prise en charge de la région et de la langue pour l’API du service de reconnaissance vocale

Différentes langues sont prises en charge pour différentes fonctions du service de reconnaissance vocale. Les tableaux suivants récapitulent la prise en charge linguistique.

## <a name="speech-to-text"></a>Reconnaissance vocale

L’API de reconnaissance vocale Microsoft prend en charge les langues suivantes. Différents niveaux de personnalisation sont disponibles pour chaque langue.

  Code | Langage | [Adaptation acoustique](how-to-customize-acoustic-models.md) | [Adaptation de langage](how-to-customize-language-model.md) | [Adaptation de prononciation](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | Arabe (Égypte), standard moderne | Non  | Oui | Non 
 ca-ES | Catalan (Espagne) | Non  | Non  | Non 
 da-DK | Danois (Danemark) | Non  | Non  | Non 
 de-DE | Allemand (Allemagne) | Oui | Oui | Non 
 en-AU | Anglais (Australie) | Non  | OUI | Oui
 en-CA | Anglais (Canada) | Non  | OUI | Oui
 en-GB | Anglais (Royaume-Uni) | Non  | OUI | Oui
 en-IN | Anglais (Inde) | Oui | OUI | Oui
 en-NZ | Anglais (Nouvelle-Zélande) | Non  | OUI | Oui  
 fr-FR | Anglais (États-Unis) | Oui | OUI | Oui
 es-ES | Espagnol (Espagne) | Non  | Oui | Non 
 es-MX | Espagnol (Mexique) | Non  | Oui | Non 
 fi-FI | Finnois (Finlande) | Non  | Non  | Non 
 fr-CA | Français (Canada) | Non  | Oui | Non 
 fr-FR | Français (France) | Oui | Oui | Non 
 hi-IN | Hindi (Inde) | Non  | Oui | Non 
 it-IT | Italien (Italie) | Oui | Oui | Non 
 ja-JP | Japonais (Japon) | Non  | Oui | Non 
 ko-KR | Coréen (Corée) | Non  | Oui | Non 
 nb-NO | Norvégien( Bokmål) (Norvège) | Non  | Non  | Non 
 nl-NL | Néerlandais (Pays-Bas) | Non  | Oui | Non 
 pl-PL | Polonais (Pologne) | Non  | Non  | Non 
 pt-br | Portugais (Brésil) | Non  | Oui | Non 
 pt-PT | Portugais (Portugal) | Non  | Oui | Non 
 ru-RU | Russe (Russie) | Oui | Oui | Non 
 sv-SE | Suédois (Suède) | Non  | Non  | Non 
 zh-CN | Chinois (mandarin, simplifié) | Oui | Oui | Non 
 zh-HK | Chinois (mandarin, traditionnel) | Non  | Oui | Non 
 zh-TW | Chinois (mandarin, taïwanais) | Non  | Oui | Non 
 th-TH | Thaï (Thaïlande) | Non  | Non  | Non 


## <a name="text-to-speech"></a>Synthèse vocale

L’API Synthèse vocale offre les voix suivantes, chacune d’elles prenant en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux.

Paramètres régionaux | Langage | Sexe | Mappage du nom du service
-------|----------|---------|--------------------
ar-EG\* | Arabe (Égypte) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-EG, Hoda) »
ar-SA | Arabe (Arabie saoudite) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-SA, Naayf) »
bg-BG | Bulgare | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (bg-BG, Ivan) »
ca-ES | Catalan (Espagne) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ca-ES, HerenaRUS) »
cs-CZ | Tchèque | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (cs-CZ, Jakub) »
cs-CZ | Tchèque | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (cs-CZ, Vit) »
da-DK | Danois | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (da-DK, HelleRUS) »
de-AT | Allemand (Autriche) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-AT, Michael) »
de-CH | Allemand (Suisse) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-CH, Karsten) »
de-DE | Allemand (Allemagne) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Hedda) »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, HeddaRUS) »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Stefan, Apollo) »
el-GR | Grec | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (el-GR, Stefanos) »
en-AU | Anglais (Australie) | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-AU, Catherine) »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-AU, HayleyRUS) »
en-CA | Anglais (Canada) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, Linda) »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, HeatherRUS) »
en-GB | Anglais (Royaume-Uni) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, Susan, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, HazelRUS) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, George, Apollo) »
en-IE | Anglais (Irlande) |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IE, Sean) »
en-IE | Anglais (Irlande) |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IE, Shaun) »
en-IN | Anglais (Inde) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Heera, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, PriyaRUS) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Ravi, Apollo) »
fr-FR | Anglais (US) |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, ZiraRUS) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, JessaRUS) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, BenjaminRUS) »
| | |Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Jessa24kRUS) »
| | |Masculin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Guy24kRUS) »
es-ES | Espagnol (Espagne) |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Laura, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, HelenaRUS) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Pablo, Apollo) »
es-MX | Espagnol (Mexique) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, HildaRUS) »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, Raul, Apollo) »
fi-FI | Finnois | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fi-FI, HeidiRUS) »
fr-CA | Français (Canada) |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, Caroline) »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, HarmonieRUS) »
fr-CH | Français (Suisse)|Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CH, Guillaume) »
fr-FR | Français (France)|Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Julie, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, HortenseRUS) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Paul, Apollo) »
he-IL| Hébreu (Israël) | Masculin| « Voix de synthèse vocale pour le service Speech Microsoft Server (he-IL, Asaf) »
hi-IN | Hindi (Inde) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana) »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Hemant) »
hr-HR | Croate | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hr-HR, Matej) »
hu-HU | Hongrois | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hu-HU, Szabolcs) »
id-ID | Indonésien| Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (id-ID, Andika) »
it-IT | Italien |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (it-IT, Cosimo, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (it-IT, LuciaRUS) »
ja-JP | Japonais |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ayumi, Apollo) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ichiro, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, HarukaRUS) »
ko-KR | Coréen |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ko-KR, HeamiRUS) »
ms-MY | Malais | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ms-MY, Rizwan) »
nb-NO | Norvégien | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (nb-NO, HuldaRUS) »
nl-NL | Néerlandais | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (nl-NL, HannaRUS) »
pl-PL | Polonais | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pl-PL, PaulinaRUS) »
pt-br | Portugais (Brésil) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, HeloisaRUS) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, Daniel, Apollo) »
pt-PT | Portugais (Portugal) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-PT, HeliaRUS) »
ro-RO | Roumain | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ro-RO, Andrei) »
ru-RU |Russe| Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Irina, Apollo) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Pavel, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (ru-RU, Pavel, Apollo) »
sk-SK | Slovaque|Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (sk-SK, Filip) »
sl-SI | Slovène|Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (sl-SI, Lado) »
sv-SE | Suédois|Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (sv-SE, HedvigRUS) »
ta-IN | Tamoul (Inde) |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ta-IN, Valluvar) »
te-IN | Télougou (Inde) |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (te-IN, Chitra) »
th-TH | Thaï|Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (th-TH, Pattara) »
tr-TR |Turc| Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (tr-TR, SedaRUS) »
vi-VN | Vietnamien|Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (vi-VN, An) »
zh-CN | Chinois (continent)|Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, HuihuiRUS) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Yaoyao, Apollo) »
| | |Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Kangkang, Apollo) »
zh-HK | Chinois (Hong Kong R.A.S.)|Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Tracy, Apollo) »
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, TracyRUS) »
| || Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Danny, Apollo) »
zh-TW | Chinois (Taïwan)|Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Yating, Apollo) »
| || Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, HanHanRUS) »
| || Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Zhiwei, Apollo) »

\* *ar-EG prend en charge l’arabe standard moderne (MSA).*

### <a name="customization"></a>Personnalisation

La personnalisation vocale est disponible pour l’anglais américain (en-US), le chinois continental (zh-CN) et l’italien (it-IT).

> [!NOTE]
> L’entraînement de la voix italienne commence par un jeu de données de plus de 2 000 énoncés. Les modèles bilingues chinois-anglais sont également pris en charge avec un premier jeu de données de plus de 2 000 énoncés.

## <a name="speech-translation"></a>Traduction vocale

L’API **Traduction vocale** prend en charge différentes langues pour la traduction de parole en parole et de parole en texte. La langue source doit toujours figurer dans le tableau des langues vocales suivant. Les langues cibles disponibles dépendent selon que cible de la traduction est de la parole ou du texte.

### <a name="speech-languages"></a>Langues de synthèse vocale

| Langue de synthèse vocale   | Code de langue |
|:----------- |-|
| Arabe (standard moderne)      | `ar` |
| Chinois (mandarin)      | `zh` |
| Français      | `en` |
| Français      | `fr` |
| Allemand      | `de` |
| Italien      | `it` |
| Japonais      | `jp` |
| Portugais (Brésil)     | `pt` |
| Russe      | `ru` |
| Espagnol      |  `es` |

### <a name="text-languages"></a>Langues de texte

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


## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
