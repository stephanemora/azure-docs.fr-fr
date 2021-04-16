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
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 52dd642c661aa60157876a89d41c771cabfe2f1d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256155"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Prise en charge des langues et de la voix pour le service Speech

La prise en charge des langues varie selon les fonctionnalités du service Speech. Les tableaux suivants résument la prise en charge des langues pour les offres de service [Reconnaissance vocale](#speech-to-text), [Synthèse vocale](#text-to-speech) et [Traduction vocale](#speech-translation).

## <a name="speech-to-text"></a>Reconnaissance vocale

Le Kit de développement logiciel (SDK) et l’API REST Speech de Microsoft prennent en charge les langages suivants (paramètres régionaux). 

Afin d'améliorer la précision, la personnalisation est proposée pour une partie des langues en chargeant des **transcriptions audio + étiquetées à la main** ou des **textes associés : Phrases.** La prise en charge de la personnalisation du modèle acoustique incluant des **données audio + transcriptions étiquetées à la main** se limite aux modèles de base spécifiques listés ci-dessous. Les autres langues et modèles de base utilisent uniquement le texte des transcriptions pour l’entraînement des modèles personnalisés, comme avec **Texte associé : Phrases.** Pour en savoir plus sur la personnalisation, consultez [Bien démarrer avec Custom Speech](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Langage                 | Paramètres régionaux (BCP-47) | Personnalisations  | [Détection de la langue](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arabe (Bahreïn), standard moderne  | `ar-BH` | Texte                                   |                           | 
| Arabe (Égypte)                     | `ar-EG` | Texte                                   | Oui                          |
| Arabe (Irak)                      | `ar-IQ` | Texte                                   |                           |
| Arabe (Israël)                    | `ar-IL` | Texte                                   |                           |
| Arabe (Jordanie)                    | `ar-JO` | Texte                                   |                           |
| Arabe (Koweït)                    | `ar-KW` | Texte                                   |                           |
| Arabe (Liban)                   | `ar-LB` | Texte                                   |                           |
| Arabe (Oman)                      | `ar-OM` | Texte                                   |                           |
| Arabe (Qatar)                     | `ar-QA` | Texte                                   |                           |
| Arabe (Arabie saoudite)              | `ar-SA` | Texte                                   |                           |
| Arabe (État de Palestine)        | `ar-PS` | Texte                                   |                           |
| Arabe (Syrie)                     | `ar-SY` | Texte                                   |                           |
| Arabe (Émirats arabes unis)      | `ar-AE` | Texte                                   |                           |
| Bulgare (Bulgarie)               | `bg-BG` | Texte                                   |                           |
| Catalan (Espagne)                    | `ca-ES` | Texte                                   | Oui                          |
| Chinois (cantonais, traditionnel)   | `zh-HK` | Audio (20201015)<br>Texte                 |        Oui                   |
| Chinois (mandarin, simplifié)     | `zh-CN` | Audio (20200910)<br>Texte                 |     Oui                      |
| Chinois (mandarin, taïwanais)       | `zh-TW` | Audio (20190701, 20201015)<br>Texte                 |           Oui                |
| Croate (Croatie)                 | `hr-HR` | Texte                                   |                           |
| Tchèque (République tchèque)             | `cs-CZ` | Texte                                   |                           |
| Danois (Danemark)                   | `da-DK` | Texte                                   | Oui                          |
| Néerlandais (Pays-Bas)                | `nl-NL` | Audio (20201015)<br>Texte<br>Prononcer|    Oui                       |
| Anglais (Australie)                | `en-AU` | Audio (20201019)<br>Texte                 | Oui                          |
| Anglais (Canada)                   | `en-CA` | Audio (20201019)<br>Texte                 | Oui                          |
| Anglais (Ghana)                    | `en-GH` | Texte                                   |                           |
| Anglais (Hong Kong)                | `en-HK` | Texte                                   |                           |
| Anglais (Inde)                    | `en-IN` | Audio (20200923)<br>Texte                 |                          |
| Anglais (Irlande)                  | `en-IE` | Texte                                   |                           |
| Anglais (Kenya)                    | `en-KE` | Texte                                   |                           |
| Anglais (Nouvelle-Zélande)              | `en-NZ` | Audio (20201019)<br>Texte                 |                          |
| Anglais (Nigeria)                  | `en-NG` | Texte                                   |                           |
| Anglais (Philippines)              | `en-PH` | Texte                                   |                           |
| Anglais (Singapour)                | `en-SG` | Texte                                   |                           |
| Anglais (Afrique du Sud)             | `en-ZA` | Texte                                   |                           |
| Anglais (Tanzanie)                 | `en-TZ` | Texte                                   |                           |
| Anglais (Royaume-Uni)           | `en-GB` | Audio (20201019)<br>Texte<br>Prononcer| Oui                          |
| Anglais (États-Unis)            | `en-US` | Audio (20201019)<br>Texte<br>Prononcer| Oui                          |
| Estonien (Estonie)                  | `et-EE` | Texte                                   |                           |
| Filipino (Philippines)             | `fil-PH`| Texte                                   |                           |
| Finnois (Finlande)                  | `fi-FI` | Texte                                   |     Oui                      |
| Français (Canada)                    | `fr-CA` | Audio (20201015)<br>Texte<br>Prononcer|     Oui                      |
| Français (France)                    | `fr-FR` | Audio (20201015)<br>Texte<br>Prononcer|      Oui                     |
| Français (Suisse)               | `fr-CH` | Texte<br>Prononcer                  |                           |
| Allemand (Autriche)                   | `de-AT` | Texte<br>Prononcer                  |                           |
| Allemand (Allemagne)                   | `de-DE` | Audio (20190701, 20200619, 20201127)<br>Texte<br>Prononcer|  Oui                         |
| Grec (Grèce)                     | `el-GR` | Texte                                   |  Oui                         |
| Goudjrati (Inde)                  | `gu-IN` | Texte                                   |                           |
| Hindi (Inde)                      | `hi-IN` | Audio (20200701)<br>Texte                 |     Oui                      |
| Hongrois (Hongrie)                | `hu-HU` | Texte                                   |                           |
| Indonésien (Indonésie)             | `id-ID` | Texte                                   |                           |
| Irlandais (Irlande)                     | `ga-IE` | Texte                                   |                           |
| Italien (Italie)                    | `it-IT` | Audio (20201016)<br>Texte<br>Prononcer|      Oui                     |
| Japonais (Japon)                   | `ja-JP` | Texte                                   |      Oui                     |
| Coréen (Corée)                     | `ko-KR` | Audio (20201015)<br>Texte                 |      Oui                     |
| Letton (Lettonie)                   | `lv-LV` | Texte                                   |                           |
| Lituanien (Lituanie)             | `lt-LT` | Texte                                   |                           |
| Malais (Malaisie)                   | `ms-MY` | Texte                                   |                           |
| Maltais (Malte)                    | `mt-MT` | Texte                                   |                           |
| Marathi (Inde)                    | `mr-IN` | Texte                                   |                           |
| Norvégien (bokmål, Norvège)         | `nb-NO` | Texte                                   |     Oui                      |
| Polonais (Pologne)                    | `pl-PL` | Texte                                   |       Oui                    |
| Portugais (Brésil)                | `pt-BR` | Audio (20190620, 20201015)<br>Texte<br>Prononcer|          Oui                 |
| Portugais (Portugal)              | `pt-PT` | Texte<br>Prononcer                  |             Oui              |
| Roumain (Roumanie)                 | `ro-RO` | Texte                                   |  Oui                         |
| Russe (Russie)                   | `ru-RU` | Audio (20200907)<br>Texte                 |                Oui           |
| Slovaque (Slovaquie)                  | `sk-SK` | Texte                                   |                           |
| Slovène (Slovénie)               | `sl-SI` | Texte                                   |                           |
| Espagnol (Argentine)                | `es-AR` | Texte<br>Prononcer                  |                           |
| Espagnol (Bolivie)                  | `es-BO` | Texte<br>Prononcer                  |                           |
| Espagnol (Chili)                    | `es-CL` | Texte<br>Prononcer                  |                           |
| Espagnol (Colombie)                 | `es-CO` | Texte<br>Prononcer                  |                           |
| Espagnol (Costa Rica)               | `es-CR` | Texte<br>Prononcer                  |                           |
| Espagnol (Cuba)                     | `es-CU` | Texte<br>Prononcer                  |                           |
| Espagnol (République dominicaine)       | `es-DO` | Texte<br>Prononcer                  |                           |
| Espagnol (Équateur)                  | `es-EC` | Texte<br>Prononcer                  |                           |
| Espagnol (Salvador)              | `es-SV` | Texte<br>Prononcer                  |                           |
| Espagnol (Guinée équatoriale)        | `es-GQ` | Texte                                   |                           |
| Espagnol (Guatemala)                | `es-GT` | Texte<br>Prononcer                  |                           |
| Espagnol (Honduras)                 | `es-HN` | Texte<br>Prononcer                  |                           |
| Espagnol (Mexique)                   | `es-MX` | Audio (20200907)<br>Texte<br>Prononcer|    Oui                       |
| Espagnol (Nicaragua)                | `es-NI` | Texte<br>Prononcer                  |                           |
| Espagnol (Panama)                   | `es-PA` | Texte<br>Prononcer                  |                           |
| Espagnol (Paraguay)                 | `es-PY` | Texte<br>Prononcer                  |                           |
| Espagnol (Pérou)                     | `es-PE` | Texte<br>Prononcer                  |                           |
| Espagnol (Porto Rico)              | `es-PR` | Texte<br>Prononcer                  |                           |
| Espagnol (Espagne)                    | `es-ES` | Audio (20201015)<br>Texte<br>Prononcer|  Oui                         |
| Espagnol (Uruguay)                  | `es-UY` | Texte<br>Prononcer                  |                           |
| Espagnol (États-Unis)                      | `es-US` | Texte<br>Prononcer                  |                           |
| Espagnol (Venezuela)                | `es-VE` | Texte<br>Prononcer                  |                           |
| Suédois (Suède)                   | `sv-SE` | Texte                                   |   Oui                        |
| Tamoul (Inde)                      | `ta-IN` | Texte                                   |                           |
| Télougou (Inde)                     | `te-IN` | Texte                                   |                           |
| Thaï (Thaïlande)                    | `th-TH` | Texte                                   |      Oui                     |
| Turc (Turquie)                   | `tr-TR` | Texte                                   |                           |
| Vietnamien (Vietnam)               | `vi-VN` | Texte                                   |                           |

## <a name="text-to-speech"></a>Synthèse vocale

Le Kit de développement logiciel (SDK) Speech de Microsoft et les API REST prennent en charge ces voix qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux. Vous pouvez également obtenir la liste complète des langues et des voix prises en charge pour chaque région/point de terminaison spécifique via l’API [voices/list](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, visitez la page [Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voix neurales

La synthèse vocale neuronale est un nouveau type de synthèse vocale alimentée par les réseaux neuronaux profonds. Lorsque vous utilisez une voix neuronale, il est presque impossible de distinguer la voix synthétisée des enregistrements humains.

Les voix neurales peuvent être utilisées pour rendre les interactions avec les chatbots et des assistants vocaux plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Grâce à la prosodie naturelle quasi humaine et à la bonne articulation des mots, les voix neuronales réduisent considérablement la fatigue d’écoute des utilisateurs qui interagissent avec les systèmes d’intelligence artificielle.

> [!NOTE]
> Les voix neurales sont créées à partir d’exemples qui utilisent un taux d’échantillonnage de 24 kHz.
> Toutes les voix peuvent s’échantillonner ou sous-échantillonner à d’autres taux d’échantillonnage lors de la synthèse.


| Langage | Paramètres régionaux | Sexe | Nom de la voix | Prise en charge du style |
|---|---|---|---|---|
| Arabe (Égypte) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Général |
| Arabe (Égypte) | `ar-EG` | Male | `ar-EG-ShakirNeural` | Général |
| Arabe (Arabie saoudite) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Général |
| Arabe (Arabie saoudite) | `ar-SA` | Male | `ar-SA-HamedNeural` | Général |
| Bulgare (Bulgarie) | `bg-BG` | Female | `bg-BG-KalinaNeural` | Général |
| Bulgare (Bulgarie) | `bg-BG` | Male | `bg-BG-BorislavNeural` | Général |
| Catalan (Espagne) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Général |
| Catalan (Espagne) | `ca-ES` | Female | `ca-ES-JoanaNeural` | Général |
| Catalan (Espagne) | `ca-ES` | Male | `ca-ES-EnricNeural` | Général |
| Chinois (cantonais, traditionnel) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Général |
| Chinois (cantonais, traditionnel) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | Général |
| Chinois (cantonais, traditionnel) | `zh-HK` | Male | `zh-HK-WanLungNeural` | Général |
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Général, plusieurs styles de voix disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Voix d’enfant, optimisée pour la narration |
| Chinois (mandarin, simplifié) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Optimisée pour la lecture de bulletins d’informations,<br /> plusieurs styles de voix disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinois (mandarin, simplifié) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Optimisée pour raconter des histoires |
| Chinois (mandarin, taïwanais) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | Général |
| Chinois (mandarin, taïwanais) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Général |
| Chinois (mandarin, taïwanais) | `zh-TW` | Male | `zh-TW-YunJheNeural` | Général |
| Croate (Croatie) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | Général |
| Croate (Croatie) | `hr-HR` | Male | `hr-HR-SreckoNeural` | Général |
| Tchèque (Tchéquie) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | Général |
| Tchèque (Tchéquie) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | Général |
| Danois (Danemark) | `da-DK` | Female | `da-DK-ChristelNeural` | Général |
| Danois (Danemark) | `da-DK` | Male | `da-DK-JeppeNeural` | Général |
| Néerlandais (Belgique) | `nl-BE` | Female | `nl-BE-DenaNeural` <sup>Nouveau</sup> | Général | 
| Néerlandais (Belgique) | `nl-BE` | Male | `nl-BE-ArnaudNeural` <sup>Nouveau</sup> | Général | 
| Néerlandais (Pays-Bas) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Général |
| Néerlandais (Pays-Bas) | `nl-NL` | Female | `nl-NL-FennaNeural` | Général |
| Néerlandais (Pays-Bas) | `nl-NL` | Male | `nl-NL-MaartenNeural` | Général |
| Anglais (Australie) | `en-AU` | Female | `en-AU-NatashaNeural` | Général |
| Anglais (Australie) | `en-AU` | Male | `en-AU-WilliamNeural` | Général |
| Anglais (Canada) | `en-CA` | Female | `en-CA-ClaraNeural` | Général |
| Anglais (Canada) | `en-CA` | Male | `en-CA-LiamNeural` | Général |
| Anglais (Inde) | `en-IN` | Female | `en-IN-NeerjaNeural` | Général |
| Anglais (Inde) | `en-IN` | Male | `en-IN-PrabhatNeural` | Général |
| Anglais (Irlande) | `en-IE` | Female | `en-IE-EmilyNeural` | Général |
| Anglais (Irlande) | `en-IE` | Male | `en-IE-ConnorNeural` | Général |
| Anglais (Philippines) | `en-PH` | Female | `en-PH-RosaNeural` <sup>Nouveau</sup> | Général | 
| Anglais (Philippines) | `en-PH` | Male | `en-PH-JamesNeural` <sup>Nouveau</sup> | Général | 
| Anglais (Royaume-Uni) | `en-GB` | Female | `en-GB-LibbyNeural` | Général |
| Anglais (Royaume-Uni) | `en-GB` | Female | `en-GB-MiaNeural` | Général |
| Anglais (Royaume-Uni) | `en-GB` | Male | `en-GB-RyanNeural` | Général |
| Anglais (États-Unis) | `en-US` | Female | `en-US-AriaNeural` | Général, plusieurs styles de voix disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Anglais (États-Unis) | `en-US` | Female | `en-US-JennyNeural` | Général |
| Anglais (États-Unis) | `en-US` | Male | `en-US-GuyNeural` | Général |
| Estonien (Estonie) | `et-EE` | Female | `et-EE-AnuNeural` | Général |
| Estonien (Estonie) | `et-EE` | Male | `et-EE-KertNeural` | Général |
| Finnois (Finlande) | `fi-FI` | Female | `fi-FI-NooraNeural` | Général |
| Finnois (Finlande) | `fi-FI` | Female | `fi-FI-SelmaNeural` | Général |
| Finnois (Finlande) | `fi-FI` | Male | `fi-FI-HarriNeural` | Général |
| Français (Belgique) | `fr-BE` | Female | `fr-BE-CharlineNeural` <sup>Nouveau</sup> | Général | 
| Français (Belgique) | `fr-BE` | Male | `fr-BE-GerardNeural` <sup>Nouveau</sup> | Général | 
| Français (Canada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Général |
| Français (Canada) | `fr-CA` | Male | `fr-CA-AntoineNeural` | Général |
| Français (Canada) | `fr-CA` | Male | `fr-CA-JeanNeural` | Général |
| Français (France) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Général |
| Français (France) | `fr-FR` | Male | `fr-FR-HenriNeural` | Général |
| Français (Suisse) | `fr-CH` | Female | `fr-CH-ArianeNeural` | Général |
| Français (Suisse) | `fr-CH` | Male | `fr-CH-FabriceNeural` | Général |
| Allemand (Autriche) | `de-AT` | Female | `de-AT-IngridNeural` | Général |
| Allemand (Autriche) | `de-AT` | Male | `de-AT-JonasNeural` | Général |
| Allemand (Allemagne) | `de-DE` | Female | `de-DE-KatjaNeural` | Général |
| Allemand (Allemagne) | `de-DE` | Male | `de-DE-ConradNeural` | Général |
| Allemand (Suisse) | `de-CH` | Female | `de-CH-LeniNeural` | Général |
| Allemand (Suisse) | `de-CH` | Male | `de-CH-JanNeural` | Général |
| Grec (Grèce) | `el-GR` | Female | `el-GR-AthinaNeural` | Général |
| Grec (Grèce) | `el-GR` | Male | `el-GR-NestorasNeural` | Général |
| Hébreu (Israël) | `he-IL` | Female | `he-IL-HilaNeural` | Général |
| Hébreu (Israël) | `he-IL` | Male | `he-IL-AvriNeural` | Général |
| Hindi (Inde) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Général |
| Hindi (Inde) | `hi-IN` | Male | `hi-IN-MadhurNeural` | Général |
| Hongrois (Hongrie) | `hu-HU` | Female | `hu-HU-NoemiNeural` | Général |
| Hongrois (Hongrie) | `hu-HU` | Male | `hu-HU-TamasNeural` | Général |
| Indonésien (Indonésie) | `id-ID` | Female | `id-ID-GadisNeural` | Général |
| Indonésien (Indonésie) | `id-ID` | Male | `id-ID-ArdiNeural` | Général |
| Irlandais (Irlande) | `ga-IE` | Female | `ga-IE-OrlaNeural` | Général |
| Irlandais (Irlande) | `ga-IE` | Male | `ga-IE-ColmNeural` | Général |
| Italien (Italie) | `it-IT` | Female | `it-IT-ElsaNeural` | Général |
| Italien (Italie) | `it-IT` | Female | `it-IT-IsabellaNeural` | Général |
| Italien (Italie) | `it-IT` | Male | `it-IT-DiegoNeural` | Général |
| Japonais (Japon) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Général |
| Japonais (Japon) | `ja-JP` | Male | `ja-JP-KeitaNeural` | Général |
| Coréen (Corée) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Général |
| Coréen (Corée) | `ko-KR` | Male | `ko-KR-InJoonNeural` | Général |
| Letton (Lettonie) | `lv-LV` | Female | `lv-LV-EveritaNeural` | Général |
| Letton (Lettonie) | `lv-LV` | Male | `lv-LV-NilsNeural` | Général |
| Lituanien (Lituanie) | `lt-LT` | Female | `lt-LT-OnaNeural` | Général |
| Lituanien (Lituanie) | `lt-LT` | Male | `lt-LT-LeonasNeural` | Général |
| Malais (Malaisie) | `ms-MY` | Female | `ms-MY-YasminNeural` | Général |
| Malais (Malaisie) | `ms-MY` | Male | `ms-MY-OsmanNeural` | Général |
| Maltais (Malte) | `mt-MT` | Female | `mt-MT-GraceNeural` | Général |
| Maltais (Malte) | `mt-MT` | Male | `mt-MT-JosephNeural` | Général |
| Norvégien (bokmål, Norvège) | `nb-NO` | Female | `nb-NO-IselinNeural` | Général |
| Norvégien (bokmål, Norvège) | `nb-NO` | Female | `nb-NO-PernilleNeural` | Général |
| Norvégien (bokmål, Norvège) | `nb-NO` | Male | `nb-NO-FinnNeural` | Général |
| Polonais (Pologne) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | Général |
| Polonais (Pologne) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Général |
| Polonais (Pologne) | `pl-PL` | Male | `pl-PL-MarekNeural` | Général |
| Portugais (Brésil) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Général, plusieurs styles de voix disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portugais (Brésil) | `pt-BR` | Male | `pt-BR-AntonioNeural` | Général |
| Portugais (Portugal) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Général |
| Portugais (Portugal) | `pt-PT` | Female | `pt-PT-RaquelNeural` | Général |
| Portugais (Portugal) | `pt-PT` | Male | `pt-PT-DuarteNeural` | Général |
| Roumain (Roumanie) | `ro-RO` | Female | `ro-RO-AlinaNeural` | Général |
| Roumain (Roumanie) | `ro-RO` | Male | `ro-RO-EmilNeural` | Général |
| Russe (Russie) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Général |
| Russe (Russie) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | Général |
| Russe (Russie) | `ru-RU` | Male | `ru-RU-DmitryNeural` | Général |
| Slovaque (Slovaquie) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | Général |
| Slovaque (Slovaquie) | `sk-SK` | Male | `sk-SK-LukasNeural` | Général |
| Slovène (Slovénie) | `sl-SI` | Female | `sl-SI-PetraNeural` | Général |
| Slovène (Slovénie) | `sl-SI` | Male | `sl-SI-RokNeural` | Général |
| Espagnol (Mexique) | `es-MX` | Female | `es-MX-DaliaNeural` | Général |
| Espagnol (Mexique) | `es-MX` | Male | `es-MX-JorgeNeural` | Général |
| Espagnol (Espagne) | `es-ES` | Female | `es-ES-ElviraNeural` | Général |
| Espagnol (Espagne) | `es-ES` | Male | `es-ES-AlvaroNeural` | Général |
| Suédois (Suède) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Général |
| Suédois (Suède) | `sv-SE` | Female | `sv-SE-SofieNeural` | Général |
| Suédois (Suède) | `sv-SE` | Male | `sv-SE-MattiasNeural` | Général |
| Tamoul (Inde) | `ta-IN` | Female | `ta-IN-PallaviNeural` | Général |
| Tamoul (Inde) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | Général |
| Télougou (Inde) | `te-IN` | Female | `te-IN-ShrutiNeural` | Général |
| Télougou (Inde) | `te-IN` | Male | `te-IN-MohanNeural` | Général |
| Thaï (Thaïlande) | `th-TH` | Female | `th-TH-AcharaNeural` | Général |
| Thaï (Thaïlande) | `th-TH` | Female | `th-TH-PremwadeeNeural` | Général |
| Thaï (Thaïlande) | `th-TH` | Male | `th-TH-NiwatNeural` | Général |
| Turc (Turquie) | `tr-TR` | Female | `tr-TR-EmelNeural` | Général |
| Turc (Turquie) | `tr-TR` | Male | `tr-TR-AhmetNeural` | Général |
| Ukrainien (Ukraine) | `uk-UA` | Female | `uk-UA-PolinaNeural` <sup>Nouveau</sup> | Général | 
| Ukrainien (Ukraine) | `uk-UA` | Male | `uk-UA-OstapNeural` <sup>Nouveau</sup> | Général | 
| Ourdou (Pakistan) | `ur-PK` | Female | `ur-PK-UzmaNeural` <sup>Nouveau</sup>  | Général | 
| Ourdou (Pakistan) | `ur-PK` | Male | `ur-PK-AsadNeural` <sup>Nouveau</sup> | Général | 
| Vietnamien (Vietnam) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | Général |
| Vietnamien (Vietnam) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | Général |
| Gallois (Royaume-Uni) | `cy-GB` | Female | `cy-GB-NiaNeural` <sup>Nouveau</sup> | Général | 
| Gallois (Royaume-Uni) | `cy-GB` | Male | `cy-GB-AledNeural` <sup>Nouveau</sup> | Général | 

#### <a name="neural-voices-in-preview"></a>Voix neuronales en préversion

Les voix neuronales ci-dessous sont en préversion publique. 

| Langage                         | Paramètres régionaux  | Sexe | Nom de la voix                             | Prise en charge du style |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | Général, plusieurs styles disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | Général, plusieurs jeux de rôle et styles disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | Voix de senior, plusieurs styles disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | Général, plusieurs jeux de rôle et styles disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinois (mandarin, simplifié) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | Général, plusieurs styles disponibles [à l’aide de SSML](speech-synthesis-markup.md#adjust-speaking-styles) |

> [!IMPORTANT]
> Les voix en préversion publique sont uniquement disponibles dans 3 régions de service : USA Est, Europe Ouest et Asie Sud-Est.

Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#neural-and-standard-voices).

Pour savoir comment configurer et ajuster les voix neuronales, comme les styles de diction, consultez [Langage de balisage de synthèse vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> La voix `en-US-JessaNeural` a basculé sur `en-US-AriaNeural`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

> [!TIP]
> Vous pouvez continuer à utiliser le mappage de nom de service complet comme « Voix Microsoft Server Speech Text to Speech (en-US, AriaNeural) » dans vos requêtes de synthèse vocale.

### <a name="standard-voices"></a>Voix standard

Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, ce qui vous permet de convertir le texte en parole synthétisée. Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#neural-and-standard-voices).

> [!NOTE]
> À deux exceptions près, les voix standard sont créées à partir d’exemples qui utilisent un taux d’échantillonnage de 16 kHz.
> **Les voix en-US-AriaRUS** et **en-US-GuyRUS** sont également créées à partir d’exemples qui utilisent un taux d’échantillonnage de 24 kHz.
> Toutes les voix peuvent s’échantillonner ou sous-échantillonner à d’autres taux d’échantillonnage lors de la synthèse.

| Langage | Paramètres régionaux (BCP-47) | Sexe | Nom de la voix |
|--|--|--|--|
| Arabe (arabe) | `ar-EG` | Female | `ar-EG-Hoda`|
| Arabe (Arabie saoudite) | `ar-SA` | Male | `ar-SA-Naayf`|
| Bulgare (Bulgarie) | `bg-BG` | Male | `bg-BG-Ivan`|
| Catalan (Espagne) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Chinois (cantonais, traditionnel) | `zh-HK` | Male | `zh-HK-Danny`|
| Chinois (cantonais, traditionnel) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Chinois (mandarin, simplifié) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Chinois (mandarin, simplifié) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Chinois (mandarin, taïwanais) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Chinois (mandarin, taïwanais) |  `zh-TW` | Female | `zh-TW-Yating`|
| Chinois (mandarin, taïwanais) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| Croate (Croatie) | `hr-HR` | Male | `hr-HR-Matej`|
| Tchèque (République tchèque) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| Danois (Danemark) | `da-DK` | Female | `da-DK-HelleRUS`|
| Néerlandais (Pays-Bas) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Anglais (Australie) | `en-AU` | Female | `en-AU-Catherine`|
| Anglais (Australie) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Anglais (Canada) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Anglais (Canada) | `en-CA` | Female | `en-CA-Linda`|
| Anglais (Inde) | `en-IN` | Female | `en-IN-Heera`|
| Anglais (Inde) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Anglais (Inde) | `en-IN` | Male | `en-IN-Ravi`|
| Anglais (Irlande) | `en-IE` | Male | `en-IE-Sean`|
| Anglais (Royaume-Uni) | `en-GB` | Male | `en-GB-George`|
| Anglais (Royaume-Uni) | `en-GB` | Female | `en-GB-HazelRUS`|
| Anglais (Royaume-Uni) | `en-GB` | Female | `en-GB-Susan`|
| Anglais (États-Unis) | `en-US` | Male | `en-US-BenjaminRUS`|
| Anglais (États-Unis) | `en-US` | Male | `en-US-GuyRUS`|
| Anglais (États-Unis) | `en-US` | Female | `en-US-AriaRUS`|
| Anglais (États-Unis) | `en-US` | Female | `en-US-ZiraRUS`|
| Finnois (Finlande) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Français (Canada) | `fr-CA` | Female | `fr-CA-Caroline`|
| Français (Canada) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| Français (France) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| Français (France) | `fr-FR` | Female | `fr-FR-Julie`|
| Français (France) | `fr-FR` | Male | `fr-FR-Paul`|
| Français (Suisse) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Allemand (Autriche) | `de-AT` | Male | `de-AT-Michael`|
| Allemand (Allemagne) | `de-DE` | Female | `de-DE-HeddaRUS`|
| Allemand (Allemagne) | `de-DE` | Male | `de-DE-Stefan`|
| Allemand (Suisse) | `de-CH` | Male | `de-CH-Karsten`|
| Grec (Grèce) | `el-GR` | Male | `el-GR-Stefanos`|
| Hébreu (Israël) | `he-IL` | Male | `he-IL-Asaf`|
| Hindi (Inde) | `hi-IN` | Male | `hi-IN-Hemant`|
| Hindi (Inde) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Hongrois (Hongrie) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonésien (Indonésie) | `id-ID` | Male | `id-ID-Andika`|
| Italien (Italie) | `it-IT` | Male | `it-IT-Cosimo`|
| Italien (Italie) | `it-IT` | Female | `it-IT-LuciaRUS`|
| Japonais (Japon) | `ja-JP` | Female | `ja-JP-Ayumi`|
| Japonais (Japon) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| Japonais (Japon) | `ja-JP` | Male | `ja-JP-Ichiro`|
| Coréen (Corée) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| Malais (Malaisie) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Norvégien (bokmål, Norvège) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| Polonais (Pologne) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portugais (Brésil) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portugais (Brésil) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| Portugais (Portugal) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| Roumain (Roumanie) | `ro-RO` | Male | `ro-RO-Andrei`|
| Russe (Russie) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| Russe (Russie) | `ru-RU` | Female | `ru-RU-Irina`|
| Russe (Russie) | `ru-RU` | Male | `ru-RU-Pavel`|
| Slovaque (Slovaquie) | `sk-SK` | Male | `sk-SK-Filip`|
| Slovène (Slovénie) | `sl-SI` | Male | `sl-SI-Lado`|
| Espagnol (Mexique) | `es-MX` | Female | `es-MX-HildaRUS`|
| Espagnol (Mexique) | `es-MX` | Male | `es-MX-Raul`|
| Espagnol (Espagne) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Espagnol (Espagne) | `es-ES` | Female | `es-ES-Laura`|
| Espagnol (Espagne) | `es-ES` | Male | `es-ES-Pablo`|
| Suédois (Suède) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamoul (Inde) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Télougou (Inde) | `te-IN` | Female | `te-IN-Chitra`|
| Thaï (Thaïlande) | `th-TH` | Male | `th-TH-Pattara`|
| Turc (Turquie) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamien (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> La voix `en-US-Jessa` a basculé sur `en-US-Aria`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

> [!TIP]
> Vous pouvez continuer à utiliser le mappage de nom de service complet comme « Voix Microsoft Server Speech Text to Speech (en-US, AriaRUS) » dans vos requêtes de synthèse vocale.

### <a name="customization"></a>Personnalisation

Custom Voice est disponible dans le niveau standard et le niveau neuronal. Les langues prises en charge sont différentes pour ces deux niveaux. 

| Langage | Paramètres régionaux | Standard | Neuronale |
|--|--|--|--|
| Chinois (mandarin, simplifié) | `zh-CN` | Oui | Oui |
| Chinois (mandarin, simplifié), anglais bilingue | `zh-CN` bilingue | Oui | Oui |
| Anglais (Australie) | `en-AU` | Non | Oui |
| Anglais (Inde) | `en-IN` | Oui | Oui |
| Anglais (Royaume-Uni) | `en-GB` | Oui | Oui |
| Anglais (États-Unis) | `en-US` | Oui | Oui |
| Français (Canada) | `fr-CA` | Non | Oui |
| Français (France) | `fr-FR` | Oui | Oui |
| Allemand (Allemagne) | `de-DE` | Oui | Oui |
| Italien (Italie) | `it-IT` | Oui | Oui |
| Japonais (Japon) | `ja-JP` | Non | Oui |
| Coréen (Corée) | `ko-KR` | Non | Oui |
| Portugais (Brésil) | `pt-BR` | Oui | Oui |
| Espagnol (Mexique) | `es-MX` | Oui | Oui |
| Espagnol (Espagne) | `es-ES` | Non | Oui |

Sélectionnez les paramètres régionaux qui correspondent aux données d’apprentissage servant à entraîner un modèle vocal personnalisé. Par exemple, si vos données d’enregistrement sont en anglais avec un accent britannique, sélectionnez `en-GB`.

> [!NOTE]
> À l’exception du chinois-anglais, nous ne gérons pas l’apprentissage de modèles bilingues dans Custom Voice. Sélectionnez « Bilingue chinois-anglais » si vous souhaitez effectuer l’apprentissage d’une voix en chinois qui parle aussi anglais. L’entraînement du modèle bilingue chinois-anglais à l’aide de la méthode standard est disponible uniquement pour la région Europe Nord et la région USA Centre Nord. L’entraînement de la voix neuronale personnalisée est disponible dans les régions Royaume-Uni Sud et USA Est.

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

| Langage | Paramètres régionaux (BCP-47) | Vérification dépendante du texte | Vérification indépendante du texte | Identification indépendante du texte |
|----|----|----|----|----|
|Anglais (US)  |  en-US  |  Oui  |  Oui  |  Oui |
|Chinois (mandarin, simplifié) | zh-CN     |     n/a |     Oui |     Oui|
|Anglais (Australie)     | en-AU     | n/a     | Oui     | Oui|
|Anglais (Canada)     | en-CA     | n/a |     Oui |     Oui|
|Anglais (Royaume-Uni)     | en-GB     | n/a     | Oui     | Oui|
|Français (Canada)     | fr-CA     | n/a     | Oui |     Oui|
|Français (France)     | fr-FR     | n/a     | Oui     | Oui|
|Allemand (Allemagne)     | de-DE     | n/a     | Oui     | Oui|
|Italien | it-IT     |     n/a     | Oui |     Oui|
|Japonais     | ja-JP | n/a     | Oui     | Oui|
|Portugais (Brésil) | pt-br |     n/a |     Oui |     Oui|
|Espagnol (Mexique)     | es-MX     | n/a |     Oui |     Oui|
|Espagnol (Espagne)     | es-ES | n/a     | Oui |     Oui|

## <a name="next-steps"></a>Étapes suivantes

* [Créez un compte Azure gratuit](https://azure.microsoft.com/free/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
