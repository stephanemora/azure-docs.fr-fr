---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497342"
---
Les fonctions facilitent l’ajout de l’intégration Application Insights à une application de fonction à partir du [portail Azure].

1. Dans le [portail][portail Azure], sélectionnez **Tous les services > Applications de fonction**, sélectionnez votre application de fonction, puis choisissez la bannière **Application Insights** en haut de la fenêtre

    ![Activer Application Insights à partir du portail](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Créez une ressource Application Insights en utilisant les paramètres spécifiés dans le tableau sous l’image :

   ![Création d’une ressource Application Insights dans Azure](media/functions-connect-new-app-insights/ai-general.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom d’application unique | Il est plus facile d’utiliser le même nom que celui de votre application de fonction, qui doit être unique dans votre abonnement. | 
    | **Lieu** | Europe Ouest | Si possible, utilisez la même [région](https://azure.microsoft.com/regions/) que celle de votre application de fonction ou une région à proximité. |

1. Choisissez **OK**. La ressource Application Insights est créée dans le même groupe de ressources et le même abonnement que votre application de fonction. Une fois la création terminée, fermez la fenêtre Application Insights.

1. Dans votre application de fonction, sélectionnez **Paramètres de l’application** et faites défiler jusqu’à **Paramètres de l’application**. Lorsque vous voyez un paramètre nommé `APPINSIGHTS_INSTRUMENTATIONKEY`, cela signifie que l’intégration d’Application Insights est activée pour votre application de fonction s’exécutant dans Azure.

[Portail Azure]: https://portal.azure.com
