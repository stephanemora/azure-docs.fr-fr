---
title: Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech
titleSuffix: Azure Cognitive Services
description: Utilisez cette rubrique pour migrer vos applications de l'API de traduction de conversation Translator Speech vers le service Speech.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: b50cdc6978519c0ec9da447c324237c00577d9fd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885270"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech

Utilisez cet article pour migrer vos applications de l'API de traduction de conversation Microsoft Translator Speech vers le [service Speech](index.yml). Ce guide souligne les différences entre l'API de traduction de conversation Translator Speech et le service Speech. Il propose également des stratégies pour migrer vos applications.

> [!NOTE]
> La clé d’abonnement de votre API de traduction de conversation Translator Speech ne sera pas acceptée par le service Speech. Vous devrez souscrire un nouvel abonnement au service Speech.

## <a name="comparison-of-features"></a>Comparaison des fonctionnalités

| Fonctionnalité                                           | API de traduction de conversation Translator Speech                                  | Speech Service | Détails                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traduction en texte                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduction vocale                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Point de terminaison global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Le service Speech n’offre actuellement aucun point de terminaison global. Un point de terminaison global dirige automatiquement le trafic vers le point de terminaison régional le plus proche, en réduisant la latence dans votre application.                                                    |
| Points de terminaison régionaux                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite en termes de durée de connexion                             | 90 minutes                                               | Illimitée avec le kit de développement logiciel (SDK). 10 minutes avec une connexion WebSocket                                                                                                                                                                                                                                                                                   |
| Clé d’authentification de l’en-tête                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Plusieurs langues traduites en une seule requête | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kits de développement logiciel (SDK) disponibles                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Pour connaître les kits de développement logiciel (SDK) disponibles, consultez la [documentation du service Speech](index.yml).                                                                                                                                                    |
| Connexions WebSocket                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de langues                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Le service Speech prend en charge les langues décrites dans l'article [référence relative aux langues de l’API de traduction Translator](../translator-speech/languages-reference.md). |
| Filtre et marqueur de propos vulgaires                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM en tant qu’entrée                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Autres types de fichiers en tant qu’entrée                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Résultats partiels                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informations de durée                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de corrélation :                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modèles vocaux personnalisés                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Le service Speech offre des modèles vocaux personnalisés qui vous permettent de personnaliser la reconnaissance vocale en fonction du vocabulaire propre à votre organisation.                                                                                                                                           |
| Modèles de traduction personnalisée                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | L’abonnement à l’API de traduction de texte Text Translation de Microsoft vous permet d’avoir recours à l'outil de traduction personnalisée [Custom Translator](https://www.microsoft.com/translator/business/customization/) (actuellement en préversion) afin d'utiliser vos propres données pour obtenir des traductions plus précises.                                                 |

## <a name="migration-strategies"></a>Stratégies de migration

Si vous ou votre organisation disposez d'applications en développement ou en production qui utilisent l’API de traduction de conversation Translator Speech, vous devez les mettre à jour pour utiliser le service Speech. Consultez la documentation du [service Speech](index.yml) pour accéder aux kits de développement logiciel (SDK), aux exemples de code et aux didacticiels disponibles. Points importants à prendre en compte lors de la migration :

* Le service Speech n’offre actuellement aucun point de terminaison global. Vous devrez déterminer si votre application fonctionnera efficacement avec un point de terminaison régional unique pour l'ensemble de son trafic. Si ce n’est pas le cas, vous devrez utiliser la géolocalisation pour identifier le point de terminaison le plus efficace.

* Si votre application utilise des connexions de longue durée et qu'elle ne peut pas avoir recours aux kits de développement logiciel (SDK) disponibles, vous pouvez utiliser une connexion Websocket et gérer la limite d'expiration de 10 minutes en vous reconnectant aux moments opportuns.

* Si votre application utilise l’API de traduction de texte Translator Text et l’API de traduction de conversation Translator Speech pour activer des modèles de traduction personnalisée, vous pourrez ajouter des ID « Catégorie » directement à l’aide du service Speech.

* Contrairement à l’API de traduction de conversation Translator Speech, le service Speech peut traduire plusieurs langues en une seule requête.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement le service Speech](get-started.md)
* [Démarrage rapide : reconnaissance vocale dans une application UWP à l’aide du kit de développement logiciel (SDK) Speech](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que le service Speech ?](overview.md)
* [Documentation sur le service Speech et le kit de développement logiciel (SDK)](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
