---
title: Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment migrer vos applications à partir de l’API Translator Speech pour les Services de reconnaissance vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 1ed494cea1ccf8845a25a3ab49d3194cc6a55509
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "65785675"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech

Utilisez cet article pour migrer vos applications de l'API de traduction de conversation Microsoft Translator Speech vers le [service Speech](index.yml). Ce guide souligne les différences entre l'API de traduction de conversation Translator Speech et le service Speech. Il propose également des stratégies pour migrer vos applications.

> [!NOTE]
> La clé d’abonnement de votre API de traduction de conversation Translator Speech ne sera pas acceptée par le service Speech. Vous devez créer un nouvel abonnement de Services de reconnaissance vocale.

## <a name="comparison-of-features"></a>Comparaison des fonctionnalités

| Fonctionnalité                                           | API de traduction de conversation Translator Speech                                  | Services Speech | Détails                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traduction en texte                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduction vocale                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Point de terminaison global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Les Services de reconnaissance vocale n’offrent pas un point de terminaison global. Un point de terminaison global dirige automatiquement le trafic vers le point de terminaison régional le plus proche, en réduisant la latence dans votre application.                                                    |
| Points de terminaison régionaux                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite en termes de durée de connexion                             | 90 minutes                                               | Illimitée avec le kit de développement logiciel (SDK). 10 minutes avec une connexion WebSockets.                                                                                                                                                                                                                                                                                   |
| Clé d’authentification de l’en-tête                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Plusieurs langues traduites en une seule requête | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kits de développement logiciel (SDK) disponibles                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Consultez le [documentation des Services de reconnaissance vocale](index.yml) des kits SDK disponibles.                                                                                                                                                    |
| Connexions WebSockets                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de langues                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Les Services de reconnaissance vocale prend en charge la même plage de langues décrites dans le [référence des langages de l’API Translator](../translator-speech/languages-reference.md) article. |
| Filtre et marqueur de propos vulgaires                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM en tant qu’entrée                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Autres types de fichiers en tant qu’entrée                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Résultats partiels                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informations de durée                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de corrélation :                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modèles vocaux personnalisés                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Les Services de reconnaissance vocale offrent des modèles vocaux personnalisés qui vous permettent de personnaliser la reconnaissance vocale vocabulaire uniques de votre organisation.                                                                                                                                           |
| Modèles de traduction personnalisée                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | L’abonnement à l’API de traduction de texte Text Translation de Microsoft vous permet d’avoir recours à l’outil de traduction personnalisée [Custom Translator](https://www.microsoft.com/translator/business/customization/) afin d’obtenir des traductions plus précises en utilisant vos propres données.                                                 |

## <a name="migration-strategies"></a>Stratégies de migration

Si vous ou votre organisation disposez d'applications en développement ou en production qui utilisent l’API de traduction de conversation Translator Speech, vous devez les mettre à jour pour utiliser le service Speech. Consultez la documentation du [service Speech](index.yml) pour accéder aux kits de développement logiciel (SDK), aux exemples de code et aux didacticiels disponibles. Prenez en compte les éléments suivants lors de la migration :

* Les Services de reconnaissance vocale n’offrent pas un point de terminaison global. Vous devez déterminer si votre application fonctionne efficacement lorsqu’elle utilise un point de terminaison régional unique pour tout son trafic. Autrement, utilisez la géolocalisation pour déterminer le point de terminaison plus efficace.

* Si votre application utilise des connexions de longue durée et ne peut pas utiliser les SDK disponibles, vous pouvez utiliser une connexion WebSockets. Gérez la limite de délai d’expiration de 10 minutes en vous reconnectant aux moments appropriés.

* Si votre application utilise l’API de traduction de texte Translator Text et l’API de traduction de conversation Translator Speech pour activer des modèles de traduction personnalisée, vous pouvez ajouter des ID Catégorie directement à l’aide du service Speech.

* Contrairement à l’API Translator Speech, les Services de reconnaissance vocale peuvent effectuer des traductions en plusieurs langues dans une demande unique.

## <a name="next-steps"></a>Étapes suivantes

* [Essayez gratuitement les Services de reconnaissance vocale](get-started.md)
* [Démarrage rapide : Reconnaissance vocale dans une application UWP à l’aide du kit SDK Speech](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le service Speech Service](overview.md)
* [Documentation des Services de reconnaissance vocale et de Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
