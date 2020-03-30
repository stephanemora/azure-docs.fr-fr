---
title: Limitations des conteneurs - LUIS
titleSuffix: Azure Cognitive Services
description: Langues des conteneurs LUIS prises en charge.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73506812"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitations des conteneurs Language Understanding (LUIS)

Les conteneurs LUIS présentent quelques limitations notables. Cet article décrit les limitations telles que les dépendances non prises en charge et le sous-ensemble de langues pris en charge.

## <a name="supported-dependencies-for-latest-container"></a>Dépendances prises en charge pour le conteneur `latest`

Le dernier conteneur LUIS, publié sur [//build/ 2019](https://news.microsoft.com/build2019/), prend en charge :

* [Nouveaux domaines prédéfinis](luis-reference-prebuilt-domains.md) : ces domaines destinés aux entreprises incluent des entités, des exemples d’énoncé et des modèles. Étendez ces domaines pour votre usage personnel.

## <a name="unsupported-dependencies-for-latest-container"></a>Dépendances non prises en charge pour le conteneur `latest`

Pour [exporter à partir d’un conteneur](luis-container-howto.md#export-packaged-app-from-luis), vous devez supprimer les dépendances non prises en charge de votre application LUIS. Lorsque vous tentez d’exporter à partir du conteneur, le portail LUIS vous signale les fonctionnalités non prises en charge que vous devez supprimer.

Vous pouvez utiliser une application LUIS si elle **n’inclut aucune** des dépendances suivantes :

Configurations d’application non prises en charge|Détails|
|--|--|
|Cultures de conteneur non prises en charge| Néerlandais (`nl-NL`)<br>Japonais (`ja-JP`)<br>L’allemand est uniquement pris en charge avec le [générateur de jetons 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entités non prises en charge pour toutes les cultures|Entité prédéfinie [KeyPhrase](luis-reference-prebuilt-keyphrase.md) pour toutes les cultures|
|Entités non prises en charge pour la culture anglaise (`en-US`)|Entités prédéfinies [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Préparation vocale|Les dépendances externes ne sont pas prises en charge dans le conteneur.|
|analyse de sentiments|Les dépendances externes ne sont pas prises en charge dans le conteneur.|
|Vérification orthographique Bing|Les dépendances externes ne sont pas prises en charge dans le conteneur.|

## <a name="languages-supported"></a>Langues prises en charge

Les conteneurs LUIS prennent en charge un sous-ensemble des [langues prises en charge](luis-language-support.md#languages-supported) par LUIS. Les conteneurs LUIS sont capables de comprendre les énoncés dans les langues suivantes :

| Langage | Paramètres régionaux | Domaine prédéfini | Entité prédéfinie | Recommandations de liste d’expression | \**[Analyse de texte](../text-analytics/language-support.md)<br>(Sentiment et<br>mots clés)|
|--|--|:--:|:--:|:--:|:--:|
| Anglais (États-Unis) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chinois](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Français (France) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Français (Canada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Allemand |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italien |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coréen |`ko-KR` | ✔️ | ❌ | ❌ | *Expression clé* uniquement |
| Portugais (Brésil) |`pt-BR` | ✔️ | ✔️ | ✔️ | pas toutes les sous-cultures |
| Espagnol (Espagne) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Espagnol (Mexique)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turc | `tr-TR` |✔️| ❌ | ❌ | *Sentiment* uniquement |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]