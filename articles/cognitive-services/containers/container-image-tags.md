---
title: Étiquettes des images conteneur Cognitive Services
titleSuffix: Azure Cognitive Services
description: Liste complète de toutes les étiquettes des images conteneur Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412542"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Étiquettes et notes de publication des images conteneur Azure Cognitive Services

Azure Cognitive Services propose de nombreuses images conteneur. Les registres de conteneurs et les dépôts correspondants varient d’une image conteneur à l’autre. Chaque nom d’image conteneur offre plusieurs étiquettes. Une étiquette d’image conteneur est un mécanisme de versioning de l’image conteneur. Cet article est destiné à servir de référence exhaustive pour lister toutes les images conteneur Cognitive Services et leurs étiquettes disponibles.

> [!TIP]
> Quand vous utilisez [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), faites particulièrement attention à la casse utilisée dans les noms du registre de conteneurs, du référentiel, de l’image conteneur et de l’étiquette correspondante, car ces noms **respectent la casse**.

## <a name="anomaly-detector"></a>Le détecteur d’anomalies

L’image conteneur [Détecteur d’anomalies][ad-containers] se trouve dans le registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/decision` et se nomme `anomaly-detector`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Version la plus récente](#tab/current)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Versions précédentes](#tab/previous)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Read OCR (reconnaissance optique de caractères)

L’image conteneur Read OCR [Vision par ordinateur][cv-containers] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services` et se nomme `read`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/vision/read`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Version la plus récente](#tab/current)

Notes de publication pour `v2.0.013250001-amd64-preview` :

* Réduire davantage l’utilisation de la mémoire pour le conteneur.
* Le cache externe est requis pour la configuration de plusieurs pods. Par exemple, configurez Redis pour la mise en cache.
* Correction de résultats manquants quand le cache Redis est configuré et `ResultExpirationPeriod` a la valeur 0.
* Supprimer la limite de taille du corps de la demande de 26 Mo. Le conteneur peut désormais accepter des fichiers avec une taille supérieure à 26 Mo.
* Ajouter l’horodatage et la version de build à l’écriture dans le journal de la console.

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Versions précédentes](#tab/previous)

Notes de publication pour `1.1.013050001-amd64-preview`

* Ajout de la configuration de l’initialisation de conteneur `ReadEngineConfig:ResultExpirationPeriod` pour spécifier le moment où le système doit nettoyer les résultats de la reconnaissance. 
    * Le paramètre s’affiche en heures et la valeur par défaut est 48 heures.
    * Le paramètre peut réduire l’utilisation de la mémoire pour le stockage des résultats, en particulier quand le stockage en mémoire du conteneur est utilisé.
    * Exemple 1. ReadEngineConfig:ResultExpirationPeriod = 1, le système efface le résultat de la reconnaissance 1 heure après le processus.
    * Si cette configuration a la valeur 0, le système efface le résultat de la reconnaissance une fois le résultat récupéré.

* Correction d’une erreur de serveur interne 500 quand un format d’image non valide est transmis au système. Une erreur 400 est maintenant retournée :

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

L’image conteneur [Form Recognizer][fr-containers] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/custom-form` et se nomme `labeltool`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Version la plus récente](#tab/current)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Versions précédentes](#tab/previous)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

L’image conteneur [LUIS][lu-containers] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/language` et se nomme `luis`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/language/luis`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Version la plus récente](#tab/current)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Version précédente](#tab/previous)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Reconnaissance vocale personnalisée

L’image conteneur [Reconnaissance vocale personnalisée][sp-cstt] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/speechservices/` et se nomme `custom-speech-to-text`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text`. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Version la plus récente](#tab/current)

Note de publication pour `2.6.0-amd64` :

**Caractéristiques**
* Prise en charge de listes d’expressions v2 
* Les listes d’expressions sont prises en charge dans les paramètres régionaux suivants :
    * en-au
    * en-ca
    * en-gb
    * en-in
    * en-nz
    * fr-FR
    * zh-cn
* Prise en charge du téléchargement de modèles de base personnalisés. 
    * Utiliser `BaseModelLocale=<locale>` avec la commande `docker run`
* Migration complète vers .NET 3.1

**Correctifs**
* Résolution d’un problème où le score de confiance était toujours égal à 1 en mode diarisation
* Migration vers l’API Analyse de texte 3.0

Notez qu’en raison des listes d’expressions incluses, la taille de cette image conteneur a augmenté.

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Version précédente](#tab/previous)

Note de publication pour `2.5.0-amd64` :

**Caractéristiques**
* Prise en charge de la prononciation personnalisée sur les modèles personnalisés
* Prise en charge du cloud Azure et du cloud Azure US Government

**Correctifs**
* Résolution du problème d’exécution en tant qu’utilisateur non-racine en mode diarisation

| Étiquettes d’image                    | Notes               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   1ère version GA    |

---

## <a name="custom-text-to-speech"></a>Synthèse vocale personnalisée

L’image conteneur [Synthèse vocale personnalisée][sp-ctts] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/speechservices/` et se nomme `custom-text-to-speech`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech`. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Version la plus récente](#tab/current)

Note de publication pour `1.8.0-amd64` :

**Caractéristiques**
* Migration complète vers .NET 3.1

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Version précédente](#tab/previous)

Note de publication pour `1.7.0-amd64` :

**Fonctionnalité**
* Migration partielle vers .NET 3.1

| Étiquettes d’image                    | Notes               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   1ère version GA    |

---

## <a name="speech-to-text"></a>Reconnaissance vocale

L’image conteneur [Reconnaissance vocale][sp-stt] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/speechservices/` et se nomme `speech-to-text`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text`. Vous trouverez la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Depuis Reconnaissance vocale v2.5.0, les images sont prises en charge dans la région *Virginie du gouvernement des États-Unis*. Utilisez le point de terminaison de facturation et les clés API correspondant à la région *Virginie du gouvernement des États-Unis* quand vous utilisez cette région.

# <a name="latest-version"></a>[Version la plus récente](#tab/current)

Note de publication pour `2.6.0-amd64-<locale>` :

**Caractéristiques**
* Mise à niveau vers les modèles les plus récents et migration complète vers .NET 3.1
* Prise en charge de listes d’expressions v2
* Les listes d’expressions sont prises en charge dans les paramètres régionaux suivants :
    * en-au
    * en-ca
    * en-gb
    * en-in
    * en-nz
    * fr-FR
    * zh-cn
* Prise en charge des paramètres régionaux `cs-CZ` 
    * Les majuscules et les signes de ponctuation ne sont actuellement pas pris en charge.

**Correctifs**
* Résolution d’un problème lié aux scores de confiance toujours égaux à 1 en mode diarisation
* Migration vers l’utilisation de l’API Analyse de texte 3.0

Notez qu’en raison des listes d’expressions incluses, la taille de cette image conteneur a augmenté. 

| Étiquettes d’image                    | Notes                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Image conteneur avec les paramètres régionaux `en-US`.                                                             |
| `2.6.0-amd64-<locale>`        | Remplacez `<locale>` par l’un des paramètres régionaux disponibles, listés ci-dessous. Par exemple, `2.6.0-amd64-en-us`. |

Les paramètres régionaux suivants sont disponibles pour ce conteneur.

| Paramètres régionaux pour v2.6.0           | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `ar-eg`                     | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `ar-kw`                     | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `ar-qa`                     | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `ar-sa`                     | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `ca-es`                     | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `cs-cz`                     | Image conteneur avec les paramètres régionaux `cs-CZ`. |
| `da-dk`                     | Image conteneur avec les paramètres régionaux `da-DK`. |
| `de-de`                     | Image conteneur avec les paramètres régionaux `de-DE`. |
| `en-au`                     | Image conteneur avec les paramètres régionaux `en-AU`. |
| `en-ca`                     | Image conteneur avec les paramètres régionaux `en-CA`. |
| `en-gb`                     | Image conteneur avec les paramètres régionaux `en-GB`. |
| `en-in`                     | Image conteneur avec les paramètres régionaux `en-IN`. |
| `en-nz`                     | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `en-us`                     | Image conteneur avec les paramètres régionaux `en-US`. |
| `es-es`                     | Image conteneur avec les paramètres régionaux `es-ES`. |
| `es-mx`                     | Image conteneur avec les paramètres régionaux `es-MX`. |
| `fi-fi`                     | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `fr-ca`                     | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `fr-fr`                     | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `gu-in`                     | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `hi-in`                     | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `it-it`                     | Image conteneur avec les paramètres régionaux `it-IT`. |
| `ja-jp`                     | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `ko-kr`                     | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `mr-in`                     | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `nb-no`                     | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `nl-nl`                     | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `pl-pl`                     | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `pt-br`                     | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `pt-pt`                     | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `ru-ru`                     | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `sv-se`                     | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `ta-in`                     | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `te-in`                     | Image conteneur avec les paramètres régionaux `te-IN`. |
| `th-th`                     | Image conteneur avec les paramètres régionaux `th-TH`. |
| `tr-tr`                     | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `zh-cn`                     | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `zh-hk`                     | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `zh-tw`                     | Image conteneur avec les paramètres régionaux `zh-TW`. |


# <a name="previous-version"></a>[Version précédente](#tab/previous)

Note de publication pour `2.5.0-amd64-<locale>` :

**Caractéristiques**
* Prise en charge du cloud Azure US Government

**Correctifs**
* Résolution d’un problème lié à l’exécution en tant qu’utilisateur non-racine en mode diarisation

| Étiquettes d’image                  | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Remplacez `<locale>` par l’un des paramètres régionaux disponibles, listés ci-dessous. Par exemple, `2.5.0-amd64-en-us`.  |

Les paramètres régionaux suivants sont disponibles pour ce conteneur.

| Paramètres régionaux pour v2.5.0           | Notes                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Image conteneur avec les paramètres régionaux `ar-AE`. |
| `ar-eg`                     | Image conteneur avec les paramètres régionaux `ar-EG`. |
| `ar-kw`                     | Image conteneur avec les paramètres régionaux `ar-KW`. |
| `ar-qa`                     | Image conteneur avec les paramètres régionaux `ar-QA`. |
| `ar-sa`                     | Image conteneur avec les paramètres régionaux `ar-SA`. |
| `ca-es`                     | Image conteneur avec les paramètres régionaux `ca-ES`. |
| `da-dk`                     | Image conteneur avec les paramètres régionaux `da-DK`. |
| `de-de`                     | Image conteneur avec les paramètres régionaux `de-DE`. |
| `en-au`                     | Image conteneur avec les paramètres régionaux `en-AU`. |
| `en-ca`                     | Image conteneur avec les paramètres régionaux `en-CA`. |
| `en-gb`                     | Image conteneur avec les paramètres régionaux `en-GB`. |
| `en-in`                     | Image conteneur avec les paramètres régionaux `en-IN`. |
| `en-nz`                     | Image conteneur avec les paramètres régionaux `en-NZ`. |
| `en-us`                     | Image conteneur avec les paramètres régionaux `en-US`. |
| `es-es`                     | Image conteneur avec les paramètres régionaux `es-ES`. |
| `es-mx`                     | Image conteneur avec les paramètres régionaux `es-MX`. |
| `fi-fi`                     | Image conteneur avec les paramètres régionaux `fi-FI`. |
| `fr-ca`                     | Image conteneur avec les paramètres régionaux `fr-CA`. |
| `fr-fr`                     | Image conteneur avec les paramètres régionaux `fr-FR`. |
| `gu-in`                     | Image conteneur avec les paramètres régionaux `gu-IN`. |
| `hi-in`                     | Image conteneur avec les paramètres régionaux `hi-IN`. |
| `it-it`                     | Image conteneur avec les paramètres régionaux `it-IT`. |
| `ja-jp`                     | Image conteneur avec les paramètres régionaux `ja-JP`. |
| `ko-kr`                     | Image conteneur avec les paramètres régionaux `ko-KR`. |
| `mr-in`                     | Image conteneur avec les paramètres régionaux `mr-IN`. |
| `nb-no`                     | Image conteneur avec les paramètres régionaux `nb-NO`. |
| `nl-nl`                     | Image conteneur avec les paramètres régionaux `nl-NL`. |
| `pl-pl`                     | Image conteneur avec les paramètres régionaux `pl-PL`. |
| `pt-br`                     | Image conteneur avec les paramètres régionaux `pt-BR`. |
| `pt-pt`                     | Image conteneur avec les paramètres régionaux `pt-PT`. |
| `ru-ru`                     | Image conteneur avec les paramètres régionaux `ru-RU`. |
| `sv-se`                     | Image conteneur avec les paramètres régionaux `sv-SE`. |
| `ta-in`                     | Image conteneur avec les paramètres régionaux `ta-IN`. |
| `te-in`                     | Image conteneur avec les paramètres régionaux `te-IN`. |
| `th-th`                     | Image conteneur avec les paramètres régionaux `th-TH`. |
| `tr-tr`                     | Image conteneur avec les paramètres régionaux `tr-TR`. |
| `zh-cn`                     | Image conteneur avec les paramètres régionaux `zh-CN`. |
| `zh-hk`                     | Image conteneur avec les paramètres régionaux `zh-HK`. |
| `zh-tw`                     | Image conteneur avec les paramètres régionaux `zh-TW`. |

---

## <a name="text-to-speech"></a>Synthèse vocale

L’image conteneur [Synthèse vocale][sp-tts] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/speechservices/` et se nomme `text-to-speech`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Version la plus récente](#tab/current)

Note de publication pour `1.8.0-amd64-<locale-and-voice>` :

**Fonctionnalité**
* Migration complète vers .NET 3.1

| Étiquettes d’image                                  | Notes                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-AriaRUS`.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Remplacez `<locale>` par l’un des paramètres régionaux disponibles, listés ci-dessous. Par exemple, `1.8.0-amd64-en-us-ariarus`.  |


| Paramètres régionaux pour v1.8.0                          | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Image conteneur avec les paramètres régionaux `ar-EG` et la voix `ar-EG-Hoda`.            |
| `ar-sa-naayf`                               | Image conteneur avec les paramètres régionaux `ar-SA` et la voix `ar-SA-Naayf`.           |
| `bg-bg-ivan`                                | Image conteneur avec les paramètres régionaux `bg-BG` et la voix `bg-BG-Ivan`.            |
| `ca-es-herenarus`                           | Image conteneur avec les paramètres régionaux `ca-ES` et la voix `ca-ES-HerenaRUS`.       |
| `cs-cz-jakub`                               | Image conteneur avec les paramètres régionaux `cs-CZ` et la voix `cs-CZ-Jakub`.           |
| `da-dk-hellerus`                            | Image conteneur avec les paramètres régionaux `da-DK` et la voix `da-DK-HelleRUS`.        |
| `de-at-michael`                             | Image conteneur avec les paramètres régionaux `de-AT` et la voix `de-AT-Michael`.         |
| `de-ch-karsten`                             | Image conteneur avec les paramètres régionaux `de-CH` et la voix `de-CH-Karsten`.         |
| `de-de-hedda`                               | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `de-de-heddarus`                            | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `de-de-stefan-apollo`                       | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Stefan-Apollo`.   |
| `el-gr-stefanos`                            | Image conteneur avec les paramètres régionaux `el-GR` et la voix `el-GR-Stefanos`.        |
| `en-au-catherine`                           | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-Catherine`.       |
| `en-au-hayleyrus`                           | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-HayleyRUS`.       |
| `en-ca-heatherrus`                          | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-HeatherRUS`.      |
| `en-ca-linda`                               | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-Linda`.           |
| `en-gb-george-apollo`                       | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-George-Apollo`.   |
| `en-gb-hazelrus`                            | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-HazelRUS`.        |
| `en-gb-susan-apollo`                        | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-Susan-Apollo`.    |
| `en-ie-sean`                                | Image conteneur avec les paramètres régionaux `en-IE` et la voix `en-IE-Sean`.            |
| `en-in-heera-apollo`                        | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Heera-Apollo`.    |
| `en-in-priyarus`                            | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-PriyaRUS`.        |
| `en-in-ravi-apollo`                         | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Ravi-Apollo`.     |
| `en-us-benjaminrus`                         | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-BenjaminRUS`.     |
| `en-us-guy24krus`                           | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Guy24kRUS`.       |
| `en-us-aria24krus`                          | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Aria24kRUS`.      |
| `en-us-ariarus`                             | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-AriaRUS`.         |
| `en-us-zirarus`                             | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-ZiraRUS`.         |
| `es-es-helenarus`                           | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-HelenaRUS`.       |
| `es-es-laura-apollo`                        | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Laura-Apollo`.    |
| `es-es-pablo-apollo`                        | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Pablo-Apollo`.    |
| `es-mx-hildarus`                            | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-HildaRUS`.        |
| `es-mx-raul-apollo`                         | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-Raul-Apollo`.     |
| `fi-fi-heidirus`                            | Image conteneur avec les paramètres régionaux `fi-FI` et la voix `fi-FI-HeidiRUS`.        |
| `fr-ca-caroline`                            | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-Caroline`.        |
| `fr-ca-harmonierus`                         | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-HarmonieRUS`.     |
| `fr-ch-guillaume`                           | Image conteneur avec les paramètres régionaux `fr-CH` et la voix `fr-CH-Guillaume`.       |
| `fr-fr-hortenserus`                         | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-HortenseRUS`.     |
| `fr-fr-julie-apollo`                        | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Julie-Apollo`.    |
| `fr-fr-paul-apollo`                         | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Paul-Apollo`.     |
| `he-il-asaf`                                | Image conteneur avec les paramètres régionaux `he-IL` et la voix `he-IL-Asaf`.            |
| `hi-in-hemant`                              | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Hemant`.          |
| `hi-in-kalpana-apollo`                      | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana-Apollo`.  |
| `hi-in-kalpana`                             | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana`.         |
| `hr-hr-matej`                               | Image conteneur avec les paramètres régionaux `hr-HR` et la voix `hr-HR-Matej`.           |
| `hu-hu-szabolcs`                            | Image conteneur avec les paramètres régionaux `hu-HU` et la voix `hu-HU-Szabolcs`.        |
| `id-id-andika`                              | Image conteneur avec les paramètres régionaux `id-ID` et la voix `id-ID-Andika`.          |
| `it-it-cosimo-apollo`                       | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-Cosimo-Apollo`.   |
| `it-it-luciarus`                            | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-LuciaRUS`.        |
| `ja-jp-ayumi-apollo`                        | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ayumi-Apollo`.    |
| `ja-jp-harukarus`                           | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-HarukaRUS`.       |
| `ja-jp-ichiro-apollo`                       | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ichiro-Apollo`.   |
| `ko-kr-heamirus`                            | Image conteneur avec les paramètres régionaux `ko-KR` et la voix `ko-KR-HeamiRUS`.        |
| `ms-my-rizwan`                              | Image conteneur avec les paramètres régionaux `ms-MY` et la voix `ms-MY-Rizwan`.          |
| `nb-no-huldarus`                            | Image conteneur avec les paramètres régionaux `nb-NO` et la voix `nb-NO-HuldaRUS`.        |
| `nl-nl-hannarus`                            | Image conteneur avec les paramètres régionaux `nl-NL` et la voix `nl-NL-HannaRUS`.        |
| `pl-pl-paulinarus`                          | Image conteneur avec les paramètres régionaux `pl-PL` et la voix `pl-PL-PaulinaRUS`.      |
| `pt-br-daniel-apollo`                       | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-Daniel-Apollo`.   |
| `pt-br-heloisarus`                          | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-HeloisaRUS`.      |
| `pt-pt-heliarus`                            | Image conteneur avec les paramètres régionaux `pt-PT` et la voix `pt-PT-HeliaRUS`.        |
| `ro-ro-andrei`                              | Image conteneur avec les paramètres régionaux `ro-RO` et la voix `ro-RO-Andrei`.          |
| `ru-ru-ekaterinarus`                        | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-EkaterinaRUS`.    |
| `ru-ru-irina-apollo`                        | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-Irina-Apollo`.    |
| `ru-ru-pavel-apollo`                        | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-Pavel-Apollo`.    |
| `sk-sk-filip`                               | Image conteneur avec les paramètres régionaux `sk-SK` et la voix `sk-SK-Filip`.           |
| `sl-si-lado`                                | Image conteneur avec les paramètres régionaux `sl-SI` et la voix `sl-SI-Lado`.            |
| `sv-se-hedvigrus`                           | Image conteneur avec les paramètres régionaux `sv-SE` et la voix `sv-SE-HedvigRUS`.       |
| `ta-in-valluvar`                            | Image conteneur avec les paramètres régionaux `ta-IN` et la voix `ta-IN-Valluvar`.        |
| `te-in-chitra`                              | Image conteneur avec les paramètres régionaux `te-IN` et la voix `te-IN-Chitra`.          |
| `th-th-pattara`                             | Image conteneur avec les paramètres régionaux `th-TH` et la voix `th-TH-Pattara`.         |
| `tr-tr-sedarus`                             | Image conteneur avec les paramètres régionaux `tr-TR` et la voix `tr-TR-SedaRUS`.         |
| `vi-vn-an`                                  | Image conteneur avec les paramètres régionaux `vi-VN` et la voix `vi-VN-An`.              |
| `zh-cn-huihuirus`                           | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-HuihuiRUS`.       |
| `zh-cn-kangkang-apollo`                     | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Kangkang-Apollo`. |
| `zh-cn-yaoyao-apollo`                       | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Yaoyao-Apollo`.   |
| `zh-hk-danny-apollo`                        | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-Danny-Apollo`.    |
| `zh-hk-tracy-apollo`                        | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-Tracy-Apollo`.    |
| `zh-hk-tracyrus`                            | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-TracyRUS`.        |
| `zh-tw-hanhanrus`                           | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-HanHanRUS`.       |
| `zh-tw-yating-apollo`                       | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-Yating-Apollo`.   |
| `zh-tw-zhiwei-apollo`                       | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-Zhiwei-Apollo`.   |


# <a name="previous-version"></a>[Version précédente](#tab/previous)

Note de publication pour `1.7.0-amd64-<locale-and-voice>` :

**Fonctionnalité**
* Composants mis à niveau vers .NET 3.1

| Étiquettes d’image                                  | Notes                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 1ère version GA. Remplacez `<locale>` par l’un des paramètres régionaux disponibles, listés ci-dessous. Par exemple, `1.7.0-amd64-en-us-ariarus`.  |


| Paramètres régionaux pour v1.7.0                          | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Image conteneur avec les paramètres régionaux `ar-EG` et la voix `ar-EG-Hoda`.            |
| `ar-sa-naayf`                               | Image conteneur avec les paramètres régionaux `ar-SA` et la voix `ar-SA-Naayf`.           |
| `bg-bg-ivan`                                | Image conteneur avec les paramètres régionaux `bg-BG` et la voix `bg-BG-Ivan`.            |
| `ca-es-herenarus`                           | Image conteneur avec les paramètres régionaux `ca-ES` et la voix `ca-ES-HerenaRUS`.       |
| `cs-cz-jakub`                               | Image conteneur avec les paramètres régionaux `cs-CZ` et la voix `cs-CZ-Jakub`.           |
| `da-dk-hellerus`                            | Image conteneur avec les paramètres régionaux `da-DK` et la voix `da-DK-HelleRUS`.        |
| `de-at-michael`                             | Image conteneur avec les paramètres régionaux `de-AT` et la voix `de-AT-Michael`.         |
| `de-ch-karsten`                             | Image conteneur avec les paramètres régionaux `de-CH` et la voix `de-CH-Karsten`.         |
| `de-de-hedda`                               | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `de-de-heddarus`                            | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Hedda`.           |
| `de-de-stefan-apollo`                       | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-Stefan-Apollo`.   |
| `el-gr-stefanos`                            | Image conteneur avec les paramètres régionaux `el-GR` et la voix `el-GR-Stefanos`.        |
| `en-au-catherine`                           | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-Catherine`.       |
| `en-au-hayleyrus`                           | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-HayleyRUS`.       |
| `en-ca-heatherrus`                          | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-HeatherRUS`.      |
| `en-ca-linda`                               | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-Linda`.           |
| `en-gb-george-apollo`                       | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-George-Apollo`.   |
| `en-gb-hazelrus`                            | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-HazelRUS`.        |
| `en-gb-susan-apollo`                        | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-Susan-Apollo`.    |
| `en-ie-sean`                                | Image conteneur avec les paramètres régionaux `en-IE` et la voix `en-IE-Sean`.            |
| `en-in-heera-apollo`                        | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Heera-Apollo`.    |
| `en-in-priyarus`                            | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-PriyaRUS`.        |
| `en-in-ravi-apollo`                         | Image conteneur avec les paramètres régionaux `en-IN` et la voix `en-IN-Ravi-Apollo`.     |
| `en-us-benjaminrus`                         | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-BenjaminRUS`.     |
| `en-us-guy24krus`                           | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Guy24kRUS`.       |
| `en-us-aria24krus`                          | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-Aria24kRUS`.      |
| `en-us-ariarus`                             | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-AriaRUS`.         |
| `en-us-zirarus`                             | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-ZiraRUS`.         |
| `es-es-helenarus`                           | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-HelenaRUS`.       |
| `es-es-laura-apollo`                        | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Laura-Apollo`.    |
| `es-es-pablo-apollo`                        | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-Pablo-Apollo`.    |
| `es-mx-hildarus`                            | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-HildaRUS`.        |
| `es-mx-raul-apollo`                         | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-Raul-Apollo`.     |
| `fi-fi-heidirus`                            | Image conteneur avec les paramètres régionaux `fi-FI` et la voix `fi-FI-HeidiRUS`.        |
| `fr-ca-caroline`                            | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-Caroline`.        |
| `fr-ca-harmonierus`                         | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-HarmonieRUS`.     |
| `fr-ch-guillaume`                           | Image conteneur avec les paramètres régionaux `fr-CH` et la voix `fr-CH-Guillaume`.       |
| `fr-fr-hortenserus`                         | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-HortenseRUS`.     |
| `fr-fr-julie-apollo`                        | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Julie-Apollo`.    |
| `fr-fr-paul-apollo`                         | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-Paul-Apollo`.     |
| `he-il-asaf`                                | Image conteneur avec les paramètres régionaux `he-IL` et la voix `he-IL-Asaf`.            |
| `hi-in-hemant`                              | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Hemant`.          |
| `hi-in-kalpana-apollo`                      | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana-Apollo`.  |
| `hi-in-kalpana`                             | Image conteneur avec les paramètres régionaux `hi-IN` et la voix `hi-IN-Kalpana`.         |
| `hr-hr-matej`                               | Image conteneur avec les paramètres régionaux `hr-HR` et la voix `hr-HR-Matej`.           |
| `hu-hu-szabolcs`                            | Image conteneur avec les paramètres régionaux `hu-HU` et la voix `hu-HU-Szabolcs`.        |
| `id-id-andika`                              | Image conteneur avec les paramètres régionaux `id-ID` et la voix `id-ID-Andika`.          |
| `it-it-cosimo-apollo`                       | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-Cosimo-Apollo`.   |
| `it-it-luciarus`                            | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-LuciaRUS`.        |
| `ja-jp-ayumi-apollo`                        | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ayumi-Apollo`.    |
| `ja-jp-harukarus`                           | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-HarukaRUS`.       |
| `ja-jp-ichiro-apollo`                       | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-Ichiro-Apollo`.   |
| `ko-kr-heamirus`                            | Image conteneur avec les paramètres régionaux `ko-KR` et la voix `ko-KR-HeamiRUS`.        |
| `ms-my-rizwan`                              | Image conteneur avec les paramètres régionaux `ms-MY` et la voix `ms-MY-Rizwan`.          |
| `nb-no-huldarus`                            | Image conteneur avec les paramètres régionaux `nb-NO` et la voix `nb-NO-HuldaRUS`.        |
| `nl-nl-hannarus`                            | Image conteneur avec les paramètres régionaux `nl-NL` et la voix `nl-NL-HannaRUS`.        |
| `pl-pl-paulinarus`                          | Image conteneur avec les paramètres régionaux `pl-PL` et la voix `pl-PL-PaulinaRUS`.      |
| `pt-br-daniel-apollo`                       | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-Daniel-Apollo`.   |
| `pt-br-heloisarus`                          | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-HeloisaRUS`.      |
| `pt-pt-heliarus`                            | Image conteneur avec les paramètres régionaux `pt-PT` et la voix `pt-PT-HeliaRUS`.        |
| `ro-ro-andrei`                              | Image conteneur avec les paramètres régionaux `ro-RO` et la voix `ro-RO-Andrei`.          |
| `ru-ru-ekaterinarus`                        | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-EkaterinaRUS`.    |
| `ru-ru-irina-apollo`                        | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-Irina-Apollo`.    |
| `ru-ru-pavel-apollo`                        | Image conteneur avec les paramètres régionaux `ru-RU` et la voix `ru-RU-Pavel-Apollo`.    |
| `sk-sk-filip`                               | Image conteneur avec les paramètres régionaux `sk-SK` et la voix `sk-SK-Filip`.           |
| `sl-si-lado`                                | Image conteneur avec les paramètres régionaux `sl-SI` et la voix `sl-SI-Lado`.            |
| `sv-se-hedvigrus`                           | Image conteneur avec les paramètres régionaux `sv-SE` et la voix `sv-SE-HedvigRUS`.       |
| `ta-in-valluvar`                            | Image conteneur avec les paramètres régionaux `ta-IN` et la voix `ta-IN-Valluvar`.        |
| `te-in-chitra`                              | Image conteneur avec les paramètres régionaux `te-IN` et la voix `te-IN-Chitra`.          |
| `th-th-pattara`                             | Image conteneur avec les paramètres régionaux `th-TH` et la voix `th-TH-Pattara`.         |
| `tr-tr-sedarus`                             | Image conteneur avec les paramètres régionaux `tr-TR` et la voix `tr-TR-SedaRUS`.         |
| `vi-vn-an`                                  | Image conteneur avec les paramètres régionaux `vi-VN` et la voix `vi-VN-An`.              |
| `zh-cn-huihuirus`                           | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-HuihuiRUS`.       |
| `zh-cn-kangkang-apollo`                     | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Kangkang-Apollo`. |
| `zh-cn-yaoyao-apollo`                       | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-Yaoyao-Apollo`.   |
| `zh-hk-danny-apollo`                        | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-Danny-Apollo`.    |
| `zh-hk-tracy-apollo`                        | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-Tracy-Apollo`.    |
| `zh-hk-tracyrus`                            | Image conteneur avec les paramètres régionaux `zh-HK` et la voix `zh-HK-TracyRUS`.        |
| `zh-tw-hanhanrus`                           | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-HanHanRUS`.       |
| `zh-tw-yating-apollo`                       | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-Yating-Apollo`.   |
| `zh-tw-zhiwei-apollo`                       | Image conteneur avec les paramètres régionaux `zh-TW` et la voix `zh-TW-Zhiwei-Apollo`.   |

---

## <a name="neural-text-to-speech"></a>Synthèse vocale neuronale

L’image conteneur [Synthèse vocale neuronale][sp-ntts] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/speechservices/` et se nomme `neural-text-to-speech`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).

| Étiquettes d’image                                  | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-AriaNeural`.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Remplacez `<locale>` par l’un des paramètres régionaux disponibles, listés ci-dessous. Par exemple, `1.2.0-amd64-en-us-arianeural-preview`. |


| Paramètres régionaux et voix de la préversion v1.2.0           | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-AriaNeural`.      |
| `de-de-katjaneural-preview`                 | Image conteneur avec les paramètres régionaux `de-DE` et la voix `de-DE-KatjaNeural`.     |
| `en-au-natashaneural-preview`               | Image conteneur avec les paramètres régionaux `en-AU` et la voix `en-AU-NatashaNeural`.   |
| `en-ca-claraneural-preview`                 | Image conteneur avec les paramètres régionaux `en-CA` et la voix `en-CA-ClaraNeural`.     |
| `en-gb-libbyneural-preview`                 | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-LibbyNeural`.     |
| `en-gb-mianeural-preview`                   | Image conteneur avec les paramètres régionaux `en-GB` et la voix `en-GB-MiaNeural`.       |
| `en-us-arianeural-preview`                  | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-AriaNeural`.      |
| `en-us-guyneural-preview`                   | Image conteneur avec les paramètres régionaux `en-US` et la voix `en-US-GuyNeural`.       |
| `es-es-elviraneural-preview`                | Image conteneur avec les paramètres régionaux `es-ES` et la voix `es-ES-ElviraNeural`.    |
| `es-mx-dalianeural-preview`                 | Image conteneur avec les paramètres régionaux `es-MX` et la voix `es-MX-DaliaNeural`.     |
| `fr-ca-sylvieneural-preview`                | Image conteneur avec les paramètres régionaux `fr-CA` et la voix `fr-CA-SylvieNeural`.    |
| `fr-fr-deniseneural-preview`                | Image conteneur avec les paramètres régionaux `fr-FR` et la voix `fr-FR-DeniseNeural`.    |
| `it-it-elsaneural-preview`                  | Image conteneur avec les paramètres régionaux `it-IT` et la voix `it-IT-ElsaNeural`.      |
| `ja-jp-nanamineural-preview`                | Image conteneur avec les paramètres régionaux `ja-JP` et la voix `ja-JP-NanamiNeural`.    |
| `ko-kr-sunhineural-preview`                 | Image conteneur avec les paramètres régionaux `ko-KR` et la voix `ko-KR-SunHiNeural`.     |
| `pt-br-franciscaneural-preview`             | Image conteneur avec les paramètres régionaux `pt-BR` et la voix `pt-BR-FranciscaNeural`. |
| `zh-cn-xiaoxiaoneural-preview`              | Image conteneur avec les paramètres régionaux `zh-CN` et la voix `zh-CN-XiaoxiaoNeural`.  |

## <a name="speech-language-detection"></a>Détection de langue vocale

L’image conteneur [Détection de langue vocale][sp-lid] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/speechservices/` et se nomme `language-detection`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Étiquettes d’image                                  | Notes                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extraction d’expressions clés

Cette image conteneur se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/textanalytics/` et se nomme `keyphrase`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Version la plus récente](#tab/current)


| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Versions précédentes](#tab/previous)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Détection de la langue du texte

L’image conteneur [Détection de langue][ta-la] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/textanalytics/` et se nomme `language`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`.


Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Dernières versions](#tab/current)

| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Versions précédentes](#tab/previous)


| Étiquettes d’image                    | Notes |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>analyse de sentiments

L’image conteneur [Analyse des sentiments][ta-se] se trouve dans le syndicat du registre de conteneurs `mcr.microsoft.com`. Elle réside dans le référentiel `azure-cognitive-services/textanalytics/` et se nomme `sentiment`. Le nom complet de l’image conteneur est `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`.

Les étiquettes suivantes sont disponibles pour cette image conteneur. Vous trouverez aussi la liste complète des [étiquettes sur MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

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
| `2.1`    | Analyse des sentiments v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
