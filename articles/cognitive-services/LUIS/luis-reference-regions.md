---
title: Points de terminaison et régions de publication – LUIS
description: La région spécifiée sur le portail Azure est la même que celle dans laquelle vous allez publier l'application LUIS, et une URL de point de terminaison est générée pour cette même région.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 160a12139054f5ee887a540a4d838d124dd34840
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110617327"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Création et la publication de régions et des clés associées

Les régions de création LUIS sont prises en charge par le portail LUIS. Pour publier une application LUIS dans plusieurs régions, vous avez besoin d’au moins une clé par région.


<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Régions de création LUIS

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

Les régions de création suivantes sont disponibles pour LUIS :
    
* Australie Est
* Europe Ouest
* USA Ouest
* Suisse Nord


LUIS a un portail que vous pouvez utiliser quelle que soit la région, [www.luis.ai](https://www.luis.ai). Vous devez toujours créer et publier dans la même région. Les régions de création ont [des régions de basculement jumelées](../../best-practices-availability-paired-regions.md).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Régions de publication et ressources Azure

L’application est publiée dans toutes les régions associées aux ressources LUIS ajoutés dans le portail de LUIS. Par exemple, pour une application créée sur [www.luis.ai][www.luis.ai], si vous créez une ressource LUIS ou une ressource Cognitive Service dans **westus** et que vous [l’ajoutez à l’application en tant que ressource](luis-how-to-azure-subscription.md), l’application est publiée dans cette région.

## <a name="public-apps"></a>Applications publiques
Une application publique est publiée dans toutes les régions. Ainsi, un utilisateur ayant une clé de ressource LUIS basée sur une région peut accéder à l’application dans toute région associée à sa clé de ressource.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Les régions de publication sont liées aux régions de création.

L’application de la région de création peut uniquement être publiée dans une région de publication correspondante. Si votre application est actuellement dans la mauvaise région de création, exportez l’application et importez-la dans la région de création correcte pour votre région de publication.

> [!NOTE]
> Les applications LUIS créées sur https://www.luis.ai peuvent désormais être publiées sur tous les points de terminaison, y compris les régions [Europe](#publishing-to-europe) et [Australie](#publishing-to-australia).

## <a name="publishing-to-europe"></a>Publication en Europe

 Région globale | Région de l’API de création | Région de publication et d’interrogation<br>`API region name`   |  Format de l’URL de point de terminaison   |
|-----|------|------|------|
| Europe | `westeurope`| France Centre<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europe | `westeurope`| Europe Nord<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europe | `westeurope`| Europe Ouest<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europe | `westeurope`| Sud du Royaume-Uni<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europe | `westeurope`| Suisse Nord<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publication en Australie

 Région globale | Région de l’API de création | Région de publication et d’interrogation<br>`API region name`   |  Format de l’URL de point de terminaison   |
|-----|------|------|------|
| Australie | `australiaeast` | Australie Est<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Autres régions de publication

 Région globale | Région de l’API de création | Région de publication et d’interrogation<br>`API region name`   |  Format de l’URL de point de terminaison   |
|-----|------|------|------|
| Afrique | `westus`<br>[www.luis.ai][www.luis.ai]| Afrique du Sud Nord<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Inde centrale<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asie Est<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Japon Est<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| OuJapon Est<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Centre de la Corée<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asie Sud-Est<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Émirats arabes unis Nord<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Centre du Canada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | USA Centre<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | USA Est<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | USA Est 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Centre-Nord des États-Unis<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | États-Unis - partie centrale méridionale<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Centre-USA Ouest<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | USA Ouest<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | USA Ouest 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Amérique du Sud | `westus`<br>[www.luis.ai][www.luis.ai] | Brésil Sud<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Points de terminaison

Découvrez-en plus sur les [points de terminaison de création et de prédiction](developer-reference-resource.md).

## <a name="failover-regions"></a>Régions de basculement

Chaque région a une région secondaire vers laquelle basculer. L’Europe bascule à l’intérieur de l’Europe et l’Australie à l’intérieur de l’Australie.

Les régions de création ont [des régions de basculement jumelées](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Informations de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai