---
title: Fichier include
description: Fichier include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730996"
---
Azure Functions facilite l’ajout de l’intégration d’Application Insights à une application de fonction à partir du [portail Azure].

1. Dans le [portail Azure][portail Azure], recherchez et sélectionnez **Application de fonction**, puis choisissez votre application de fonction. 

1. Sélectionnez la bannière **Application Insights n'est pas configuré** en haut de la fenêtre. Si vous ne voyez pas cette bannière, cela signifie que votre application a probablement déjà Application Insights activé.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Activer Application Insights à partir du portail":::

1. Développez **Changer votre ressource** et créez une ressource Application Insights en utilisant les paramètres spécifiés dans le tableau suivant.  

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nouveau nom de la ressource** | Nom d’application unique | Il est plus facile d’utiliser le même nom que celui de votre application de fonction, qui doit être unique dans votre abonnement. | 
    | **Lieu** | Europe Ouest | Si possible, utilisez la même [région](https://azure.microsoft.com/regions/) que celle de votre application de fonction ou une région proche. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Création d’une ressource Application Insights":::

1. Sélectionnez **Appliquer**. 

   La ressource Application Insights est créée dans le même groupe de ressources et le même abonnement que votre application de fonction. Une fois la ressource créée, fermez la fenêtre Application Insights.

1. Dans votre application de fonction, sélectionnez **Configuration** sous **Paramètres**, puis sélectionnez **Paramètres d'application**. Si vous voyez un paramètre nommé `APPINSIGHTS_INSTRUMENTATIONKEY`, cela signifie que l’intégration d’Application Insights est activée pour votre application de fonction s’exécutant dans Azure.

[Portail Azure]: https://portal.azure.com
