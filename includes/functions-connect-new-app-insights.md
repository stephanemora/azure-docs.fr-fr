---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669621"
---
La solution Azure Functions facilite l’ajout de l’intégration d’Application Insights à une application de fonction à partir du [portail Azure].

1. Dans le [portail][portail Azure], sélectionnez **Tous les services > Applications de fonction**, sélectionnez votre application de fonction, puis choisissez la bannière **Application Insights** en haut de la fenêtre.

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
