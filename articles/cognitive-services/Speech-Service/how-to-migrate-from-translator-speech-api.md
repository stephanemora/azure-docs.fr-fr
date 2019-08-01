---
title: Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment migrer vos applications de l'API de traduction de conversation Translator Speech vers le service Speech.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 7b61aef13b113d9b2502c24e3001da25fa186c76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559561"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech

Utilisez cet article pour migrer vos applications de l'API de traduction de conversation Microsoft Translator Speech vers le [service Speech](index.yml). Ce guide souligne les différences entre l'API de traduction de conversation Translator Speech et le service Speech. Il propose également des stratégies pour migrer vos applications.

> [!NOTE]
> La clé d’abonnement de votre API de traduction de conversation Translator Speech ne sera pas acceptée par le service Speech. Vous devrez souscrire créer un abonnement aux services Speech.

## <a name="comparison-of-features"></a>Comparaison des fonctionnalités

| Fonctionnalité                                           | API de traduction de conversation Translator Speech                                  | Services Speech | Détails                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traduction en texte                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduction vocale                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Point de terminaison global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Les services Speech n’offrent pas de point de terminaison global. Un point de terminaison global dirige automatiquement le trafic vers le point de terminaison régional le plus proche, en réduisant la latence dans votre application.                                                    |
| Points de terminaison régionaux                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite en termes de durée de connexion                             | 90 minutes                                               | Illimitée avec le kit de développement logiciel (SDK). 10 minutes avec une connexion WebSockets.                                                                                                                                                                                                                                                                                   |
| Clé d’authentification de l’en-tête                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Plusieurs langues traduites en une seule requête | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kits de développement logiciel (SDK) disponibles                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Pour connaître les kits de développement logiciel (SDK) disponibles, consultez la [documentation des services Speech](index.yml).                                                                                                                                                    |
| Connexions WebSockets                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de langues                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Les services Speech prennent en charge les langues décrites dans l'article [référence relative aux langues de l’API de traduction Translator](../translator-speech/languages-reference.md). |
| Filtre et marqueur de propos vulgaires                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM en tant qu’entrée                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Autres types de fichiers en tant qu’entrée                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Résultats partiels                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informations de durée                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de corrélation :                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modèles vocaux personnalisés                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Les services Speech offrent des modèles vocaux personnalisés qui vous permettent de personnaliser la reconnaissance vocale en fonction du vocabulaire propre à votre organisation.                                                                                                                                           |
| Modèles de traduction personnalisée                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | L’abonnement à l’API de traduction de texte Text Translation de Microsoft vous permet d’avoir recours à l’outil de traduction personnalisée [Custom Translator](https://www.microsoft.com/translator/business/customization/) afin d’obtenir des traductions plus précises en utilisant vos propres données.                                                 |

## <a name="migration-strategies"></a>Stratégies de migration

Si vous ou votre organisation disposez d'applications en développement ou en production qui utilisent l’API de traduction de conversation Translator Speech, vous devez les mettre à jour pour utiliser le service Speech. Consultez la documentation du [service Speech](index.yml) pour accéder aux kits de développement logiciel (SDK), aux exemples de code et aux didacticiels disponibles. Prenez en compte les éléments suivants lors de la migration :

* Les services Speech n’offrent pas de point de terminaison global. Vous devez déterminer si votre application fonctionne efficacement lorsqu’elle utilise un point de terminaison régional unique pour tout son trafic. Autrement, utilisez la géolocalisation pour déterminer le point de terminaison plus efficace.

* Si votre application utilise des connexions de longue durée et ne peut pas utiliser les SDK disponibles, vous pouvez utiliser une connexion WebSockets. Gérez la limite de délai d’expiration de 10 minutes en vous reconnectant aux moments appropriés.

* Si votre application utilise l’API de traduction de texte Translator Text et l’API de traduction de conversation Translator Speech pour activer des modèles de traduction personnalisée, vous pouvez ajouter des ID Catégorie directement à l’aide du service Speech.

* Contrairement à l’API de traduction de conversation Translator Speech, les services Speech peuvent traduire plusieurs langues en une seule requête.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement les services Speech](get-started.md)
* [Démarrage rapide : Reconnaissance vocale dans une application UWP à l’aide du kit SDK Speech](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le service Speech Service](overview.md)
* [Documentation du kit de développement logiciel (SDK) et des services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
