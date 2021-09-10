---
title: Kits de développement logiciel (SDK) et ressources Azure Video Analyzer
description: En savoir plus sur les kits de développement logiciel (SDK) d’Azure Video Analyzer
author: bennage
ms.author: christb
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: d350c757ec977f743cdcc921770a670863dc9577
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122528028"
---
# <a name="azure-video-analyzer-sdks"></a>Kits de développement logiciel (SDK) d’Azure Video Analyzer

Azure Video Analyzer comprend deux groupes de kits de développement logiciel (SDK). Les SDK de gestion sont utilisés pour gérer la ressource Azure, et les SDK clients pour interagir avec les modules périphériques.

## <a name="management-sdks"></a>Kits SDK de gestion

Les SDK de gestion vous permettent d’interagir avec les ressources exposées par Azure Resource Manager. Vous pouvez créer un compte Video Analyzer, générer des jetons de provisionnement pour les modules périphériques, gérer les stratégies d’accès pour les vidéos et bien plus encore. Les SDK s’appuient sur une [API REST](/rest/api/videoanalyzer/?branch=video) sous-jacente.

Les plateformes suivantes sont prises en charge :

- [.NET](https://aka.ms/ava/sdk/mgt/net)
- [Java](https://aka.ms/ava/sdk/mgt/java)
- [Python](https://aka.ms/ava/sdk/mgt/python)

## <a name="client-sdks"></a>Kits de développement logiciel (SDK) client

Les SDK clients vous permettent d’interagir avec les [méthodes directes][docs-direct-methods] d’un module périphérique de Video Analyzer. Ces SDK sont conçus pour être utilisés avec les [SDK Azure IoT Hub][docs-iot-hub-sdks]. Ils prennent en charge la construction d’objets qui représentent les méthodes directes qui peuvent ensuite être envoyées au module périphérique à l’aide des SDK IoT Hub.

Les plateformes suivantes sont prises en charge :

- [.NET](https://aka.ms/ava/sdk/client/net)
- [Python](https://aka.ms/ava/sdk/client/python)
- [Java](https://aka.ms/ava/sdk/client/java)

## <a name="see-also"></a>Voir aussi

- Vous pouvez également consulter les [API d’Azure Video Analyzer](https://docs.microsoft.com/rest/api/videoanalyzer/).

<!-- links -->
[docs-direct-methods]: direct-methods.md
[docs-iot-hub-sdks]: ../../iot-hub/iot-hub-devguide-sdks.md

[REST API]: https://aka.ms/ava/api/rest
