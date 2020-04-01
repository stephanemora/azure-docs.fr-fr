---
title: Fichier include
description: Fichier include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132554"
---
La solution Azure Functions facilite l’ajout de l’intégration d’Application Insights à une application de fonction à partir du [portail Azure].

1. Dans le [portail][portail Azure], tapez `Function Apps` dans la barre de recherche en haut de la page, choisissez votre application de fonction, puis sélectionnez la bannière **Application Insights n’est pas configuré** en haut de la fenêtre. Si vous ne voyez pas cette bannière, cela signifie que votre application a déjà Application Insights activé.

    ![Activer Application Insights à partir du portail](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Créez une ressource Application Insights en utilisant les paramètres spécifiés dans le tableau sous l’image.

   ![Création d’une ressource Application Insights dans Azure](media/functions-connect-new-app-insights/ai-general.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom d’application unique | Il est plus facile d’utiliser le même nom que celui de votre application de fonction, qui doit être unique dans votre abonnement. | 
    | **Lieu** | Europe Ouest | Si possible, utilisez la même [région](https://azure.microsoft.com/regions/) que celle de votre application de fonction ou une région proche. |

1. Sélectionnez **OK**. La ressource Application Insights est créée dans le même groupe de ressources et le même abonnement que votre application de fonction. Une fois la ressource créée, fermez la fenêtre Application Insights.

1. Dans votre application de fonction, sélectionnez **Paramètres de l’application**, puis faites défiler jusqu’à **Paramètres de l’application**. Si vous voyez un paramètre nommé `APPINSIGHTS_INSTRUMENTATIONKEY`, cela signifie que l’intégration d’Application Insights est activée pour votre application de fonction s’exécutant dans Azure.

[Portail Azure]: https://portal.azure.com
