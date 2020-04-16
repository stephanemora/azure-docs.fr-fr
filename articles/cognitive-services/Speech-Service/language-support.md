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
ms.openlocfilehash: 2a876349af2bd53a46737dda82961afe442bae05
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401080"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Prise en charge des langues et de la voix pour le service Speech

La prise en charge des langues varie selon les fonctionnalités du service Speech. Les tableaux suivants résument la prise en charge des langues pour les offres de service [Reconnaissance vocale](#speech-to-text), [Synthèse vocale](#text-to-speech) et [Traduction vocale](#speech-translation).

## <a name="speech-to-text"></a>Reconnaissance vocale

Le Kit de développement logiciel (SDK) et l’API REST Speech de Microsoft prennent en charge les langages suivants (paramètres régionaux). Afin d'améliorer la précision, la personnalisation est proposée pour un sous-ensemble de langues en téléchargeant des transcriptions audio + étiquetées à la main ou des textes associés : Phrases. La personnalisation de la prononciation est actuellement disponible uniquement pour `en-US` et `de-DE`. Pour en savoir plus sur la personnalisation, cliquez [ici](how-to-custom-speech.md).

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
| `ar-KW` | Arabe (Koweït)                   | Oui       | Non                                                |
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
| `tr-TR` | Turc (Turquie)                  | Oui       | Non                                                |
| `zh-CN` | Chinois (mandarin, simplifié)    | Oui       | Modèle acoustique<br>Modèle de langage                  |
| `zh-HK` | Chinois (cantonais, traditionnel)  | Oui       | Modèle de langage                                    |
| `zh-TW` | Chinois (mandarin, taïwanais)      | Oui       | Modèle de langage                                    |

## <a name="text-to-speech"></a>Synthèse vocale

Le Kit de développement logiciel (SDK) Speech de Microsoft et les API REST prennent en charge ces voix qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux.

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, visitez la page [Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voix neurales

La synthèse vocale neuronale est un nouveau type de synthèse vocale alimentée par les réseaux neuronaux profonds. Lorsque vous utilisez une voix neuronale, il est presque impossible de distinguer la voix synthétisée des enregistrements humains.

Les voix neurales peuvent être utilisées pour rendre les interactions avec les chatbots et des assistants vocaux plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Grâce à la prosodie naturelle quasi humaine et à la bonne articulation des mots, les voix neuronales réduisent considérablement la fatigue d’écoute des utilisateurs qui interagissent avec les systèmes d’intelligence artificielle.

Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

| Paramètres régionaux  | Langage            | Sexe | Mappage du nom complet du service                                               | Nom court de la voix        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Allemand (Allemagne)    | Female | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (de-DE, KatjaNeural) »     | « de-DE-KatjaNeural »     |
| `en-US` | Anglais (US)        | Female | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, AriaNeural) »      | « en-US-AriaNeural »      |
| `en-US` | Anglais (US)        | Male   | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, GuyNeural) »       | « en-US-GuyNeural »       |
| `it-IT` | Italien (Italie)     | Female | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (it-IT, ElsaNeural) »      | « it-IT-ElsaNeural »      |
| `pt-BR` | Portugais (Brésil) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, FranciscaNeural) » | « pt-BR-FranciscaNeural » |
| `zh-CN` | Chinois (mandarin, simplifié)  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, XiaoxiaoNeural) »  | « zh-CN-XiaoxiaoNeural »  |

> [!IMPORTANT]
> La voix `en-US-JessaNeural` a basculé sur `en-US-AriaNeural`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

Pour savoir comment configurer et ajuster les voix neuronales, voir [Langage de balisage de synthèse vocale](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Il est possible d’utiliser le mappage du nom complet du service ou le nom court de la voix dans les demandes de synthèse vocale.

### <a name="standard-voices"></a>Voix standard

Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, ce qui vous permet de convertir le texte en parole synthétisée. Pour plus d’informations sur la disponibilité régionale, consultez [régions](regions.md#standard-and-neural-voices).

| Paramètres régionaux | Langage | Sexe | Mappage du nom complet du service | Nom court |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabe (Égypte) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-EG, Hoda) » | « ar-EG-Hoda » |
| `ar-SA` | Arabe (Arabie saoudite) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-SA, Naayf) » | « ar-SA-Naayf » |
| `bg-BG` | Bulgare | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (bg-BG, Ivan) » | « bg-BG-Ivan » |
| `ca-ES` | Catalan (Espagne) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (ca-ES, HerenaRUS) » | « ca-ES-HerenaRUS » |
| `cs-CZ` | Tchèque | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (cs-CZ, Jakub) » | « cs-CZ-Jakub » |
| `da-DK` | Danois | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (da-DK, HelleRUS) » | « da-DK-HelleRUS » |
| `de-AT` | Allemand (Autriche) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-AT, Michael) » | « de-AT-Michael » |
| `de-CH` | Allemand (Suisse) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-CH, Karsten) » | « de-CH-Karsten » |
| `de-DE` | Allemand (Allemagne) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Hedda) » | « de-DE-Hedda » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, HeddaRUS) » | « de-DE-HeddaRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Stefan, Apollo) » | « de-DE-Stefan-Apollo » |
| `el-GR` | Grec | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (el-GR, Stefanos) » | « el-GR-Stefanos » |
| `en-AU` | Anglais (Australie) | Female | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-AU, Catherine) » | « en-AU-Catherine » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-AU, HayleyRUS) » | « en-AU-HayleyRUS » |
| `en-CA` | Anglais (Canada) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, Linda) » | « en-CA-Linda » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, HeatherRUS) » | « en-CA-HeatherRUS » |
| `en-GB` | Anglais (Royaume-Uni) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, Susan, Apollo) » | « en-GB-Susan-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, HazelRUS) » | « en-GB-HazelRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, George, Apollo) » | « en-GB-George-Apollo » |
| `en-IE` | Anglais (Irlande) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IE, Sean) » | « en-IE-Sean » |
| `en-IN` | Anglais (Inde) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Heera, Apollo) » | « en-IN-Heera-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, PriyaRUS) » | « en-IN-PriyaRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Ravi, Apollo) » | « en-IN-Ravi-Apollo » |
| `en-US` | Anglais (US) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, ZiraRUS) » | « en-US-ZiraRUS » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, AriaRUS) » | « en-US-AriaRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, BenjaminRUS) » | « en-US-BenjaminRUS » |
|  |  | Male | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Guy24kRUS) » | « en-US-Guy24kRUS » |
| `es-ES` | Espagnol (Espagne) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Laura, Apollo) » | « es-ES-Laura-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, HelenaRUS) » | « es-ES-HelenaRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Pablo, Apollo) » | « es-ES-Pablo-Apollo » |
| `es-MX` | Espagnol (Mexique) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, HildaRUS) » | « es-MX-HildaRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, Raul, Apollo) » | « es-MX-Raul-Apollo » |
| `fi-FI` | Finnois | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (fi-FI, HeidiRUS) » | « fi-FI-HeidiRUS » |
| `fr-CA` | Français (Canada) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, Caroline) » | « fr-CA-Caroline » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, HarmonieRUS) » | « fr-CA-HarmonieRUS » |
| `fr-CH` | Français (Suisse) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CH, Guillaume) » | « fr-CH-Guillaume » |
| `fr-FR` | Français (France) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Julie, Apollo) » | « fr-FR-Julie-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, HortenseRUS) » | « fr-FR-HortenseRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Paul, Apollo) » | « fr-FR-Paul-Apollo » |
| `he-IL` | Hébreu (Israël) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (he-IL, Asaf) » | « he-IL-Asaf » |
| `hi-IN` | Hindi (Inde) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana, Apollo) » | « hi-IN-Kalpana-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana) » | « hi-IN-Kalpana » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Hemant) » | « hi-IN-Hemant » |
| `hr-HR` | Croate | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (hr-HR, Matej) » | « hr-HR-Matej » |
| `hu-HU` | Hongrois | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (hu-HU, Szabolcs) » | « hu-HU-Szabolcs » |
| `id-ID` | Indonésien | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (id-ID, Andika) » | « id-ID-Andika » |
| `it-IT` | Italien | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (it-IT, Cosimo, Apollo) » | « it-IT-Cosimo-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (it-IT, LuciaRUS) » | « it-IT-LuciaRUS » |
| `ja-JP` | Japonais | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ayumi, Apollo) » | « ja-JP-Ayumi-Apollo » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ichiro, Apollo) » | « ja-JP-Ichiro-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, HarukaRUS) » | « ja-JP-HarukaRUS » |
| `ko-KR` | Coréen | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (ko-KR, HeamiRUS) » | « ko-KR-HeamiRUS » |
| `ms-MY` | Malais | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (ms-MY, Rizwan) » | « ms-MY-Rizwan » |
| `nb-NO` | Norvégien | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (nb-NO, HuldaRUS) » | « nb-NO-HuldaRUS » |
| `nl-NL` | Néerlandais | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (nl-NL, HannaRUS) » | « nl-NL-HannaRUS » |
| `pl-PL` | Polonais | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (pl-PL, PaulinaRUS) » | « pl-PL-PaulinaRUS » |
| `pt-BR` | Portugais (Brésil) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, HeloisaRUS) » | « pt-BR-HeloisaRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, Daniel, Apollo) » | « pt-BR-Daniel-Apollo » |
| `pt-PT` | Portugais (Portugal) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-PT, HeliaRUS) » | « pt-PT-HeliaRUS » |
| `ro-RO` | Roumain | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (ro-RO, Andrei) » | « ro-RO-Andrei » |
| `ru-RU` | Russe | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Irina, Apollo) » | « ru-RU-Irina-Apollo » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Pavel, Apollo) » | « ru-RU-Pavel-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (ru-RU, Pavel, Apollo) » | « ru-RU-EkaterinaRUS » |
| `sk-SK` | Slovaque | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (sk-SK, Filip) » | « sk-SK-Filip » |
| `sl-SI` | Slovène | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (sl-SI, Lado) » | « sl-SI-Lado » |
| `sv-SE` | Suédois | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (sv-SE, HedvigRUS) » | « sv-SE-HedvigRUS » |
| `ta-IN` | Tamoul (Inde) | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (ta-IN, Valluvar) » | « ta-IN-Valluvar » |
| `te-IN` | Télougou (Inde) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (te-IN, Chitra) » | « te-IN-Chitra » |
| `th-TH` | Thaï | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (th-TH, Pattara) » | « th-TH-Pattara » |
| `tr-TR` | Turc (Turquie) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (tr-TR, SedaRUS) » | « tr-TR-SedaRUS » |
| `vi-VN` | Vietnamien | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (vi-VN, An) » | « vi-VN-An » |
| `zh-CN` | Chinois (mandarin, simplifié) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, HuihuiRUS) » | « zh-CN-HuihuiRUS » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Yaoyao, Apollo) » | « zh-CN-Yaoyao-Apollo » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Kangkang, Apollo) » | « zh-CN-Kangkang-Apollo » |
| `zh-HK` | Chinois (cantonais, traditionnel) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Tracy, Apollo) » | « zh-HK-Tracy-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, TracyRUS) » | « zh-HK-TracyRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Danny, Apollo) » | « zh-HK-Danny-Apollo » |
| `zh-TW` | Chinois (mandarin, taïwanais) | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Yating, Apollo) » | « zh-TW-Yating-Apollo » |
|  |  | Female | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, HanHanRUS) » | « zh-TW-HanHanRUS » |
|  |  | Male | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Zhiwei, Apollo) » | « zh-TW-Zhiwei-Apollo » |

**1** *ar-EG prend en charge l’arabe standard moderne (MSA).*

> [!IMPORTANT]
> La voix `en-US-Jessa` a basculé sur `en-US-Aria`. Si vous utilisiez « Jessa », procédez à une conversion vers « Aria ».

> [!TIP]
> Il est possible d’utiliser le mappage du nom complet du service ou le nom court de la voix dans les demandes de synthèse vocale.

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
