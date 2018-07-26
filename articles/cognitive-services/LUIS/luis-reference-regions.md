---
title: Régions LUIS (Language Understanding) | Microsoft Docs
titleSuffix: Azure
description: Cet article contient les listes des régions LUIS pour le site web LUIS, les abonnements Azure et les régions du monde.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: diberry
ms.openlocfilehash: 1f6090bf1ac588585a16f93d2ac091e8950ca45f
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238928"
---
# <a name="regions-and-keys"></a>Régions et clés

La région dans laquelle vous publiez votre application LUIS correspond à la région ou à l’emplacement que vous spécifiez dans le portail Azure lorsque vous créez une clé de point de terminaison Azure LUIS. Lorsque vous [publiez une application](./luis-how-to-publish-app.md), LUIS génère automatiquement une URL de point de terminaison pour la région associée à la clé. Pour publier une application LUIS dans plusieurs régions, vous avez besoin d’au moins une clé par région. 

## <a name="luis-website"></a>Site web LUIS
Il existe trois sites web LUIS, en fonction de la région. Vous devez créer et publier dans la même région. 

|LUIS|Région|
|--|--|
|[www.luis.ai][www.luis.ai]|Données<br>pas l’Europe<br>pas l’Australie|
|[au.luis.ai][au.luis.ai]|Australie|
|[eu.luis.ai][eu.luis.ai]|Europe|


## <a name="publishing-regions"></a>Régions de publication

Les applications LUIS créées sur https://www.luis.ai peuvent être publiées sur tous les points de terminaison à l’exception des régions [européenne](#publishing-to-europe) et [australienne](#publishing-to-australia). 

L’application de la région de création peut uniquement être publiée dans une région de publication correspondante. Si votre application est actuellement dans la mauvaise région de création, exportez l’application et importez-la dans la région de création correcte pour votre région de publication. 

 Région globale | Région de création | Région de publication et d’interrogation   |   Site web LUIS | Format de l’URL de point de terminaison   |
|-----|------|------|------|------|
| Asie | États-Unis de l’Ouest| Est de l'Asie     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | États-Unis de l’Ouest| Asie du Sud-Est     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australie](#publishing-to-australia) | Est de l’Australie| Est de l’Australie     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europe](#publishing-to-europe)| Europe de l'Ouest| Europe du Nord     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europe](#publishing-to-europe) | Europe de l'Ouest| Europe de l'Ouest     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Amérique du Nord | États-Unis de l’Ouest | Est des États-Unis      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | États-Unis de l’Ouest | Est des États-Unis 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | États-Unis de l’Ouest | États-Unis - partie centrale méridionale     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Amérique du Nord | États-Unis de l’Ouest | Centre-Ouest des États-Unis     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | États-Unis de l’Ouest | États-Unis de l’Ouest |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Amérique du Nord | États-Unis de l’Ouest | Ouest des États-Unis 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Amérique du Sud | États-Unis de l’Ouest | Sud du Brésil     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publication en Europe

Pour publier dans les régions européennes, vous créez des applications LUIS sur https://eu.luis.ai uniquement. Si vous essayez de publier n’importe où ailleurs à l’aide d’une clé dans la région Europe, LUIS affiche un message d’avertissement. Utilisez plutôt https://eu.luis.ai. Les applications LUIS créées sur [https://eu.luis.ai][eu.luis.ai] ne migrent pas automatiquement vers d’autres régions. Exportez et importez l’application LUIS pour la faire migrer.

## <a name="publishing-to-australia"></a>Publication en Australie

Pour publier dans les régions australiennes, vous créez des applications LUIS sur https://au.luis.ai uniquement. Si vous essayez de publier n’importe où ailleurs à l’aide d’une clé dans la région Australie, LUIS affiche un message d’avertissement. Utilisez plutôt https://au.luis.ai. Les applications LUIS créées sur [https://au.luis.ai][au.luis.ai] ne migrent pas automatiquement vers d’autres régions. Exportez et importez l’application LUIS pour la faire migrer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Informations de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai