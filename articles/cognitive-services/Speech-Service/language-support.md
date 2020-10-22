---
title: Prise en charge des langues – Service Speech
titleSuffix: Azure Cognitive Services
description: Le service Speech prend en charge de nombreuses langues, que ce soit pour la reconnaissance vocale, la synthèse vocale ou la traduction vocale. Cet article fournit une liste complète des langues prise en charge, par fonctionnalité de service.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 7d5dd7d2d0aa3d10f439b5cf3604e2d595456f86
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172251"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Prise en charge des langues et de la voix pour le service Speech

La prise en charge des langues varie selon les fonctionnalités du service Speech. Les tableaux suivants résument la prise en charge des langues pour les offres de service [Reconnaissance vocale](#speech-to-text), [Synthèse vocale](#text-to-speech) et [Traduction vocale](#speech-translation).

## <a name="speech-to-text"></a>Reconnaissance vocale

Le Kit de développement logiciel (SDK) et l’API REST Speech de Microsoft prennent en charge les langages suivants (paramètres régionaux). 

Afin d'améliorer la précision, la personnalisation est proposée pour une partie des langues en chargeant des **transcriptions audio + étiquetées à la main** ou des **textes associés : Phrases.** Pour en savoir plus sur la personnalisation, consultez [Bien démarrer avec Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Paramètres régionaux  | Langage                          | Personnalisations                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabe (Émirats arabes unis)     | Modèle de langage                                    |
| `ar-BH` | Arabe (Bahreïn), standard moderne | Modèle de langage                                    |
| `ar-EG` | Arabe (Égypte)                    | Modèle de langage                                    |
| `ar-IQ` | Arabe (Irak)                     | Modèle de langage                                    |
| `ar-JO` | Arabe (Jordanie)                   | Modèle de langage                                    |
| `ar-KW` | Arabe (Koweït)                   | Modèle de langage                                    |
| `ar-LB` | Arabe (Liban)                  | Modèle de langage                                    |
| `ar-OM` | Arabe (Oman)                     | Modèle de langage                                    |
| `ar-QA` | Arabe (Qatar)                    | Modèle de langage                                    |
| `ar-SA` | Arabe (Arabie saoudite)             | Modèle de langage                                    |
| `ar-SY` | Arabe (Syrie)                    | Modèle de langage                                    |
| `bg-BG` | Bulgare (Bulgarie)              | Modèle de langage                                    |
| `ca-ES` | Catalan (Espagne)                   | Modèle de langage                                    |
| `cs-CZ` | Tchèque (République tchèque)            | Modèle de langage                                    | 
| `da-DK` | Danois (Danemark)                  | Modèle de langage                                    |
| `de-DE` | Allemand (Allemagne)                  | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `el-GR` | Grec (Grèce)                    | Modèle de langage                                    |
| `en-AU` | Anglais (Australie)               | Modèle acoustique<br>Modèle de langage                  |
| `en-CA` | Anglais (Canada)                  | Modèle acoustique<br>Modèle de langage                  |
| `en-GB` | Anglais (Royaume-Uni)          | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `en-HK` | Anglais (Hong Kong)               | Modèle de langage                                    | 
| `en-IE` | Anglais (Irlande)                 | Modèle de langage                                    | 
| `en-IN` | Anglais (Inde)                   | Modèle acoustique<br>Modèle de langage                  |
| `en-NZ` | Anglais (Nouvelle-Zélande)             | Modèle acoustique<br>Modèle de langage                  |
| `en-PH` | Anglais (Philippines)             | Modèle de langage                                    | 
| `en-SG` | Anglais (Singapour)               | Modèle de langage                                    | 
| `en-US` | Anglais (États-Unis)           | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `en-ZA` | Anglais (Afrique du Sud)            | Modèle de langage                                    | 
| `es-AR` | Espagnol (Argentine)               | Modèle de langage                                    | 
| `es-BO` | Espagnol (Bolivie)                 | Modèle de langage                                    | 
| `es-CL` | Espagnol (Chili)                   | Modèle de langage                                    | 
| `es-CO` | Espagnol (Colombie)                | Modèle de langage                                    | 
| `es-CR` | Espagnol (Costa Rica)              | Modèle de langage                                    | 
| `es-CU` | Espagnol (Cuba)                    | Modèle de langage                                    | 
| `es-DO` | Espagnol (République dominicaine)      | Modèle de langage                                    | 
| `es-EC` | Espagnol (Équateur)                 | Modèle de langage                                    | 
| `es-ES` | Espagnol (Espagne)                   | Modèle acoustique<br>Modèle de langage                  |
| `es-GT` | Espagnol (Guatemala)               | Modèle de langage                                    | 
| `es-HN` | Espagnol (Honduras)                | Modèle de langage                                    | 
| `es-MX` | Espagnol (Mexique)                  | Modèle acoustique<br>Modèle de langage                  |
| `es-NI` | Espagnol (Nicaragua)               | Modèle de langage                                    | 
| `es-PA` | Espagnol (Panama)                  | Modèle de langage                                    | 
| `es-PE` | Espagnol (Pérou)                    | Modèle de langage                                    | 
| `es-PR` | Espagnol (Porto Rico)             | Modèle de langage                                    | 
| `es-PY` | Espagnol (Paraguay)                | Modèle de langage                                    | 
| `es-SV` | Espagnol (Salvador)             | Modèle de langage                                    | 
| `es-US` | Espagnol (États-Unis)                     | Modèle de langage                                    | 
| `es-UY` | Espagnol (Uruguay)                 | Modèle de langage                                    | 
| `es-VE` | Espagnol (Venezuela)               | Modèle de langage                                    |
| `et-EE` | Estonien (Estonie)                 | Modèle de langage                                    | 
| `fi-FI` | Finnois (Finlande)                 | Modèle de langage                                    |
| `fr-CA` | Français (Canada)                   | Modèle acoustique<br>Modèle de langage                  |
| `fr-FR` | Français (France)                   | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `ga-IE` | Irlandais (Irlande)                    | Modèle de langage                                    |
| `gu-IN` | Goudjrati (Inde)                 | Modèle de langage                                    |
| `hi-IN` | Hindi (Inde)                     | Modèle acoustique<br>Modèle de langage                  |
| `hr-HR` | Croate (Croatie)                | Modèle de langage                                    |
| `hu-HU` | Hongrois (Hongrie)               | Modèle de langage                                    | 
| `it-IT` | Italien (Italie)                   | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `ja-JP` | Japonais (Japon)                  | Modèle de langage                                    |
| `ko-KR` | Coréen (Corée)                    | Modèle de langage                                    |
| `lt-LT` | Lituanien (Lituanie)            | Modèle de langage                                    |
| `lv-LV` | Letton (Lettonie)                  | Modèle de langage                                    |
| `mr-IN` | Marathi (Inde)                   | Modèle de langage                                    |
| `mt-MT` | Maltais (Malte)                    | Modèle de langage                                    |
| `nb-NO` | Norvégien( Bokmål) (Norvège)       | Modèle de langage                                    |
| `nl-NL` | Néerlandais (Pays-Bas)               | Modèle de langage                                    |
| `pl-PL` | Polonais (Pologne)                   | Modèle de langage                                    |
| `pt-BR` | Portugais (Brésil)               | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `pt-PT` | Portugais (Portugal)             | Modèle de langage                                    |
| `ro-RO` | Roumain (Roumanie)                | Modèle de langage                                    |
| `ru-RU` | Russe (Russie)                  | Modèle acoustique<br>Modèle de langage                  |
| `sk-SK` | Slovaque (Slovaquie)                 | Modèle de langage                                    |
| `sl-SI` | Slovène (Slovénie)              | Modèle de langage                                    |
| `sv-SE` | Suédois (Suède)                  | Modèle de langage                                    |
| `ta-IN` | Tamoul (Inde)                     | Modèle de langage                                    |
| `te-IN` | Télougou (Inde)                    | Modèle de langage                                    |
| `th-TH` | Thaï (Thaïlande)                   | Modèle de langage                                    |
| `tr-TR` | Turc (Turquie)                  | Modèle de langage                                    |
| `zh-CN` | Chinois (mandarin, simplifié)    | Modèle acoustique<br>Modèle de langage                  |
| `zh-HK` | Chinois (cantonais, traditionnel)  | Modèle de langage                                    |
| `zh-TW` | Chinois (mandarin, taïwanais)      | Modèle de langage                                    |

## <a name="text-to-speech"></a>Synthèse vocale

Le Kit de développement logiciel (SDK) Speech de Microsoft et les API REST prennent en charge ces voix qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux. Vous pouvez également obtenir la liste complète des langues et des voix prises en charge pour chaque région/point de terminaison spécifique via l’API [voices/list](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, visitez la page [Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voix neurales

La synthèse vocale neuronale est un nouveau type de synthèse vocale alimentée par les réseaux neuronaux profonds. Lorsque vous utilisez une voix neuronale, il est presque impossible de distinguer la voix synthétisée des enregistrements humains.

Les voix neurales peuvent être utilisées pour rendre les interactions avec les chatbots et des assistants vocaux plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Grâce à la prosodie naturelle quasi humaine et à la bonne articulation des mots, les voix neuronales réduisent considérablement la fatigue d’écoute des utilisateurs qui interagissent avec les systèmes d’intelligence artificielle.

Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

|Paramètres régionaux  | Langage            | Sexe | Nom de la voix | Prise en charge du style |
|--|--|--|--|--|
| `ar-EG` | Arabe (Égypte) | Female | `ar-EG-SalmaNeural` | Général |
| `ar-SA` | Arabe (Arabie saoudite) | Female | `ar-SA-ZariyahNeural` | Général |
| `bg-BG` <sup>Nouveau</sup> | Bulgare (Bulgarie) | Female | `bg-BG-KalinaNeural` | Général |
| `ca-ES` | Catalan (Espagne) | Female | `ca-ES-AlbaNeural` | Général |
| `cs-CZ` <sup>Nouveau</sup> | Tchèque (Tchéquie)  | Female | `cs-CZ-VlastaNeural` | Général |
| `da-DK` | Danois (Danemark) | Female | `da-DK-ChristelNeural` | Général |
| `de-AT` <sup>Nouveau</sup> | Allemand (Autriche) | Female | `de-AT-IngridNeural` | Général |
| `de-CH` <sup>Nouveau</sup> | Allemand (Suisse) | Female | `de-CH-LeniNeural` | Général |
| `de-DE` | Allemand (Allemagne) | Female | `de-DE-KatjaNeural` | Général |
| `de-DE` <sup>Nouveau</sup> | Allemand (Allemagne) | Male | `de-DE-ConradNeural` | Général |
| `el-GR` <sup>Nouveau</sup> | Grec (Grèce) | Female | `el-GR-AthinaNeural` | Général |
| `en-AU` | Anglais (Australie) | Female | `en-AU-NatashaNeural` | Général |
| `en-AU` <sup>Nouveau</sup> | Australien (Australie) | Male | `en-AU-WilliamNeural` | Général |
| `en-CA` | Anglais (Canada) | Female | `en-CA-ClaraNeural` | Général |
| `en-GB` | Anglais (Royaume-Uni) | Female | `en-GB-LibbyNeural` | Général |
| `en-GB` | Anglais (Royaume-Uni) | Female | `en-GB-MiaNeural` | Général |
| `en-GB` <sup>Nouveau</sup> | Anglais (Royaume-Uni) | Male | `en-GB-RyanNeural` | Général |
| `en-IE` <sup>Nouveau</sup> | Anglais irlandais (Irlande) | Female | `en-IE-EmilyNeural` | Général |
| `en-IN` | Anglais (Inde) | Female | `en-IN-NeerjaNeural` | Général |
| `en-US` | Anglais (États-Unis) | Female | `en-US-AriaNeural` | Général, plusieurs styles de voix sont disponibles |
| `en-US` | Anglais (États-Unis) | Male | `en-US-GuyNeural` | Général |
| `en-US` <sup>Nouveau</sup> | Anglais (États-Unis) | Female | `en-US-JennyNeural` | Général, plusieurs styles de voix sont disponibles |
| `es-ES` | Espagnol (Espagne) | Female | `es-ES-ElviraNeural` | Général |
| `es-ES` <sup>Nouveau</sup> | Espagnol (Espagne) | Male | `es-ES-AlvaroNeural` | Général |
| `es-MX` | Espagnol (Mexique) | Female | `es-MX-DaliaNeural` | Général |
| `es-MX` <sup>Nouveau</sup> | Espagnol (Mexique) | Male | `es-MX-JorgeNeural` | Général |
| `fi-FI` | Finnois (Finlande) | Female | `fi-FI-NooraNeural` | Général |
| `fr-CA` | Français (Canada) | Female | `fr-CA-SylvieNeural` | Général |
| `fr-CA` <sup>Nouveau</sup> | Français (Canada) | Male | `fr-CA-JeanNeural` | Général |
| `fr-CH` <sup>Nouveau</sup> | Français (Suisse) | Female | `fr-CH-ArianeNeural` | Général |
| `fr-FR` | Français (France) | Female | `fr-FR-DeniseNeural` | Général |
| `fr-FR` <sup>Nouveau</sup> | Français (France) | Male | `fr-FR-HenriNeural` | Général |
| `he-IL` <sup>Nouveau</sup> | Hébreu (Israël) | Female | `he-IL-HilaNeural` | Général |
| `hi-IN` | Hindi (Inde) | Female | `hi-IN-SwaraNeural` | Général |
| `hr-HR` <sup>Nouveau</sup> | Croate (Croatie) | Female | `hr-HR-GabrijelaNeural` | Général |
| `hu-HU` <sup>Nouveau</sup> | Hongrois (Hongrie) | Female | `hu-HU-NoemiNeural` | Général |
| `id-ID` <sup>Nouveau</sup> | Indonésien (Indonésie) | Male | `id-ID-ArdiNeural` | Général |
| `it-IT` | Italien (Italie) | Female | `it-IT-ElsaNeural` | Général |
| `it-IT` <sup>Nouveau</sup> | Italien (Italie) | Female | `it-IT-IsabellaNeural` | Général |
| `it-IT` <sup>Nouveau</sup> | Italien (Italie) | Male | `it-IT-DiegoNeural` | Général |
| `ja-JP` | Japonais (Japon) | Female | `ja-JP-NanamiNeural` | Général |
| `ja-JP` <sup>Nouveau</sup> | Japonais (Japon) | Male | `ja-JP-KeitaNeural` | Général |
| `ko-KR` | Coréen (Corée) | Female | `ko-KR-SunHiNeural` | Général |
| `ko-KR` <sup>Nouveau</sup> | Coréen (Corée) | Male | `ko-KR-InJoonNeural` | Général |
| `ms-MY` <sup>Nouveau</sup> | Malais (Malaisie) | Female | `ms-MY-YasminNeural` | Général |
| `nb-NO` | Norvégien, Bokmål (Norvège) | Female | `nb-NO-IselinNeural` | Général |
| `nl-NL` | Néerlandais (Pays-Bas) | Female | `nl-NL-ColetteNeural` | Général |
| `pl-PL` | Polonais (Pologne) | Female | `pl-PL-ZofiaNeural` | Général |
| `pt-BR` | Portugais (Brésil) | Female | `pt-BR-FranciscaNeural` | Général, plusieurs styles de voix sont disponibles |
| `pt-BR` <sup>Nouveau</sup> | Portugais brésilien (Brésil) | Male | `pt-BR-AntonioNeural` | Général |
| `pt-PT` | Portugais (Portugal) | Female | `pt-PT-FernandaNeural` | Général |
| `ro-RO` <sup>Nouveau</sup> | Roumain (Roumanie) | Female | `ro-RO-AlinaNeural` | Général |
| `ru-RU` | Russe (Russie) | Female | `ru-RU-DariyaNeural` | Général |
| `sk-SK` <sup>Nouveau</sup> | Slovaque (Slovaquie) | Female | `sk-SK-ViktoriaNeural` | Général |
| `sl-SI` <sup>Nouveau</sup> | Slovène (Slovénie) | Female | `sl-SI-PetraNeural` | Général |
| `sv-SE` | Suédois (Suède) | Female | `sv-SE-HilleviNeural` | Général |
| `ta-IN` <sup>Nouveau</sup> | Tamoul (Inde) | Female | `ta-IN-PallaviNeural` | Général |
| `te-IN` <sup>Nouveau</sup> | Télougou (Inde) | Female | `te-IN-ShrutiNeural` | Général |
| `th-TH` | Thaï (Thaïlande) | Female | `th-TH-AcharaNeural` | Général |
| `th-TH` <sup>Nouveau</sup> | Thaï (Thaïlande) | Female | `th-TH-PremwadeeNeural` | Général |
| `tr-TR` | Turc (Turquie) | Female | `tr-TR-EmelNeural` | Général |
| `vi-VN` <sup>Nouveau</sup> | Vietnamien (Vietnam) | Female | `vi-VN-HoaiMyNeural` | Général |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Female | `zh-CN-XiaoxiaoNeural` | Général, plusieurs styles de voix sont disponibles |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Female | `zh-CN-XiaoyouNeural` | Voix d’enfant, optimisée pour raconter des histoires |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Male | `zh-CN-YunyangNeural` | Optimisée pour la lecture d’actualités, plusieurs styles de voix disponibles |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Male | `zh-CN-YunyeNeural` | Optimisée pour raconter des histoires |
| `zh-HK` | Cantonais (chinois traditionnel, Hong Kong) | Female | `zh-HK-HiuGaaiNeural` | Général |
| `zh-TW` | Mandarin (chinois traditionnel, Taïwan) | Female | `zh-TW-HsiaoYuNeural` | Général |


> [!IMPORTANT]
> La voix `en-US-JessaNeural` a basculé sur `en-US-AriaNeural`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

Pour savoir comment configurer et ajuster les voix neuronales, voir [Langage de balisage de synthèse vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Vous pouvez continuer à utiliser le mappage de nom de service complet comme « Voix Microsoft Server Speech Text to Speech (en-US, AriaNeural) » dans vos requêtes de synthèse vocale.

### <a name="standard-voices"></a>Voix standard

Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, ce qui vous permet de convertir le texte en parole synthétisée. Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

| Paramètres régionaux | Langage | Sexe | Nom de la voix |
|--|--|--|--|
| `ar-EG` | Arabe (arabe) | Female | `ar-EG-Hoda` |
| `ar-SA` | Arabe (Arabie saoudite) | Male | `ar-SA-Naayf` |
| `bg-BG` | Bulgare (Bulgarie) | Male | `bg-BG-Ivan` |
| `ca-ES` | Catalan (Espagne) | Female | `ca-ES-HerenaRUS` |
| `cs-CZ` | Tchèque (République tchèque) | Male | `cs-CZ-Jakub` |
| `da-DK` | Danois (Danemark) | Female | `da-DK-HelleRUS` |
| `de-AT` | Allemand (Autriche) | Male | `de-AT-Michael` |
| `de-CH` | Allemand (Suisse) | Male | `de-CH-Karsten` |
| `de-DE` | Allemand (Allemagne) | Female | `de-DE-HeddaRUS` |
| `de-DE` | Allemand (Allemagne) | Male | `de-DE-Stefan` |
| `el-GR` | Grec (Grèce) | Male | `el-GR-Stefanos` |
| `en-AU` | Anglais (Australie) | Female | `en-AU-Catherine` |
| `en-AU` | Anglais (Australie) | Female | `en-AU-HayleyRUS` |
| `en-CA` | Anglais (Canada) | Female | `en-CA-HeatherRUS` |
| `en-CA` | Anglais (Canada) | Female | `en-CA-Linda` |
| `en-GB` | Anglais (Royaume-Uni) | Male | `en-GB-George` |
| `en-GB` | Anglais (Royaume-Uni) | Female | `en-GB-HazelRUS` |
| `en-GB` | Anglais (Royaume-Uni) | Female | `en-GB-Susan` |
| `en-IE` | Anglais (Irlande) | Male | `en-IE-Sean` |
| `en-IN` | Anglais (Inde) | Female | `en-IN-Heera` |
| `en-IN` | Anglais (Inde) | Female | `en-IN-PriyaRUS` |
| `en-IN` | Anglais (Inde) | Male | `en-IN-Ravi` |
| `en-US` | Anglais (États-Unis) | Male | `en-US-BenjaminRUS` |
| `en-US` | Anglais (États-Unis) | Male | `en-US-GuyRUS` |
| `en-US` | Anglais (États-Unis) | Female | `en-US-JessaRUS` |
| `en-US` | Anglais (États-Unis) | Female | `en-US-ZiraRUS` |
| `es-ES` | Espagnol (Espagne) | Female | `es-ES-HelenaRUS` |
| `es-ES` | Espagnol (Espagne) | Female | `es-ES-Laura` |
| `es-ES` | Espagnol (Espagne) | Male | `es-ES-Pablo` |
| `es-MX` | Espagnol (Mexique) | Female | `es-MX-HildaRUS` |
| `es-MX` | Espagnol (Mexique) | Male | `es-MX-Raul` |
| `fi-FI` | Finnois (Finlande) | Female | `fi-FI-HeidiRUS` |
| `fr-CA` | Français (Canada) | Female | `fr-CA-Caroline` |
| `fr-CA` | Français (Canada) | Female | `fr-CA-HarmonieRUS` |
| `fr-CH` | Français (Suisse) | Male | `fr-CH-Guillaume` |
| `fr-FR` | Français (France) | Female | `fr-FR-HortenseRUS` |
| `fr-FR` | Français (France) | Female | `fr-FR-Julie` |
| `fr-FR` | Français (France) | Male | `fr-FR-Paul` |
| `he-IL` | Hébreu (Israël) | Male | `he-IL-Asaf` |
| `hi-IN` | Hindi (Inde) | Male | `hi-IN-Hemant` |
| `hi-IN` | Hindi (Inde) | Female | `hi-IN-Kalpana` |
| `hr-HR` | Croate (Croatie) | Male | `hr-HR-Matej` |
| `hu-HU` | Hongrois (Hongrie) | Male | `hu-HU-Szabolcs` |
| `id-ID` | Indonésien (Indonésie) | Male | `id-ID-Andika` |
| `it-IT` | Italien (Italie) | Male | `it-IT-Cosimo` |
| `it-IT` | Italien (Italie) | Female | `it-IT-LuciaRUS` |
| `ja-JP` | Japonais (Japon) | Female | `ja-JP-Ayumi` |
| `ja-JP` | Japonais (Japon) | Female | `ja-JP-HarukaRUS` |
| `ja-JP` | Japonais (Japon) | Male | `ja-JP-Ichiro` |
| `ko-KR` | Coréen (Corée) | Female | `ko-KR-HeamiRUS` |
| `ms-MY` | Malais (Malaisie) | Male | `ms-MY-Rizwan` |
| `nb-NO` | Norvégien (bokmål, Norvège) | Female | `nb-NO-HuldaRUS` |
| `nl-NL` | Néerlandais (Pays-Bas) | Female | `nl-NL-HannaRUS` |
| `pl-PL` | Polonais (Pologne) | Female | `pl-PL-PaulinaRUS` |
| `pt-BR` | Portugais (Brésil) | Male | `pt-BR-Daniel` |
| `pt-BR` | Portugais (Brésil) | Female | `pt-BR-HeloisaRUS` |
| `pt-PT` | Portugais (Portugal) | Female | `pt-PT-HeliaRUS` |
| `ro-RO` | Roumain (Roumanie) | Male | `ro-RO-Andrei` |
| `ru-RU` | Russe (Russie) | Female | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Russe (Russie) | Female | `ru-RU-Irina` |
| `ru-RU` | Russe (Russie) | Male | `ru-RU-Pavel` |
| `sk-SK` | Slovaque (Slovaquie) | Male | `sk-SK-Filip` |
| `sl-SI` | Slovène (Slovénie) | Male | `sl-SI-Lado` |
| `sv-SE` | Suédois (Suède) | Female | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamoul (Inde) | Male | `ta-IN-Valluvar` |
| `te-IN` | Télougou (Inde) | Female | `te-IN-Chitra` |
| `th-TH` | Thaï (Thaïlande) | Male | `th-TH-Pattara` |
| `tr-TR` | Turc (Turquie) | Female | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnamien (Vietnam) | Male | `vi-VN-An` |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Female | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Male | `zh-CN-Kangkang` |
| `zh-CN` | Mandarin (chinois simplifié, Chine) | Female | `zh-CN-Yaoyao` |
| `zh-HK` | Cantonais (chinois traditionnel, Hong Kong) | Male | `zh-HK-Danny` |
| `zh-HK` | Cantonais (chinois traditionnel, Hong Kong) | Female | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarin (chinois traditionnel, Taïwan) | Female | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarin (chinois traditionnel, Taïwan) | Female | `zh-TW-Yating` |
| `zh-TW` | Mandarin (chinois traditionnel, Taïwan) | Male | `zh-TW-Zhiwei` |

> [!IMPORTANT]
> La voix `en-US-Jessa` a basculé sur `en-US-Aria`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

> [!TIP]
> Vous pouvez continuer à utiliser le mappage de nom de service complet comme « Voix Microsoft Server Speech Text to Speech (en-US, AriaRUS) » dans vos requêtes de synthèse vocale.

### <a name="customization"></a>Personnalisation

La personnalisation vocale est disponible pour `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`et `zh-CN`. Sélectionnez les paramètres régionaux qui correspondent aux données d’apprentissage servant à entraîner un modèle vocal personnalisé. Par exemple, si vos données d’enregistrement sont en anglais avec un accent britannique, sélectionnez `en-GB`.

> [!NOTE]
> À l’exception du chinois-anglais, nous ne gérons pas l’apprentissage de modèles bilingues dans Custom Voice. Sélectionnez « Bilingue chinois-anglais » si vous souhaitez effectuer l’apprentissage d’une voix en chinois qui parle aussi anglais. Dans tous les paramètres régionaux, la formation vocale commence par un jeu de données de plus de 2 000 énoncés, à l’exception de `en-US` et `zh-CN`, pour lesquels la taille des données de formation de départ n’a pas d’importance.

## <a name="speech-translation"></a>Traduction vocale

L’API **Traduction vocale** prend en charge différentes langues pour la traduction de parole en parole et de parole en texte. La langue source doit toujours provenir du tableau des langues de reconnaissance vocale. Les langues cibles disponibles dépendent selon que cible de la traduction est de la parole ou du texte. Vous pouvez traduire les conversations entrantes dans plus de [60 langues](https://www.microsoft.com/translator/business/languages/). Un sous-ensemble de langues est disponible pour la [synthèse vocale](language-support.md#text-languages).

### <a name="text-languages"></a>Langues de texte

| Langue de texte           | Code langue |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabe                  | `ar`          |
| Bangla                  | `bn`          |
| Bosniaque (latin)         | `bs`          |
| Bulgare               | `bg`          |
| Cantonais (traditionnel) | `yue`         |
| Catalan                 | `ca`          |
| Chinois (simplifié)      | `zh-Hans`     |
| Chinois traditionnel     | `zh-Hant`     |
| Croate                | `hr`          |
| Tchèque                   | `cs`          |
| Danois                  | `da`          |
| Néerlandais                   | `nl`          |
| Anglais                 | `en`          |
| Estonien                | `et`          |
| Fidjien                  | `fj`          |
| Filipino                | `fil`         |
| Finnois                 | `fi`          |
| Français                  | `fr`          |
| Allemand                  | `de`          |
| Grec                   | `el`          |
| Goudjrati                | `gu`          |
| Créole haïtien          | `ht`          |
| Hébreu                  | `he`          |
| Hindi                   | `hi`          |
| Hmong blanc               | `mww`         |
| Hongrois               | `hu`          |
| Indonésien              | `id`          |
| Irlandais                   | `ga`          |
| Italien                 | `it`          |
| Japonais                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Coréen                  | `ko`          |
| Letton                 | `lv`          |
| Lituanien              | `lt`          |
| Malgache                | `mg`          |
| Malais                   | `ms`          |
| Malayalam               | `ml`          |
| Maltais                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norvégien               | `nb`          |
| Persan                 | `fa`          |
| Polonais                  | `pl`          |
| Portugais (Brésil)     | `pt-br`       |
| Portugais (Portugal)   | `pt-pt`       |
| Pendjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Roumain                | `ro`          |
| Russe                 | `ru`          |
| Samoan                  | `sm`          |
| Serbe (cyrillique)      | `sr-Cyrl`     |
| Serbe (latin)         | `sr-Latn`     |
| Slovaque                  | `sk`          |
| Slovène               | `sl`          |
| Espagnol                 | `es`          |
| Suédois                 | `sv`          |
| Tahitien                | `ty`          |
| Tamoul                   | `ta`          |
| Télougou                  | `te`          |
| Thaï                    | `th`          |
| Tonga                  | `to`          |
| Turc                 | `tr`          |
| Ukrainien               | `uk`          |
| Ourdou                    | `ur`          |
| Vietnamien              | `vi`          |
| Gallois                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Reconnaissance de l’orateur

Consultez le tableau suivant qui répertorie les langues prises en charge pour les différentes API Reconnaissance de l’orateur. Consultez la [vue d’ensemble](speaker-recognition-overview.md) pour plus d’informations sur Reconnaissance de l’orateur.

| Paramètres régionaux | Langage | Vérification dépendante du texte | Vérification indépendante du texte | Identification indépendante du texte |
|----|----|----|----|----|
| fr-FR | Anglais (US) | Oui | Oui | Oui |
|zh-CN    |Chinois (mandarin, simplifié)|    n/a|    Oui|    Oui|
|de-DE    |Allemand (Allemagne)    |n/a    |Oui    |Oui|
|en-GB    |Anglais (Royaume-Uni)    |n/a    |Oui    |Oui|
|fr-FR    |Français (France)    |n/a    |Oui    |Oui|
|en-AU    |Anglais (Australie)    |n/a    |Oui    |Oui|
|en-CA    |Anglais (Canada)    |n/a|    Oui|    Oui|
|fr-CA    |Français (Canada)    |n/a    |Oui|    Oui|
|it-IT    |Italien|    n/a    |Oui|    Oui|
|es-ES|    Espagnol (Espagne)    |n/a    |Oui|    Oui|
|es-MX    |Espagnol (Mexique)    |n/a|    Oui|    Oui|
|ja-JP|    Japonais    |n/a    |Oui    |Oui|
|pt-br|    Portugais (Brésil)|    n/a|    Oui|    Oui|

## <a name="next-steps"></a>Étapes suivantes

* [Créez un compte Azure gratuit](https://azure.microsoft.com/free/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
