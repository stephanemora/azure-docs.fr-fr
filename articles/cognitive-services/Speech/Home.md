---
title: Service Microsoft Bing Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Utilisez l’API Microsoft Speech pour ajouter des actions vocales à vos applications, y compris une interaction en temps réel avec les utilisateurs.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966645"
---
# <a name="what-is-bing-speech"></a>Quelle est la Reconnaissance vocale Bing ?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

L’API Microsoft Bing Speech basée sur le cloud fournit aux développeurs un moyen simple de créer des fonctionnalités vocales puissantes dans leurs applications, telles que la commande vocale, le dialogue utilisateur via la conversation naturelle, ainsi que la transcription et la dictée. L’API Microsoft Speech prend en charge la conversion par *reconnaissance vocale* et par *synthèse vocale*.

- L’API **Reconnaissance vocale** convertit la parole en texte qui peut servir d’entrée ou de commandes pour contrôler votre application.
- L’API **Synthèse vocale** convertit le texte en flux audio qui peuvent être relus à l’utilisateur de votre application.

## <a name="speech-to-text-speech-recognition"></a>Reconnaissance vocale

L’API de reconnaissance vocale Microsoft *transcrit* les flux audio en texte que votre application peut afficher à l’utilisateur ou manipuler en tant qu’entrée de commande. Elle propose aux développeurs deux manières d’ajouter Speech à leurs applications : API REST **ou** bibliothèques clientes Websocket.

- [API REST](GetStarted/GetStartedREST.md) : Les développeurs peuvent utiliser des appels HTTP au service à partir de leurs applications pour la reconnaissance vocale.
- [Bibliothèques clientes](GetStarted/GetStartedClientLibraries.md) : Pour bénéficier de fonctionnalités avancées, les développeurs peuvent télécharger les bibliothèques clientes Microsoft Speech et les lier dans leurs applications.  Les bibliothèques clientes sont disponibles sur différentes plateformes (Windows, Android, iOS) dans différents langages (C#, Java, JavaScript, ObjectiveC). Contrairement aux API REST, les bibliothèques clientes utilisent un protocole basé sur Websocket.

| Cas d'utilisation | [API REST](GetStarted/GetStartedREST.md) | [Bibliothèques clientes](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Convertir un énoncé bref, tel que des commandes (durée < 15 s), sans résultats intermédiaires | Oui | Oui |
| Convertir un long énoncé (> 15 s) | Non | Oui |
| Diffuser en continu un énoncé avec les résultats intermédiaires souhaités | Non | Oui |
| Comprendre le texte converti à partir d’un énoncé à l’aide de LUIS | Non | Oui |

Quelle que soit l’approche choisie par les développeurs (API REST ou bibliothèques clientes), le service Microsoft Speech prend en charge les éléments suivants :

- Technologies avancées de reconnaissance vocale de Microsoft qui sont utilisées par Cortana, la dictée Office, Translator pour Office et d’autres produits Microsoft.
- Reconnaissance continue en temps réel. L’API de reconnaissance vocale permet aux utilisateurs de transcrire du contenu audio en texte en temps réel et permet de placer sur des supports les résultats intermédiaires des mots reconnus jusque là. Le service vocal prend également en charge la détection de la fin de l’énoncé. En outre, les utilisateurs peuvent choisir des fonctionnalités de mise en forme supplémentaires, telles que la mise en majuscules et la ponctuation, le masquage des grossièretés et la normalisation du texte.
- Résultats optimisés de la reconnaissance vocale pour des scénarios *interactifs*, *de conversation* et *de dictée*. Pour les scénarios utilisateur qui nécessitent des modèles linguistiques et acoustiques personnalisés, [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) permet de créer des modèles vocaux adaptés à votre application et vos utilisateurs.
- De nombreuses langues parlées dans plusieurs dialectes. Pour obtenir la liste complète des langues prises en charge dans chaque mode de reconnaissance, consultez les [langues de reconnaissance](api-reference-rest/supportedlanguages.md).
- Intégration à la compréhension de la langue. Outre la conversion du contenu audio d’entrée en texte, l’API *Reconnaissance vocale* fournit aux applications une capacité supplémentaire pour comprendre le sens du texte. Elle utilise le [service LUIS (Language Understanding Intelligent Service)](../LUIS/what-is-luis.md) pour extraire des intentions et des entités à partir du texte reconnu.

### <a name="next-steps"></a>Étapes suivantes

- Commencez à utiliser le service de reconnaissance vocale Microsoft avec les [API REST](GetStarted/GetStartedREST.md) ou les [bibliothèques clientes](GetStarted/GetStartedClientLibraries.md).
- Passez en revue des [exemples d’applications](samples.md) dans le langage de programmation de votre choix.
- Accédez à la section des références pour trouver des détails sur le [protocole Microsoft Speech](API-Reference-REST/websocketprotocol.md) et les références des API.

## <a name="text-to-speech-speech-synthesis"></a>Synthèse vocale

Les API *Synthèse vocale* utilisent REST pour convertir un texte structuré en flux audio. Ces API permettent une synthèse vocale rapide dans différentes langues et avec différentes voix. Les utilisateurs ont également la possibilité de modifier des caractéristiques audio, telles que la prononciation, le volume, la tonalité, etc., à l’aide de balises SSML.

### <a name="next-steps"></a>Étapes suivantes

- Prise en main du service de synthèse vocale Microsoft : [Informations de référence sur l’API Synthèse vocale](api-reference-rest/bingvoiceoutput.md). Pour obtenir la liste complète des langues et des voix prises en charge par la synthèse vocale, consultez [Paramètres régionaux et polices de voix pris en charge](api-reference-rest/bingvoiceoutput.md#SupLocales).
