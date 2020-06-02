---
title: Fichier include
description: Fichier include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121535"
---
La solution Azure Functions facilite l’ajout de l’intégration d’Application Insights à une application de fonction à partir du [portail Azure].

1. Dans le [portail Azure][portail Azure], recherchez et sélectionnez **Application de fonction**, puis choisissez votre application de fonction. 

1. Sélectionnez la bannière **Application Insights n'est pas configuré** en haut de la fenêtre. Si vous ne voyez pas cette bannière, cela signifie que votre application a déjà Application Insights activé.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Activer Application Insights à partir du portail":::

1. Créez une ressource Application Insights en utilisant les paramètres spécifiés dans le tableau sous l’image.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Création d’une ressource Application Insights":::

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom d’application unique | Il est plus facile d’utiliser le même nom que celui de votre application de fonction, qui doit être unique dans votre abonnement. | 
    | **Lieu** | Europe Ouest | Si possible, utilisez la même [région](https://azure.microsoft.com/regions/) que celle de votre application de fonction ou une région proche. |

1. Sélectionnez **Appliquer**. La ressource Application Insights est créée dans le même groupe de ressources et le même abonnement que votre application de fonction. Une fois la ressource créée, fermez la fenêtre Application Insights.

1. Revenez dans votre application de fonction, sélectionnez **Paramètres** > **Configuration**, puis sélectionnez **Paramètres d'application**. Si vous voyez un paramètre nommé `APPINSIGHTS_INSTRUMENTATIONKEY`, cela signifie que l’intégration d’Application Insights est activée pour votre application de fonction s’exécutant dans Azure.

[Portail Azure]: https://portal.azure.com
