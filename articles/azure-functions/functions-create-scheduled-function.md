---
title: Créer une fonction dans Azure qui s’exécute selon une planification
description: Découvrez comment utiliser le portail Azure pour créer une fonction qui s’exécute selon une planification que vous définissez.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035187"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>Créer une fonction dans le portail Azure qui s’exécute selon une planification

Découvrez comment utiliser le portail Azure pour créer une fonction qui s’exécute [serverless](https://azure.microsoft.com/solutions/serverless/) selon une planification que vous définissez.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

+ Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-function-app"></a>Créer une application de fonction

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Votre nouvelle application de fonction est prête à être utilisée. Créez ensuite une fonction dans la nouvelle application de fonction.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Application de fonction créée avec succès." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Créer une fonction déclenchée par un minuteur

1. Dans votre application de fonction, sélectionnez **Fonctions**, puis **+ Ajouter**. 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Ajoutez une fonction dans le portail Azure." border="true":::

1. Sélectionnez le modèle **Déclencheur de minuteur**. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Sélectionnez le déclencheur de minuteur dans le portail Azure." border="true":::

1. Configurez le nouveau déclencheur avec les paramètres spécifié dans le tableau situé sous l’image, puis sélectionnez **Créer une fonction**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Capture d’écran montrant la page Nouvelle fonction avec le modèle Déclencheur de minuteur sélectionné." border="true":::
    
    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom** | Default | Définit le nom de votre fonction déclenchée par minuteur. |
    | **Planification** | 0 \*/1 \* \* \* \* | Un champ de six [expressions CRON](functions-bindings-timer.md#ncrontab-expressions) qui planifie l’exécution de votre fonction chaque minute. |

## <a name="test-the-function"></a>Tester la fonction

1. Dans votre fonction, sélectionnez **Code + test**, puis développez les journaux.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Testez le déclencheur de minuteur dans le portail Azure." border="true":::

1. Vérifiez l’exécution en consultant les informations écrites dans les journaux.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Affichez le déclencheur de minuteur dans le portail Azure." border="true":::

À présent, vous pouvez modifier la planification de la fonction afin qu’elle s’exécute une fois par heure plutôt que toutes les minutes.

## <a name="update-the-timer-schedule"></a>Mise à jour de la planification du minuteur

1. Dans votre fonction, sélectionnez **Intégration**. Il s’agit de l’endroit où vous définissez les liaisons d’entrée et de sortie pour votre fonction, et où vous configurez la planification. 

1. Sélectionnez **Minuteur (myTimer)** .

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Mettez à jour la planification du minuteur dans le portail Azure." border="true":::

1. Mettez à jour la valeur **Planification** sur `0 0 */1 * * *`, puis sélectionnez **Enregistrer**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Mettez à jour la planification du minuteur de la fonction dans le portail Azure." border="true":::

Vous disposez maintenant d’une fonction qui s’exécute toutes les heures, à l’heure pile.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute selon une planification. Pour plus d’informations sur les déclencheurs de minuteur, consultez [Planifier l’exécution de code avec Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
