---
title: À propos de la reconnaissance vocale - Services Speech
titleSuffix: Azure Cognitive Services
description: L’API Reconnaissance vocale transcrit Les flux audio en texte que votre application peut afficher ou traiter comme entrée. Le service est disponible via le kit SDK et un point de terminaison RESTful.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 4884756c50e181e5536ad93ae795543bf1edf8ba
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220219"
---
# <a name="about-the-speech-to-text-api"></a>À propos de l’API Reconnaissance vocale

L’API **Reconnaissance vocale** *transcrit* des flux audio en texte que votre application peut afficher à l’utilisateur ou traiter en tant qu’entrée de commande. Les API peuvent être utilisées avec une bibliothèque de client de Kit de développement logiciel (pour les langues et plateformes prises en charge) ou une API REST.

L’API **Reconnaissance vocale** offre les fonctionnalités suivantes :

- Technologie de reconnaissance vocale de Microsoft, la même que celle utilisée par Cortana et d’autres produits Microsoft Office.

- Reconnaissance continue en temps réel. La **Reconnaissance vocale** permet aux utilisateurs de transcrire de l’audio en texte en temps réel. Elle prend également en charge la réception des résultats intermédiaires des mots déjà reconnus. Le service reconnaît automatiquement à la fin de la parole. Les utilisateurs peuvent également choisir des options de mise en forme supplémentaires, dont la casse et la ponctuation, le masquage des vulgarités et la normalisation du texte inverse.

- Résultats retournés à la fois sous forme lexicale et sous forme écrite (pour les résultats sous forme lexicale, consultez DetailedSpeechRecognitionResult dans les exemples ou l’API).

- Prise en charge de nombreuses langues parlées et dialectes. Pour obtenir la liste complète des langues prises en charge dans chaque mode de reconnaissance, voir [Langues prises en charge](language-support.md#speech-to-text).

- Modèles linguistiques et acoustiques personnalisés qui vous permettent d’adapter votre application au vocabulaire spécialisé, au contexte linguistique et à la façon de parler de vos utilisateurs.

- Compréhension du langage naturel. Grâce à l’intégration avec [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), vous pouvez dériver de la parole des entités et des intentions. Les utilisateurs ne doivent pas nécessairement connaître le vocabulaire de votre application, et peuvent décrire ce qu’ils veulent avec leurs propres mots.

- Le score de confiance est retourné par le service si vous spécifiez une sortie détaillée sur l’objet de configuration de reconnaissance vocale (propriété SpeechConfig.OutputFormat). Vous pouvez ensuite utiliser soit la méthode Best() sur le résultat, soit obtenir le score directement à partir du code JSON retourné par le service (semblable à Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>Fonctionnalités de l’API

Certaines des fonctionnalités de l’API **Reconnaissance vocale**, notamment concernant la personnalisation, sont disponibles par le biais de REST. Le tableau suivant récapitule les fonctionnalités de chaque méthode d’accès à l’API. Pour obtenir la liste complète des fonctionnalités et des détails de l’API, consultez [Informations de référence sur Swagger](https://westus.cris.ai/swagger/ui/index).

| Cas d’utilisation | REST | Kits de développement logiciel (SDK) |
|-----|-----|-----|----|
| Transcrire un énoncé court, tel qu’une commande (longueur < 15 s), sans résultat intermédiaire | Oui | Oui |
| Transcrire un énoncé plus long (> 15 s) | Non  | Oui |
| Transcrire une diffusion audio en continu avec des résultats intermédiaires facultatifs | Non  | Oui |
| Comprendre les intentions de l’orateur via LUIS | Non\* | Oui |
| Créer des tests de précision | Oui | Non  |
| Charger des jeux de données pour l’adaptation de modèle | Oui | Non  |
| Créer et gérer des modèles vocaux | Oui | Non  |
| Créer et gérer des déploiements de modèle | Oui | Non  |
| Gérer les abonnements | Oui | Non  |
| Créer et gérer des déploiements de modèle | Oui | Non  |
| Créer et gérer des déploiements de modèle | Oui | Non  |

> [!NOTE]
> L’API REST implémente une limitation des requêtes d’API à 25 toutes les cinqs secondes. Les en-têtes de messages vous informeront des limites.

\* *Les intentions et entités de LUIS peuvent être dérivées à l’aide d’un abonnement LUIS distinct. Avec cet abonnement, le Kit de développement logiciel (SDK) peut appeler LUIS pour vous, et fournir des résultats d’entité et d’intention, ainsi que des transcriptions de la parole. L’API REST vous permet d’appeler LUIS vous-même pour dériver des intentions et des entités avec votre abonnement LUIS.*

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Démarrage rapide : reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
* [Découvrez comment reconnaître les intentions vocales dans C#](how-to-recognize-intents-from-speech-csharp.md)
