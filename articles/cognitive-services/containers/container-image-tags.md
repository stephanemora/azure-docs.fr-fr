---
title: Étiquettes des images conteneur Cognitive Services
titleSuffix: Azure Cognitive Services
description: Liste complète de toutes les étiquettes des images conteneur Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e0df3de5eadfd2cc5c00c52da5c4942b42a68b2b
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722566"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Étiquettes des images conteneur Azure Cognitive Services

Azure Cognitive Services propose de nombreuses images conteneur. Les registres de conteneurs et les dépôts correspondants varient d’une image conteneur à l’autre. Chaque nom d’image conteneur offre plusieurs étiquettes. Une étiquette d’image conteneur est un mécanisme de versioning de l’image conteneur. Cet article est destiné à servir de référence exhaustive pour lister toutes les images conteneur Cognitive Services et leurs étiquettes disponibles.

> [!TIP]
> Quand vous utilisez [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), faites particulièrement attention à la casse utilisée dans les noms du registre de conteneurs, du référentiel, de l’image conteneur et de l’étiquette correspondante, car ces noms **respectent la casse**.

## <a name="anomaly-detector"></a>Le détecteur d’anomalies

L’image conteneur [Détecteur d’anomalies][ad-containers] se trouve dans le registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services` et se nomme `anomaly-detector`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/anomaly-detector`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Vision par ordinateur

L’image conteneur OCR Lire [Vision par ordinateur][cv-containers] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-read`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • Réduire davantage l’utilisation de la mémoire pour le conteneur. |
|                                          | • Le cache externe est requis pour la configuration de plusieurs pods. Par exemple, configurez Redis pour la mise en cache. |
|                                          | • Correction du problème des résultats manquants quand le cache Redis est configuré et ResultExpirationPeriod = 0.  |
|                                          | • Supprimer la limite de taille du corps de la demande de 26 Mo. Le conteneur peut désormais accepter des fichiers avec une taille supérieure à 26 Mo.  |
|                                          | • Ajouter l’horodatage et la version de build à l’écriture dans le journal de la console.  |
| `1.1.013050001-amd64-preview`            | * Ajout de la configuration de l’initialisation de conteneur ReadEngineConfig:ResultExpirationPeriod pour spécifier le moment où le système doit nettoyer les résultats de la reconnaissance. |
|                                          | Le paramètre s’affiche en heures et la valeur par défaut est 48 heures.   |
|                                          |   Le paramètre peut réduire l’utilisation de la mémoire pour le stockage des résultats, en particulier quand le stockage en mémoire du conteneur est utilisé.  |
|                                          |    * Exemple 1. ReadEngineConfig:ResultExpirationPeriod = 1, le système efface le résultat de la reconnaissance 1 heure après le processus.   |
|                                          |    * Exemple 2. ReadEngineConfig:ResultExpirationPeriod = 0, le système efface le résultat de la reconnaissance après la récupération des résultats.  |
|                                          | Correction d’une erreur de serveur interne 500 lorsque le format d’image non valide est transmis au système. Une erreur 400 est maintenant retournée :   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

L’image conteneur [Visage][fa-containers] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-face`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Form Recognizer

L’image conteneur [Form Recognizer][fr-containers] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-form-recognizer`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L’image conteneur [LUIS][lu-containers] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services` et se nomme `luis`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/luis`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Reconnaissance vocale personnalisée

L’image conteneur [Reconnaissance vocale personnalisée][sp-cstt] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-custom-speech-to-text`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image            | Notes |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Synthèse vocale personnalisée

L’image conteneur [Synthèse vocale personnalisée][sp-ctts] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-custom-text-to-speech`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image            | Notes |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Reconnaissance vocale

L’image conteneur [Reconnaissance vocale][sp-stt] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-speech-to-text`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                  | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.2.0-amd64-ar-ae-preview` | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `2.2.0-amd64-ar-eg-preview` | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `2.2.0-amd64-ar-kw-preview` | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `2.2.0-amd64-ar-qa-preview` | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `2.2.0-amd64-ar-sa-preview` | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `2.2.0-amd64-ca-es-preview` | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `2.2.0-amd64-da-dk-preview` | Image conteneur avec les paramètres régionaux `da-DK`. |
| `2.2.0-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `2.2.0-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `2.2.0-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `2.2.0-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `2.2.0-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `2.2.0-amd64-en-nz-preview` | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `2.2.0-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.2.0-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `2.2.0-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `2.2.0-amd64-fi-fi-preview` | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `2.2.0-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `2.2.0-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `2.2.0-amd64-gu-in-preview` | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `2.2.0-amd64-hi-in-preview` | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `2.2.0-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `2.2.0-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.2.0-amd64-ko-kr-preview` | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `2.2.0-amd64-mr-in-preview` | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `2.2.0-amd64-nb-no-preview` | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `2.2.0-amd64-nl-nl-preview` | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `2.2.0-amd64-pl-pl-preview` | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `2.2.0-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `2.2.0-amd64-pt-pt-preview` | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `2.2.0-amd64-ru-ru-preview` | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `2.2.0-amd64-sv-se-preview` | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `2.2.0-amd64-ta-in-preview` | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `2.2.0-amd64-te-in-preview` | Image conteneur avec les paramètres régionaux `te-IN`. |
| `2.2.0-amd64-th-th-preview` | Image conteneur avec les paramètres régionaux `th-TH`. |
| `2.2.0-amd64-tr-tr-preview` | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `2.2.0-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `2.2.0-amd64-zh-hk-preview` | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `2.2.0-amd64-zh-tw-preview` | Image conteneur avec les paramètres régionaux `zh-TW`. |
| `2.1.1-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.1.1-amd64-ar-ae-preview` | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `2.1.1-amd64-ar-eg-preview` | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `2.1.1-amd64-ar-kw-preview` | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `2.1.1-amd64-ar-qa-preview` | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `2.1.1-amd64-ar-sa-preview` | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `2.1.1-amd64-ca-es-preview` | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `2.1.1-amd64-da-dk-preview` | Image conteneur avec les paramètres régionaux `da-DK`. |
| `2.1.1-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `2.1.1-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `2.1.1-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `2.1.1-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `2.1.1-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `2.1.1-amd64-en-nz-preview` | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `2.1.1-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.1.1-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `2.1.1-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `2.1.1-amd64-fi-fi-preview` | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `2.1.1-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `2.1.1-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `2.1.1-amd64-gu-in-preview` | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `2.1.1-amd64-hi-in-preview` | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `2.1.1-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `2.1.1-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.1.1-amd64-ko-kr-preview` | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `2.1.1-amd64-mr-in-preview` | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `2.1.1-amd64-nb-no-preview` | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `2.1.1-amd64-nl-nl-preview` | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `2.1.1-amd64-pl-pl-preview` | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `2.1.1-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `2.1.1-amd64-pt-pt-preview` | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `2.1.1-amd64-ru-ru-preview` | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `2.1.1-amd64-sv-se-preview` | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `2.1.1-amd64-ta-in-preview` | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `2.1.1-amd64-te-in-preview` | Image conteneur avec les paramètres régionaux `te-IN`. |
| `2.1.1-amd64-th-th-preview` | Image conteneur avec les paramètres régionaux `th-TH`. |
| `2.1.1-amd64-tr-tr-preview` | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `2.1.1-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `2.1.1-amd64-zh-hk-preview` | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `2.1.1-amd64-zh-tw-preview` | Image conteneur avec les paramètres régionaux `zh-TW`. |
| `2.1.0-amd64-ar-ae-preview` | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `2.1.0-amd64-ar-eg-preview` | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `2.1.0-amd64-ar-kw-preview` | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `2.1.0-amd64-ar-qa-preview` | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `2.1.0-amd64-ar-sa-preview` | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `2.1.0-amd64-ca-es-preview` | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `2.1.0-amd64-da-dk-preview` | Image conteneur avec les paramètres régionaux `da-DK`. |
| `2.1.0-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `2.1.0-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `2.1.0-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `2.1.0-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `2.1.0-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `2.1.0-amd64-en-nz-preview` | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `2.1.0-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.1.0-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `2.1.0-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `2.1.0-amd64-fi-fi-preview` | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `2.1.0-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `2.1.0-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `2.1.0-amd64-gu-in-preview` | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `2.1.0-amd64-hi-in-preview` | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `2.1.0-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `2.1.0-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.1.0-amd64-ko-kr-preview` | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `2.1.0-amd64-mr-in-preview` | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `2.1.0-amd64-nb-no-preview` | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `2.1.0-amd64-nl-nl-preview` | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `2.1.0-amd64-pl-pl-preview` | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `2.1.0-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `2.1.0-amd64-pt-pt-preview` | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `2.1.0-amd64-ru-ru-preview` | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `2.1.0-amd64-sv-se-preview` | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `2.1.0-amd64-ta-in-preview` | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `2.1.0-amd64-te-in-preview` | Image conteneur avec les paramètres régionaux `te-IN`. |
| `2.1.0-amd64-th-th-preview` | Image conteneur avec les paramètres régionaux `th-TH`. |
| `2.1.0-amd64-tr-tr-preview` | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `2.1.0-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `2.1.0-amd64-zh-hk-preview` | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `2.1.0-amd64-zh-tw-preview` | Image conteneur avec les paramètres régionaux `zh-TW`. |
| `2.0.3-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.0.2-amd64-ar-ae-preview` | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `2.0.2-amd64-ar-eg-preview` | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `2.0.2-amd64-ar-kw-preview` | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `2.0.2-amd64-ar-qa-preview` | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `2.0.2-amd64-ar-sa-preview` | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `2.0.2-amd64-ca-es-preview` | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `2.0.2-amd64-da-dk-preview` | Image conteneur avec les paramètres régionaux `da-DK`. |
| `2.0.2-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `2.0.2-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `2.0.2-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `2.0.2-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `2.0.2-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `2.0.2-amd64-en-nz-preview` | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `2.0.2-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.0.2-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `2.0.2-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `2.0.2-amd64-fi-fi-preview` | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `2.0.2-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `2.0.2-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `2.0.2-amd64-gu-in-preview` | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `2.0.2-amd64-hi-in-preview` | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `2.0.2-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `2.0.2-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.0.2-amd64-ko-kr-preview` | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `2.0.2-amd64-mr-in-preview` | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `2.0.2-amd64-nb-no-preview` | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `2.0.2-amd64-nl-nl-preview` | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `2.0.2-amd64-pl-pl-preview` | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `2.0.2-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `2.0.2-amd64-pt-pt-preview` | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `2.0.2-amd64-ru-ru-preview` | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `2.0.2-amd64-sv-se-preview` | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `2.0.2-amd64-ta-in-preview` | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `2.0.2-amd64-te-in-preview` | Image conteneur avec les paramètres régionaux `te-IN`. |
| `2.0.2-amd64-th-th-preview` | Image conteneur avec les paramètres régionaux `th-TH`. |
| `2.0.2-amd64-tr-tr-preview` | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `2.0.2-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `2.0.2-amd64-zh-hk-preview` | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `2.0.2-amd64-zh-tw-preview` | Image conteneur avec les paramètres régionaux `zh-TW`. |
| `2.0.1-amd64-ar-ae-preview` | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `2.0.1-amd64-ar-eg-preview` | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `2.0.1-amd64-ar-kw-preview` | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `2.0.1-amd64-ar-qa-preview` | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `2.0.1-amd64-ar-sa-preview` | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `2.0.1-amd64-ca-es-preview` | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `2.0.1-amd64-da-dk-preview` | Image conteneur avec les paramètres régionaux `da-DK`. |
| `2.0.1-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `2.0.1-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `2.0.1-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `2.0.1-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `2.0.1-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `2.0.1-amd64-en-nz-preview` | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `2.0.1-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.0.1-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `2.0.1-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `2.0.1-amd64-fi-fi-preview` | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `2.0.1-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `2.0.1-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `2.0.1-amd64-gu-in-preview` | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `2.0.1-amd64-hi-in-preview` | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `2.0.1-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `2.0.1-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.0.1-amd64-ko-kr-preview` | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `2.0.1-amd64-mr-in-preview` | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `2.0.1-amd64-nb-no-preview` | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `2.0.1-amd64-nl-nl-preview` | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `2.0.1-amd64-pl-pl-preview` | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `2.0.1-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `2.0.1-amd64-pt-pt-preview` | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `2.0.1-amd64-ru-ru-preview` | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `2.0.1-amd64-sv-se-preview` | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `2.0.1-amd64-ta-in-preview` | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `2.0.1-amd64-te-in-preview` | Image conteneur avec les paramètres régionaux `te-IN`. |
| `2.0.1-amd64-th-th-preview` | Image conteneur avec les paramètres régionaux `th-TH`. |
| `2.0.1-amd64-tr-tr-preview` | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `2.0.1-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `2.0.1-amd64-zh-hk-preview` | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `2.0.1-amd64-zh-tw-preview` | Image conteneur avec les paramètres régionaux `zh-TW`. |
| `2.0.0-amd64-ar-eg-preview` | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `2.0.0-amd64-ca-es-preview` | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `2.0.0-amd64-da-dk-preview` | Image conteneur avec les paramètres régionaux `da-DK`. |
| `2.0.0-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `2.0.0-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `2.0.0-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `2.0.0-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `2.0.0-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `2.0.0-amd64-en-nz-preview` | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `2.0.0-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `2.0.0-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `2.0.0-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `2.0.0-amd64-fi-fi-preview` | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `2.0.0-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `2.0.0-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `2.0.0-amd64-hi-in-preview` | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `2.0.0-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `2.0.0-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `2.0.0-amd64-ko-kr-preview` | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `2.0.0-amd64-nb-no-preview` | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `2.0.0-amd64-nl-nl-preview` | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `2.0.0-amd64-pl-pl-preview` | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `2.0.0-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `2.0.0-amd64-pt-pt-preview` | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `2.0.0-amd64-ru-ru-preview` | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `2.0.0-amd64-sv-se-preview` | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `2.0.0-amd64-th-th-preview` | Image conteneur avec les paramètres régionaux `th-TH`. |
| `2.0.0-amd64-tr-tr-preview` | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `2.0.0-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `2.0.0-amd64-zh-hk-preview` | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `2.0.0-amd64-zh-tw-preview` | Image conteneur avec les paramètres régionaux `zh-TW`. |
| `1.2.0-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `1.2.0-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `1.2.0-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `1.2.0-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `1.2.0-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `1.2.0-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `1.2.0-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `1.2.0-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `1.2.0-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `1.2.0-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `1.2.0-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `1.2.0-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `1.2.0-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `1.2.0-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `1.1.3-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `1.1.3-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `1.1.3-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `1.1.3-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `1.1.3-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `1.1.3-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `1.1.3-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `1.1.3-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `1.1.3-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `1.1.3-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `1.1.3-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `1.1.3-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `1.1.3-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `1.1.3-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `1.1.2-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `1.1.2-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `1.1.2-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `1.1.2-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `1.1.2-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `1.1.2-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `1.1.2-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `1.1.2-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `1.1.2-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `1.1.2-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `1.1.2-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `1.1.2-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `1.1.2-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `1.1.2-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `1.1.1-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `1.1.1-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `1.1.1-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `1.1.1-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `1.1.1-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `1.1.1-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `1.1.1-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `1.1.1-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `1.1.1-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `1.1.1-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `1.1.1-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `1.1.1-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `1.1.1-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `1.1.1-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `1.1.0-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `1.1.0-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `1.1.0-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `1.1.0-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `1.1.0-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `1.1.0-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `1.1.0-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `1.1.0-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `1.1.0-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `1.1.0-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `1.1.0-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `1.1.0-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `1.1.0-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `1.1.0-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `1.0.0-amd64-de-de-preview` | Image conteneur avec les paramètres régionaux `de-DE`. |
| `1.0.0-amd64-en-au-preview` | Image conteneur avec les paramètres régionaux `en-AU`. |
| `1.0.0-amd64-en-ca-preview` | Image conteneur avec les paramètres régionaux `en-CA`. |
| `1.0.0-amd64-en-gb-preview` | Image conteneur avec les paramètres régionaux `en-GB`. |
| `1.0.0-amd64-en-in-preview` | Image conteneur avec les paramètres régionaux `en-IN`. |
| `1.0.0-amd64-en-us-preview` | Image conteneur avec les paramètres régionaux `en-US`. |
| `1.0.0-amd64-es-es-preview` | Image conteneur avec les paramètres régionaux `es-ES`. |
| `1.0.0-amd64-es-mx-preview` | Image conteneur avec les paramètres régionaux `es-MX`. |
| `1.0.0-amd64-fr-ca-preview` | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `1.0.0-amd64-fr-fr-preview` | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `1.0.0-amd64-it-it-preview` | Image conteneur avec les paramètres régionaux `it-IT`. |
| `1.0.0-amd64-ja-jp-preview` | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `1.0.0-amd64-pt-br-preview` | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `1.0.0-amd64-zh-cn-preview` | Image conteneur avec les paramètres régionaux `zh-CN`. |

## <a name="text-to-speech"></a>Synthèse vocale

L’image conteneur [Synthèse vocale][sp-tts] se trouve dans le registre de conteneurs `containerpreview.azurecr.io`. Elle réside dans le référentiel `microsoft` et se nomme `cognitive-services-text-to-speech`. Le nom complet de l’image conteneur est `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                                  | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-JessaRUS`.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Image conteneur avec les paramètres régionaux `ar-EG` et la voix `ar-EG-Hoda`.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Image conteneur avec les paramètres régionaux `ar-SA` et la voix `ar-SA-Naayf`.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Image conteneur avec les paramètres régionaux `bg-BG` et la voix `bg-BG-Ivan`.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Image conteneur avec les paramètres régionaux `ca-ES` et la voix `ca-ES-HerenaRUS`.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Image conteneur avec les paramètres régionaux `cs-CZ` et la voix `cs-CZ-Jakub`.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Image conteneur avec les paramètres régionaux `da-DK` et la voix `da-DK-HelleRUS`.        |
| `1.3.0-amd64-de-at-michael-preview`         | Image conteneur avec les paramètres régionaux `de-AT` et la voix `de-AT-Michael`.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Image conteneur avec les paramètres régionaux `de-CH` et la voix `de-CH-Karsten`.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-HeddaRUS`.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Stefan-Apollo`.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Image conteneur avec les paramètres régionaux `el-GR` et la voix `el-GR-Stefanos`.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-Catherine`.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-HayleyRUS`.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-HeatherRUS`.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-Linda`.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-George-Apollo`.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-HazelRUS`.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-Susan-Apollo`.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Image conteneur avec les paramètres régionaux `en-IE` et la voix `en-IE-Sean`.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Heera-Apollo`.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-PriyaRUS`.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Ravi-Apollo`.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-BenjaminRUS`.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Guy24kRUS`.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Jessa24kRUS`.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-JessaRUS`.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-ZiraRUS`.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-HelenaRUS`.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Laura-Apollo`.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Pablo-Apollo`.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-HildaRUS`.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-Raul-Apollo`.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Image conteneur avec les paramètres régionaux `fi-FI` et la voix `fi-FI-HeidiRUS`.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-Caroline`.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-HarmonieRUS`.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Image conteneur avec les paramètres régionaux `fr-CH` et la voix `fr-CH-Guillaume`.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-HortenseRUS`.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Julie-Apollo`.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Paul-Apollo`.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Image conteneur avec les paramètres régionaux `he-IL` et la voix `he-IL-Asaf`.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Hemant`.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana-Apollo`.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana`.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana`.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Image conteneur avec les paramètres régionaux `hr-HR` et la voix `hr-HR-Matej`.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Image conteneur avec les paramètres régionaux `hu-HU` et la voix `hu-HU-Szabolcs`.        |
| `1.3.0-amd64-id-id-andika-preview`          | Image conteneur avec les paramètres régionaux `id-ID` et la voix `id-ID-Andika`.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-Cosimo-Apollo`.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-LuciaRUS`.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ayumi-Apollo`.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-HarukaRUS`.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ichiro-Apollo`.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Image conteneur avec les paramètres régionaux `ko-KR` et la voix `ko-KR-HeamiRUS`.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Image conteneur avec les paramètres régionaux `ms-MY` et la voix `ms-MY-Rizwan`.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Image conteneur avec les paramètres régionaux `nb-NO` et la voix `nb-NO-HuldaRUS`.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Image conteneur avec les paramètres régionaux `nl-NL` et la voix `nl-NL-HannaRUS`.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Image conteneur avec les paramètres régionaux `pl-PL` et la voix `pl-PL-PaulinaRUS`.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-Daniel-Apollo`.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-HeloisaRUS`.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Image conteneur avec les paramètres régionaux `pt-PT` et la voix `pt-PT-HeliaRUS`.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Image conteneur avec les paramètres régionaux `ro-RO` et la voix `ro-RO-Andrei`.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-EkaterinaRUS`.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-Irina-Apollo`.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-Pavel-Apollo`.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Image conteneur avec les paramètres régionaux `sk-SK` et la voix `sk-SK-Filip`.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Image conteneur avec les paramètres régionaux `sl-SI` et la voix `sl-SI-Lado`.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Image conteneur avec les paramètres régionaux `sv-SE` et la voix `sv-SE-HedvigRUS`.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Image conteneur avec les paramètres régionaux `ta-IN` et la voix `ta-IN-Valluvar`.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Image conteneur avec les paramètres régionaux `te-IN` et la voix `te-IN-Chitra`.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Image conteneur avec les paramètres régionaux `th-TH` et la voix `th-TH-Pattara`.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Image conteneur avec les paramètres régionaux `tr-TR` et la voix `tr-TR-SedaRUS`.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Image conteneur avec les paramètres régionaux `vi-VN` et la voix `vi-VN-An`.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-HuihuiRUS`.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Kangkang-Apollo`. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Yaoyao-Apollo`.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-Danny-Apollo`.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-Tracy-Apollo`.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-TracyRUS`.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-HanHanRUS`.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-Yating-Apollo`.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-Zhiwei-Apollo`.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-HeddaRUS`.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Stefan-Apollo`.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-Catherine`.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-HayleyRUS`.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-George-Apollo`.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-HazelRUS`.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-Susan-Apollo`.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Heera-Apollo`.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-PriyaRUS`.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Ravi-Apollo`.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-BenjaminRUS`.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Guy24kRUS`.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Jessa24kRUS`.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-JessaRUS`.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-ZiraRUS`.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-HelenaRUS`.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Laura-Apollo`.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Pablo-Apollo`.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-HildaRUS`.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-Raul-Apollo`.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-Caroline`.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-HarmonieRUS`.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-HortenseRUS`.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Julie-Apollo`.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Paul-Apollo`.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-Cosimo-Apollo`.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-LuciaRUS`.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ayumi-Apollo`.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-HarukaRUS`.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ichiro-Apollo`.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Image conteneur avec les paramètres régionaux `ko-KR` et la voix `ko-KR-HeamiRUS`.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-Daniel-Apollo`.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-HeloisaRUS`.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-HuihuiRUS`.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Kangkang-Apollo`. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Yaoyao-Apollo`.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-HeddaRUS`.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Stefan-Apollo`.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-Catherine`.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-HayleyRUS`.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-George-Apollo`.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-HazelRUS`.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-Susan-Apollo`.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Heera-Apollo`.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-PriyaRUS`.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Ravi-Apollo`.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-BenjaminRUS`.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Guy24kRUS`.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Jessa24kRUS`.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-JessaRUS`.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-ZiraRUS`.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-HelenaRUS`.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Laura-Apollo`.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Pablo-Apollo`.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-HildaRUS`.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-Raul-Apollo`.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-Caroline`.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-HarmonieRUS`.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-HortenseRUS`.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Julie-Apollo`.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Paul-Apollo`.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-Cosimo-Apollo`.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-LuciaRUS`.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ayumi-Apollo`.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-HarukaRUS`.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ichiro-Apollo`.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Image conteneur avec les paramètres régionaux `ko-KR` et la voix `ko-KR-HeamiRUS`.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-Daniel-Apollo`.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-HeloisaRUS`.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-HuihuiRUS`.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Kangkang-Apollo`. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Yaoyao-Apollo`.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-BenjaminRUS`.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Guy24kRUS`.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Jessa24kRUS`.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-JessaRUS`.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-ZiraRUS`.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-HuihuiRUS`.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Kangkang-Apollo`. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Yaoyao-Apollo`.   |

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

L’image conteneur [Extraction de phrases clés][ta-kp] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services` et se nomme `keyphrase`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Détection de la langue

L’image conteneur [Détection de langue][ta-la] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services` et se nomme `language`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/language`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analyse des sentiments

L’image conteneur [Analyse des sentiments][ta-se] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services` et se nomme `sentiment`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Les étiquettes suivantes sont disponibles pour cette image conteneur :

| Étiquettes d’image | Notes                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Analyse des sentiments v3 (anglais)               |
| `3.0-es`   | Analyse des sentiments v3 (espagnol)               |
| `3.0-fr`   | Analyse des sentiments v3 (français)                |
| `3.0-it`   | Analyse des sentiments v3 (italien)               |
| `3.0-de`   | Analyse des sentiments v3 (allemand)                |
| `3.0-zh`   | Analyse des sentiments v3 (chinois – simplifié)  |
| `3.0-zht`  | Analyse des sentiments v3 (chinois – traditionnel) |
| `3.0-ja`   | Analyse des sentiments v3 (japonais)              |
| `3.0-pt`   | Analyse des sentiments v3 (portugais)            |
| `3.0-nl`   | Analyse des sentiments v3 (néerlandais)                 |
| `1.1.009301-amd64-preview`    | Analyse des sentiments v2      |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
