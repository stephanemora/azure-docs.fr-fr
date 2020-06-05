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
ms.openlocfilehash: 437dc18dc16e879e95ff4ec7c1a9ab7ec3f17bef
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84265996"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Prise en charge des langues et de la voix pour le service Speech

La prise en charge des langues varie selon les fonctionnalités du service Speech. Les tableaux suivants résument la prise en charge des langues pour les offres de service [Reconnaissance vocale](#speech-to-text), [Synthèse vocale](#text-to-speech) et [Traduction vocale](#speech-translation).

## <a name="speech-to-text"></a>Reconnaissance vocale

Le Kit de développement logiciel (SDK) et l’API REST Speech de Microsoft prennent en charge les langages suivants (paramètres régionaux). 

Afin d'améliorer la précision, la personnalisation est proposée pour une partie des langues en chargeant des **transcriptions audio + étiquetées à la main** ou des **textes associés : Phrases.** Pour en savoir plus sur la personnalisation, consultez [Bien démarrer avec Custom Speech](how-to-custom-speech.md).

Pour plus d’informations sur la façon dont vous pouvez améliorer la prononciation, consultez [Améliorer un modèle pour Custom Speech](how-to-custom-speech-improve-accuracy.md#add-new-words-with-pronunciation).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Paramètres régionaux  | Langage                          | Prise en charge | Personnalisations                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabe (Émirats arabes unis)                      | Oui       | Non                                                |
| `ar-BH` | Arabe (Bahreïn), standard moderne | Oui       | Modèle de langage                                    |
| `ar-EG` | Arabe (Égypte)                    | Oui       | Modèle de langage                                    |
| `ar-IL` | Arabe (Israël)                   | Oui       | Non                                                |
| `ar-KW` | Arabe (Koweït)                   | Oui       | Non                                                |
| `ar-PS` | Arabe (Palestine)                | Oui       | Non                                                |
| `ar-QA` | Arabe (Qatar)                    | Oui       | Non                                                |
| `ar-SA` | Arabe (Arabie saoudite)             | Oui       | Non                                                |
| `ar-SY` | Arabe (Syrie)                    | Oui       | Modèle de langage                                    |
| `ca-ES` | Catalan                           | Oui       | Modèle de langage                                    |
| `da-DK` | Danois (Danemark)                  | Oui       | Modèle de langage                                    |
| `de-DE` | Allemand (Allemagne)                  | Oui       | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `en-AU` | Anglais (Australie)               | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `en-CA` | Anglais (Canada)                  | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `en-GB` | Anglais (Royaume-Uni)          | Oui       | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `en-IN` | Anglais (Inde)                   | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `en-NZ` | Anglais (Nouvelle-Zélande)             | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `en-US` | Anglais (États-Unis)           | Oui       | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `es-ES` | Espagnol (Espagne)                   | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `es-MX` | Espagnol (Mexique)                  | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `fi-FI` | Finnois (Finlande)                 | Oui       | Modèle de langage                                    |
| `fr-CA` | Français (Canada)                   | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `fr-FR` | Français (France)                   | Oui       | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `gu-IN` | Goudjrati (Inde)                 | Oui       | Modèle de langage                                    |
| `hi-IN` | Hindi (Inde)                     | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `it-IT` | Italien (Italie)                   | Oui       | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `ja-JP` | Japonais (Japon)                  | Oui       | Modèle de langage                                    |
| `ko-KR` | Coréen (Corée)                    | Oui       | Modèle de langage                                    |
| `mr-IN` | Marathi (Inde)                   | Oui       | Modèle de langage                                    |
| `nb-NO` | Norvégien( Bokmål) (Norvège)       | Oui       | Modèle de langage                                    |
| `nl-NL` | Néerlandais (Pays-Bas)               | Oui       | Modèle de langage                                    |
| `pl-PL` | Polonais (Pologne)                   | Oui       | Modèle de langage                                    |
| `pt-BR` | Portugais (Brésil)               | Oui       | Modèle acoustique<br>Modèle de langage<br>Prononcer |
| `pt-PT` | Portugais (Portugal)             | Oui       | Modèle de langage                                    |
| `ru-RU` | Russe (Russie)                  | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `sv-SE` | Suédois (Suède)                  | Oui       | Modèle de langage                                    |
| `ta-IN` | Tamoul (Inde)                     | Oui       | Modèle de langage                                    |
| `te-IN` | Télougou (Inde)                    | Oui       | Non                                                |
| `th-TH` | Thaï (Thaïlande)                   | Oui       | Non                                                |
| `tr-TR` | Turc (Turquie)                  | Oui       | Modèle de langage                                    |
| `zh-CN` | Chinois (mandarin, simplifié)    | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `zh-HK` | Chinois (cantonais, traditionnel)  | Oui       | Modèle de langage                                    |
| `zh-TW` | Chinois (mandarin, taïwanais)      | Oui       | Modèle de langage                                    |

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
| `de-DE` | Allemand (Allemagne)                | `Female` | « de-DE-KatjaNeural »      | Général |
| `en-AU` | Anglais (Australie)             | `Female` | « en-AU-NatashaNeural »    | Général |
| `en-CA` | Anglais (Canada)                | `Female` | « en-CA-ClaraNeural »      | Général |
| `en-GB` | Anglais (Royaume-Uni)                    | `Female` | « en-GB-LibbyNeural »      | Général |
|         |                                 | `Female` | « en-GB-MiaNeural »        | Général |
| `en-US` | Anglais (US)                    | `Female` | « en-US-AriaNeural »       | Général, plusieurs styles de voix sont disponibles |
|         |                                 | `Male`   | « en-US-GuyNeural »        | Général |
| `es-ES` | Espagnol (Espagne)                 | `Female` | « es-ES-ElviraNeural »     | Général |
| `es-MX` | Espagnol (Mexique)                | `Female` | « es-MX-DaliaNeural »      | Général |
| `fr-CA` | Français (Canada)                 | `Female` | « fr-CA-SylvieNeural »     | Général |
| `fr-FR` | Français (France)                 | `Female` | « fr-FR-DeniseNeural »     | Général |
| `it-IT` | Italien (Italie)                 | `Female` | « it-IT-ElsaNeural »       | Général |
| `ja-JP` | Japonais                        | `Female` | « ja-JP-NanamiNeural »     | Général |
| `ko-KR` | Coréen                          | `Female` | « ko-KR-SunHiNeural »      | Général |
| `nb-NO` | Norvégien                       | `Female` | « NB-non-IselinNeural »     | Général |
| `pt-BR` | Portugais (Brésil)             | `Female` | « pt-BR-FranciscaNeural »  | Général |
| `tr-TR` | Turc                         | `Female` | « TR-TR-EmelNeural »       | Général |
| `zh-CN` | Chinois (mandarin, simplifié)  | `Female` | « zh-CN-XiaoxiaoNeural »   | Général, plusieurs styles de voix sont disponibles |
|         |                                 | `Female` | « zh-CN-XiaoyouNeural »    | Voix d’enfant, optimisée pour raconter des histoires |
|         |                                 | `Male`   | « zh-CN-YunyangNeural »    | Optimisée pour la lecture d’actualités, plusieurs styles de voix sont disponibles |
|         |                                 | `Male`   | « zh-CN-YunyeNeural »      | Optimisée pour raconter des histoires |

> [!IMPORTANT]
> La voix `en-US-JessaNeural` a basculé sur `en-US-AriaNeural`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

Pour savoir comment configurer et ajuster les voix neuronales, voir [Langage de balisage de synthèse vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Vous pouvez continuer à utiliser le mappage de nom de service complet comme « Voix Microsoft Server Speech Text to Speech (en-US, AriaNeural) » dans vos requêtes de synthèse vocale.

### <a name="standard-voices"></a>Voix standard

Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, ce qui vous permet de convertir le texte en parole synthétisée. Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

| Paramètres régionaux | Langage | Sexe | Nom de la voix |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabe (Égypte) | `Female` | « ar-EG-Hoda » |
| `ar-SA` | Arabe (Arabie saoudite) | `Male` | « ar-SA-Naayf » |
| `bg-BG` | Bulgare | `Male` |  « bg-BG-Ivan » |
| `ca-ES` | Catalan | `Female` |  « ca-ES-HerenaRUS » |
| `cs-CZ` | Tchèque | `Male` | « cs-CZ-Jakub » |
| `da-DK` | Danois | `Female` |  « da-DK-HelleRUS » |
| `de-AT` | Allemand (Autriche) | `Male` | « de-AT-Michael » |
| `de-CH` | Allemand (Suisse) | `Male` |  « de-CH-Karsten » |
| `de-DE` | Allemand (Allemagne) | `Female` |  « de-DE-Hedda » |
|  |  | `Female` | « de-DE-HeddaRUS » |
|  |  | `Male` |  « de-DE-Stefan-Apollo » |
| `el-GR` | Grec | `Male` | « el-GR-Stefanos » |
| `en-AU` | Anglais (Australie) | `Female` |  « en-AU-Catherine » |
|  |  | `Female` |  « en-AU-HayleyRUS » |
| `en-CA` | Anglais (Canada) | `Female` |  « en-CA-Linda » |
|  |  | `Female` |  « en-CA-HeatherRUS » |
| `en-GB` | Anglais (Royaume-Uni) | `Female` |  « en-GB-Susan-Apollo » |
|  |  | `Female` |  « en-GB-HazelRUS » |
|  |  | `Male` |  « en-GB-George-Apollo » |
| `en-IE` | Anglais (Irlande) | `Male` | « en-IE-Sean » |
| `en-IN` | Anglais (Inde) | `Female` | « en-IN-Heera-Apollo » |
|  |  | `Female` |  « en-IN-PriyaRUS » |
|  |  | `Male` |  « en-IN-Ravi-Apollo » |
| `en-US` | Anglais (US) | `Female` |  « en-US-ZiraRUS » |
|  |  | `Female` | « en-US-AriaRUS » |
|  |  | `Male` | « en-US-BenjaminRUS » |
|  |  | `Male` |  « en-US-Guy24kRUS » |
| `es-ES` | Espagnol (Espagne) | `Female` |  « es-ES-Laura-Apollo » |
|  |  | `Female` | « es-ES-HelenaRUS » |
|  |  | `Male` | « es-ES-Pablo-Apollo » |
| `es-MX` | Espagnol (Mexique) | `Female` |  « es-MX-HildaRUS » |
|  |  | `Male` | « es-MX-Raul-Apollo » |
| `fi-FI` | Finnois | `Female` | « fi-FI-HeidiRUS » |
| `fr-CA` | Français (Canada) | `Female` | « fr-CA-Caroline » |
|  |  | `Female` | « fr-CA-HarmonieRUS » |
| `fr-CH` | Français (Suisse) | `Male` | « fr-CH-Guillaume » |
| `fr-FR` | Français (France) | `Female` |  « fr-FR-Julie-Apollo » |
|  |  | `Female` |« fr-FR-HortenseRUS » |
|  |  | `Male` |  « fr-FR-Paul-Apollo » |
| `he-IL` | Hébreu (Israël) | `Male` |  « he-IL-Asaf » |
| `hi-IN` | Hindi (Inde) | `Female` | « hi-IN-Kalpana-Apollo » |
|  |  | `Female` |  « hi-IN-Kalpana » |
|  |  | `Male` |  « hi-IN-Hemant » |
| `hr-HR` | Croate | `Male` | « hr-HR-Matej » |
| `hu-HU` | Hongrois | `Male` |  « hu-HU-Szabolcs » |
| `id-ID` | Indonésien | `Male` | « id-ID-Andika » |
| `it-IT` | Italien | `Male` |  « it-IT-Cosimo-Apollo » |
|  |  | `Female` |  « it-IT-LuciaRUS » |
| `ja-JP` | Japonais | `Female` |  « ja-JP-Ayumi-Apollo » |
|  |  | `Male` | « ja-JP-Ichiro-Apollo » |
|  |  | `Female` |  « ja-JP-HarukaRUS » |
| `ko-KR` | Coréen | `Female` | « ko-KR-HeamiRUS » |
| `ms-MY` | Malais | `Male` |  « ms-MY-Rizwan » |
| `nb-NO` | Norvégien | `Female` |  « nb-NO-HuldaRUS » |
| `nl-NL` | Néerlandais | `Female` |  « nl-NL-HannaRUS » |
| `pl-PL` | Polonais | `Female` |  « pl-PL-PaulinaRUS » |
| `pt-BR` | Portugais (Brésil) | `Female` | « pt-BR-HeloisaRUS » |
|  |  | `Male` |  « pt-BR-Daniel-Apollo » |
| `pt-PT` | Portugais (Portugal) | `Female` | « pt-PT-HeliaRUS » |
| `ro-RO` | Roumain | `Male` | « ro-RO-Andrei » |
| `ru-RU` | Russe | `Female` |  « ru-RU-Irina-Apollo » |
|  |  | `Male` | « ru-RU-Pavel-Apollo » |
|  |  | `Female` |  « ru-RU-EkaterinaRUS » |
| `sk-SK` | Slovaque | `Male` | « sk-SK-Filip » |
| `sl-SI` | Slovène | `Male` |  « sl-SI-Lado » |
| `sv-SE` | Suédois | `Female` | « sv-SE-HedvigRUS » |
| `ta-IN` | Tamoul (Inde) | `Male` |  « ta-IN-Valluvar » |
| `te-IN` | Télougou (Inde) | `Female` |  « te-IN-Chitra » |
| `th-TH` | Thaï | `Male` |  « th-TH-Pattara » |
| `tr-TR` | Turc (Turquie) | `Female` | « tr-TR-SedaRUS » |
| `vi-VN` | Vietnamien | `Male` |  « vi-VN-An » |
| `zh-CN` | Chinois (mandarin, simplifié) | `Female` |  « zh-CN-HuihuiRUS » |
|  |  | `Female` | « zh-CN-Yaoyao-Apollo » |
|  |  | `Male` | « zh-CN-Kangkang-Apollo » |
| `zh-HK` | Chinois (cantonais, traditionnel) | `Female` |  « zh-HK-Tracy-Apollo » |
|  |  | `Female` | « zh-HK-TracyRUS » |
|  |  | `Male` |  « zh-HK-Danny-Apollo » |
| `zh-TW` | Chinois (mandarin, taïwanais) | `Female` |  « zh-TW-Yating-Apollo » |
|  |  | `Female` | « zh-TW-HanHanRUS » |
|  |  | `Male` |  « zh-TW-Zhiwei-Apollo » |

**1** *ar-EG prend en charge l’arabe standard moderne (MSA).*

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
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
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

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
