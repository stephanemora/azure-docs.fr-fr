---
title: Créer une fonction qui s’exécute selon une planification dans Azure
description: Apprenez à créer une fonction dans Azure, qui s’exécute selon une planification que vous définissez.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 808f0f81f937da688a8873e5f6ee959976e9d6aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75769283"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Créez une fonction dans Azure, qui est déclenchée par un minuteur

Apprenez à utiliser Azure Functions pour créer une fonction [serverless](https://azure.microsoft.com/solutions/serverless/) qui s’exécute selon une planification que vous définissez.

![Créer une Function App dans le Portail Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel :

+ Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Créer une fonction déclenchée par un minuteur

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre application de fonction, sélectionnez **Dans le portail**, puis **Continuer**. Sinon, passez à l’étape 3.

   ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Choisissez **Autres modèles**, puis **Terminer et afficher les modèles**.

    ![Page de démarrage rapide Functions permettant de choisir d’autres modèles](./media/functions-create-scheduled-function/add-first-function.png)

3. Dans le champ de recherche, tapez `timer`, puis configurez le nouveau déclencheur avec les paramètres spécifié dans le tableau situé sous l’image.

    ![Créez une fonction déclenchée par un minuteur dans le portail Azure.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom** | Default | Définit le nom de votre fonction déclenchée par minuteur. |
    | **Planification** | 0 \*/1 \* \* \* \* | Un champ de six [expressions CRON](functions-bindings-timer.md#ncrontab-expressions) qui planifie l’exécution de votre fonction chaque minute. |

4. Cliquez sur **Créer**. Une fonction est créée dans le langage que vous avez choisi et s’exécute toutes les minutes, à la minute pile.

5. Vérifiez l’exécution en consultant les informations de traçage écrites dans les journaux d’activité.

    ![Affichage des journaux de fonction dans le portail Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

À présent, vous pouvez modifier la planification de la fonction afin qu’elle s’exécute une fois par heure plutôt que toutes les minutes.

## <a name="update-the-timer-schedule"></a>Mise à jour de la planification du minuteur

1. Développez votre fonction et cliquez sur **Intégrer**. Il s’agit de l’endroit où vous définissez les liaisons d’entrée et de sortie pour votre fonction, et où vous configurez la planification. 

2. Entrez une nouvelle valeur horaire de **Planification** de `0 0 */1 * * *`, puis cliquez sur **Enregistrer**.  

![Les fonctions mettent à jour la planification du minuteur dans le Portail Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Vous disposez maintenant d’une fonction qui s’exécute toutes les heures, à l’heure pile.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute selon une planification. Pour plus d’informations sur les déclencheurs de minuteur, consultez [Planifier l’exécution de code avec Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
