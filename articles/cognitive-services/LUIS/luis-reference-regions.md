---
title: Régions et points de terminaison LUIS (Language Understanding) - Azure - Cognitive Services LUIS | Microsoft Docs
titleSuffix: Azure
description: La région dans laquelle vous publiez votre application LUIS correspond à la région ou à l’emplacement que vous spécifiez dans le portail Azure lorsque vous créez une clé de point de terminaison Azure LUIS. Quand vous publiez une application, LUIS génère automatiquement une URL de point de terminaison pour la région associée à la clé. Pour publier une application LUIS dans plusieurs régions, vous avez besoin d’au moins une clé par région.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 7812ce37cb22c8774c785f5f645b8fef90b02a3e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815501"
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
| Asie | USA Ouest| Asie Est     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | USA Ouest| Asie Sud-Est     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australie](#publishing-to-australia) | Australie Est| Australie Est     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europe](#publishing-to-europe)| Europe Ouest| Europe Nord     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europe](#publishing-to-europe) | Europe Ouest| Europe Ouest     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Amérique du Nord | USA Ouest | USA Est      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | USA Ouest | USA Est 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | USA Ouest | USA Centre Sud     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Amérique du Nord | USA Ouest | USA Centre-Ouest     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | USA Ouest | USA Ouest |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Amérique du Nord | USA Ouest | USA Ouest 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Amérique du Sud | USA Ouest | Brésil Sud     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publication en Europe

Pour publier dans les régions européennes, vous créez des applications LUIS sur https://eu.luis.ai uniquement. Si vous essayez de publier n’importe où ailleurs à l’aide d’une clé dans la région Europe, LUIS affiche un message d’avertissement. Utilisez plutôt https://eu.luis.ai. Les applications LUIS créées sur [https://eu.luis.ai][eu.luis.ai] ne migrent pas automatiquement vers d’autres régions. Exportez et importez l’application LUIS pour la faire migrer.

## <a name="publishing-to-australia"></a>Publication en Australie

Pour publier dans les régions australiennes, vous créez des applications LUIS sur https://au.luis.ai uniquement. Si vous essayez de publier n’importe où ailleurs à l’aide d’une clé dans la région Australie, LUIS affiche un message d’avertissement. Utilisez plutôt https://au.luis.ai. Les applications LUIS créées sur [https://au.luis.ai][au.luis.ai] ne migrent pas automatiquement vers d’autres régions. Exportez et importez l’application LUIS pour la faire migrer.

## <a name="endpoints"></a>Points de terminaison

LUIS dispose actuellement de 2 points de terminaison : un pour la création et l’autre pour l’analyse de texte.

|Objectif|URL|
|--|--|
|Création|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analyse de texte (prédiction de requête)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

Le tableau suivant décrit les paramètres qui apparaissent entre accolades `{}` dans le tableau précédent.

|Paramètre|Objectif|
|--|--|
|region|Région Azure - Les régions de création et de publication sont différentes|
|appID|ID d’application LUIS utilisé dans l’itinéraire d’URL et figurant dans le tableau de bord de l’application|
|q|Texte de l’énoncé envoyé à partir de l’application cliente en tant que bot de conversation|


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Informations de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai