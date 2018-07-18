---
title: À propos de la reconnaissance vocale | Microsoft Docs
description: Vue d’ensemble des fonctionnalités de l’API Reconnaissance vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370713"
---
# <a name="about-the-speech-to-text-api"></a>À propos de l’API Reconnaissance vocale

L’API **Reconnaissance vocale** *transcrit* des flux audio en texte que votre application peut afficher à l’utilisateur ou traiter en tant qu’entrée de commande. Les API peuvent être utilisées avec une bibliothèque de client de Kit de développement logiciel (pour les langues et plateformes prises en charge) ou une API REST.

L’API **Reconnaissance vocale** offre les fonctionnalités suivantes :

- Technologie de reconnaissance vocale de Microsoft, la même que celle utilisée par Cortana et d’autres produits Microsoft Office.

- Reconnaissance continue en temps réel. La **Reconnaissance vocale** permet aux utilisateurs de transcrire de l’audio en texte en temps réel. Elle prend également en charge la réception des résultats intermédiaires des mots déjà reconnus. Le service reconnaît automatiquement à la fin de la parole. Les utilisateurs peuvent également choisir des options de mise en forme supplémentaires, dont la casse et la ponctuation, le masquage des vulgarités et la normalisation du texte.

- Résultats de **reconnaissance vocale** optimisés pour des scénarios interactifs de conversation et de dictée. 

- Prise en charge de nombreuses langues parlées dans plusieurs dialectes. Pour obtenir la liste complète des langues prises en charge dans chaque mode de reconnaissance, voir [Langues prises en charge](supported-languages.md#speech-to-text).

- Modèles linguistiques et acoustiques personnalisés qui vous permettent d’adapter votre application à la façon de parler de vos utilisateurs, à l’environnement acoustique et à un vocabulaire spécialisé.

- Compréhension du langage naturel. Grâce à l’intégration avec [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), vous pouvez dériver de la parole des entités et des intentions. Les utilisateurs ne doivent pas nécessairement connaître le vocabulaire de votre application, et peuvent décrire ce qu’ils veulent avec leurs propres mots.

## <a name="api-capabilities"></a>Fonctionnalités de l’API

Certaines fonctionnalités de l’API **Reconnaissance vocale** ne sont pas disponibles via REST. Le tableau suivant récapitule les fonctionnalités de chaque méthode d’accès à l’API.

| Cas d’utilisation | REST | Kits de développement logiciel (SDK) |
|-----|-----|-----|----|
| Transcrire un énoncé court, tel qu’une commande (longueur < 15 s), sans résultat intermédiaire | OUI | OUI |
| Transcrire un énoncé plus long (> 15 s) | Non  | OUI |
| Transcrire une diffusion audio en continu avec des résultats intermédiaires facultatifs | Non  | OUI |
| Comprendre les intentions de l’orateur via LUIS | Non\* | Oui |

\* *Les intentions et entités de LUIS peuvent être dérivées à l’aide d’un abonnement LUIS distinct. Avec cet abonnement, le Kit de développement logiciel (SDK) peut appeler LUIS pour vous, et fournir des résultats d’entité et d’intention, ainsi que des transcriptions de la parole. L’API REST vous permet d’appeler LUIS vous-même pour dériver des intentions et des entités avec votre abonnement LUIS.*

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-windows.md)
