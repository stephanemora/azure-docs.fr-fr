---
title: Prise en charge linguistique - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Les services Azure Speech prennent en charge de nombreuses langues, que ce soit pour la reconnaissance vocale, la synthèse vocale ou la traduction vocale. Cet article fournit une liste complète des langues prise en charge par service.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9b8e12220f220bd8183675d13e25bdcab02707fd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020853"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Prise en charge de langue et région pour les Services de reconnaissance vocale

Les langues prises en charge varient selon les fonctions des services Speech. Les tableaux suivants récapitulent la prise en charge linguistique.

## <a name="speech-to-text"></a>Reconnaissance vocale

L’API de reconnaissance vocale Microsoft prend en charge les langues suivantes. Différents niveaux de personnalisation sont disponibles pour chaque langue.

  Code | Langage | [Adaptation acoustique](how-to-customize-acoustic-models.md) | [Adaptation de langage](how-to-customize-language-model.md) | [Adaptation de prononciation](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | Arabe (Égypte), standard moderne | Non  | Oui | Non 
 ca-ES | Catalan | Non  | Non  | Non 
 da-DK | Danois (Danemark) | Non  | Non  | Non 
 de-DE | Allemand (Allemagne) | Oui | Oui | Non 
 en-AU | Anglais (Australie) | Non  | OUI | Oui
 en-CA | Anglais (Canada) | Non  | OUI | Oui
 en-GB | Anglais (Royaume-Uni) | Non  | OUI | Oui
 en-IN | Anglais (Inde) | Oui | OUI | Oui
 en-NZ | Anglais (Nouvelle-Zélande) | Non  | OUI | Oui  
 en-US | Anglais (États-Unis) | Oui | OUI | Oui
 es-ES | Espagnol (Espagne) | Oui | Oui | Non 
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
 pt-br | Portugais (Brésil) | Oui | Oui | Non 
 pt-PT | Portugais (Portugal) | Non  | Oui | Non 
 ru-RU | Russe (Russie) | Oui | Oui | Non 
 sv-SE | Suédois (Suède) | Non  | Non  | Non 
 zh-CN | Chinois (mandarin, simplifié) | Oui | Oui | Non 
 zh-HK | Chinois (Cantonais, traditionnel) | Non  | Oui | Non 
 zh-TW | Chinois (mandarin, taïwanais) | Non  | Oui | Non 
 th-TH | Thaï (Thaïlande) | Non  | Non  | Non 


## <a name="text-to-speech"></a>Synthèse vocale

L’API REST de synthèse vocale prend en charge ces voix qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux.

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, visitez la page [Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voix neurales

La synthèse vocale neuronale est un nouveau type de synthèse vocale alimentée par les réseaux neuronaux profonds. Lorsque vous utilisez une voix neuronale, il est presque impossible de distinguer la voix synthétisée des enregistrements humains.

Les voix neuronales peuvent être utilisées pour rendre les interactions avec les chatbots et les assistants virtuels plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Grâce à la prosodie naturelle quasi humaine et à la bonne articulation des mots, les voix neuronales réduisent considérablement la fatigue d’écoute des utilisateurs qui interagissent avec les systèmes d’intelligence artificielle.

Pour obtenir la liste complète des voix neuronales et la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

Paramètres régionaux | Langage | Sexe | Mappage de nom de service complet | Nom court vocale
--------|----------|--------|---------|------------
de-DE | Allemand (Allemagne) | Féminin | « Microsoft Server Speech Text pour la synthèse vocale (de-DE, KatjaNeural) » | « de-DE-KatjaNeural »
en-US | Anglais (US) | Masculin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, GuyNeural) » | « en-US-GuyNeural »
en-US | Anglais (US) | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, JessaNeural) » | « en-US-JessaNeural »
it-IT | Italien (Italie) | Féminin |« Microsoft Server Speech Text pour la synthèse vocale (it-IT, ElsaNeural) » | « it-IT-ElsaNeural »
zh-CN | Chinois (continent) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, XiaoxiaoNeural) » | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Vous pouvez utiliser le mappage de nom de service complet ou le nom court vocale dans vos demandes de synthèse vocale.

### <a name="standard-voices"></a>Voix standard

Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, ce qui vous permet de convertir le texte en parole synthétisée. Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

Paramètres régionaux | Langage | Sexe | Mappage de nom de service complet | Nom court vocale
-------|----------|---------|----------|----------
ar-EG\* | Arabe (Égypte) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-EG, Hoda) » | "ar-EG-Hoda"
ar-SA | Arabe (Arabie saoudite) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-SA, Naayf) » | « ar-SA-Naayf »
bg-BG | Bulgare | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (bg-BG, Ivan) » | "bg-BG-Ivan"
ca-ES | Catalan (Espagne) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ca-ES, HerenaRUS) » | « autorité de certification-ES-HerenaRUS »
cs-CZ | Tchèque | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (cs-CZ, Jakub) » | "cs-CZ-Jakub"
da-DK | Danois | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (da-DK, HelleRUS) » | « da-DK-HelleRUS »
de-AT | Allemand (Autriche) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-AT, Michael) » | « de-AT-Michael »
de-CH | Allemand (Suisse) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-CH, Karsten) » | "de-CH-Karsten"
de-DE | Allemand (Allemagne) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Hedda) » | « de-DE-Hedda »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, HeddaRUS) » | « de-DE-HeddaRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Stefan, Apollo) » | "de-DE-Stefan-Apollo"
el-GR | Grec | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (el-GR, Stefanos) » | « el-GR-Stefanos »
en-AU | Anglais (Australie) | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-AU, Catherine) » | « en-AU-Catherine »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-AU, HayleyRUS) » | « en-AU-HayleyRUS »
en-CA | Anglais (Canada) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, Linda) » | « fr-CA-Linda »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, HeatherRUS) » | « fr-CA-HeatherRUS »
en-GB | Anglais (Royaume-Uni) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, Susan, Apollo) » | « en-GB-Susan-Apollo »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, HazelRUS) » | « en-GB-HazelRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, George, Apollo) » | « en-GB-George-Apollo »
en-IE | Anglais (Irlande) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IE, Sean) » | « fr-IE-Sean »
en-IN | Anglais (Inde) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Heera, Apollo) » | « en-IN-Heera-Apollo »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, PriyaRUS) » | « en-IN-PriyaRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Ravi, Apollo) » | « en-IN-Ravi-Apollo »
en-US | Anglais (US) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, ZiraRUS) » | « en-US-ZiraRUS »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, JessaRUS) » | « en-US-JessaRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, BenjaminRUS) » | « en-US-BenjaminRUS »
| | | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Jessa24kRUS) » | « en-US-Jessa24kRUS »
| | | Masculin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Guy24kRUS) » | « en-US-Guy24kRUS »
es-ES | Espagnol (Espagne) |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Laura, Apollo) » | « es-ES-Laura-Apollo »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, HelenaRUS) » | « es-ES-HelenaRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Pablo, Apollo) » | « es-ES-Pablo-Apollo »
es-MX | Espagnol (Mexique) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, HildaRUS) » | "es-MX-HildaRUS"
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, Raul, Apollo) » | « es-MX-Raul-Apollo »
fi-FI | Finnois | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fi-FI, HeidiRUS) » | « fi-FI-HeidiRUS »
fr-CA | Français (Canada) |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, Caroline) » | « fr-CA-Caroline »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, HarmonieRUS) » | « fr-CA-HarmonieRUS »
fr-CH | Français (Suisse)| Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CH, Guillaume) » | « fr-CH-Guillaume »
fr-FR | Français (France)| Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Julie, Apollo) » | « fr-FR-Julie-Apollo »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, HortenseRUS) » | « fr-FR-HortenseRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Paul, Apollo) » | « fr-FR-Paul-Apollo »
he-IL| Hébreu (Israël) | Masculin| « Voix de synthèse vocale pour le service Speech Microsoft Server (he-IL, Asaf) » | "he-IL-Asaf"
hi-IN | Hindi (Inde) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana, Apollo) » | "hi-IN-Kalpana-Apollo"
| | |Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana) » | "hi-IN-Kalpana"
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Hemant) » | « hi-IN-Hemant »
hr-HR | Croate | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hr-HR, Matej) » | « hr-HR-Matej »
hu-HU | Hongrois | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (hu-HU, Szabolcs) » | « hu-HU-Szabolcs »
id-ID | Indonésien| Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (id-ID, Andika) » | "id-ID-Andika"
it-IT | Italien | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (it-IT, Cosimo, Apollo) » | "it-IT-Cosimo-Apollo"
| | | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (it-IT, LuciaRUS) » | « it-IT-LuciaRUS »
ja-JP | Japonais | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ayumi, Apollo) » | "ja-JP-Ayumi-Apollo"
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ichiro, Apollo) » | « ja-JP-AV-Apollo »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, HarukaRUS) » | "ja-JP-HarukaRUS"
ko-KR | Coréen | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ko-KR, HeamiRUS) » | « ko-KR-HeamiRUS »
ms-MY | Malais | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ms-MY, Rizwan) » | « ms-mon-Rizwan »
nb-NO | Norvégien | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (nb-NO, HuldaRUS) » | « nb-NO-HuldaRUS »
nl-NL | Néerlandais | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (nl-NL, HannaRUS) » | "nl-NL-HannaRUS"
pl-PL | Polonais | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pl-PL, PaulinaRUS) » | « pl-PL-PaulinaRUS »
pt-br | Portugais (Brésil) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, HeloisaRUS) » | « pt-BR-HeloisaRUS »
| | | Masculin |« Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, Daniel, Apollo) » | "pt-BR-Daniel-Apollo"
pt-PT | Portugais (Portugal) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-PT, HeliaRUS) » | "pt-PT-HeliaRUS"
ro-RO | Roumain | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ro-RO, Andrei) » | "ro-RO-Andrei"
ru-RU |Russe| Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Irina, Apollo) » | « ru-RU-Irina-Apollo »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Pavel, Apollo) » | "ru-RU-Pavel-Apollo"
| | | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (ru-RU, Pavel, Apollo) » | ru-RU-EkaterinaRUS
sk-SK | Slovaque | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (sk-SK, Filip) » | "sk-SK-Filip"
sl-SI | Slovène | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (sl-SI, Lado) » | "sl-SI-Lado"
sv-SE | Suédois | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (sv-SE, HedvigRUS) » | « sv-SE-HedvigRUS »
ta-IN | Tamoul (Inde) | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (ta-IN, Valluvar) » | « ta-IN-Valluvar »
te-IN | Télougou (Inde) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (te-IN, Chitra) » | « te-IN-Chitra »
th-TH | Thaï | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (th-TH, Pattara) » | « th-TH-Pattara »
tr-TR | Turc | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (tr-TR, SedaRUS) » | « tr-TR-SedaRUS »
vi-VN | Vietnamien | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (vi-VN, An) » | « vi-VN-An »
zh-CN | Chinois (continent) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, HuihuiRUS) » | "zh-CN-HuihuiRUS"
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Yaoyao, Apollo) » | "zh-CN-Yaoyao-Apollo"
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Kangkang, Apollo) » | "zh-CN-Kangkang-Apollo"
zh-HK | Chinois (Hong Kong R.A.S.) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Tracy, Apollo) » | « zh-HK-Tracy mon-Apollo »
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, TracyRUS) » | « zh-HK-TracyRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Danny, Apollo) » | « zh-HK-Delphine-Apollo »
zh-TW | Chinois (Taïwan) | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Yating, Apollo) » | "zh-TW-Yating-Apollo"
| | | Féminin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, HanHanRUS) » | « zh-TW-HanHanRUS »
| | | Masculin | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Zhiwei, Apollo) » | "zh-TW-Zhiwei-Apollo"

\* *ar-EG prend en charge l’arabe standard moderne (MSA).*

> [!NOTE]
> Vous pouvez utiliser le mappage de nom de service complet ou le nom court vocale dans vos demandes de synthèse vocale.

### <a name="customization"></a>Personnalisation

Personnalisation de la voix est disponible pour fr-fr, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, pt-BR et zh-CN. Sélectionnez les paramètres régionaux approprié qui correspond aux données d’apprentissage pour former un modèle vocal personnalisé. Par exemple, si les données de l’enregistrement que vous avez sont parlées en anglais avec un accent British, sélectionnez en-GB.  

> [!NOTE]
> Nous ne gèrent pas l’apprentissage du modèle de bilingues dans vocal personnalisé, à l’exception de la bilingues chinois-anglais. Sélectionnez « Bilingue chinois-anglais » si vous souhaitez former une voix chinois qui parlent anglais également. Apprentissage de la voix dans tous les paramètres régionaux commence par un jeu de données de plus de 2 000 énoncés, à l’exception de l’en-US et zh-CN, où vous pouvez commencer par n’importe quelle taille de données d’apprentissage.

## <a name="speech-translation"></a>Traduction vocale

L’API **Traduction vocale** prend en charge différentes langues pour la traduction de parole en parole et de parole en texte. La langue source doit toujours provenir du tableau des langues de reconnaissance vocale. Les langues cibles disponibles dépendent selon que cible de la traduction est de la parole ou du texte. Vous pouvez traduire les conversations entrantes dans plus de [60 langues](https://www.microsoft.com/translator/business/languages/). Un sous-ensemble de ces langues est disponible pour la [synthèse vocale](language-support.md#text-languages).

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

* [Obtenir votre abonnement d’essai gratuit à Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
