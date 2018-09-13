---
title: Créer sa première application Language Understanding Intelligent Service (LUIS) en 10 minutes - Cognitive Services LUIS | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appliances. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "43769943"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Guide de démarrage rapide : Utiliser une application domotique prédéfinie

Dans ce guide de démarrage rapide, vous allez créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appliances. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

## <a name="prerequisites"></a>Prérequis

Pour cet article, vous devez disposer d’un compte LUIS gratuit que vous pouvez créer depuis le portail LUIS à l’adresse [http://www.luis.ai](http://www.luis.ai). 

## <a name="create-a-new-app"></a>Créer une application
Vous pouvez créer et gérer vos applications sur la page **Mes applications**. 

1. Connectez-vous au portail LUIS.

2. Sélectionnez **Créer une application**.

    [![](media/luis-quickstart-new-app/app-list.png "Capture d’écran de la liste des applications")](media/luis-quickstart-new-app/app-list.png)

3. Dans la boîte de dialogue, nommez votre application « HomeAutomation ».

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Capture d’écran de la boîte de dialogue contextuelle Créer une application")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Choisissez la culture de votre application. Pour cette application HomeAutomation, sélectionnez Anglais. Ensuite, sélectionnez **Terminé**. LUIS crée l’application HomeAutomation. 

    >[!NOTE]
    >La culture ne peut pas être modifiée une fois que l’application est créée. 

## <a name="add-prebuilt-domain"></a>Ajouter un domaine prédéfini

Sélectionnez **Domaines prédéfinis** dans le volet de navigation de gauche. Lancez une recherche sur le terme « Home ». Sélectionnez **Ajouter un domaine**.

[![](media/luis-quickstart-new-app/home-automation.png "Capture d’écran du domaine HomeAutomation affiché dans le menu de domaines prédéfinis")](media/luis-quickstart-new-app/home-automation.png)

Une fois le domaine ajouté, la zone de domaine prédéfini affiche un bouton **Supprimer le domaine**.

[![](media/luis-quickstart-new-app/remove-domain.png "Capture d’écran du domaine HomeAutomation avec le bouton Supprimer")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intentions et entités

Sélectionnez **Intentions** dans le volet de navigation de gauche pour afficher les intentions du domaine HomeAutomation. 

[![](media/luis-quickstart-new-app/home-automation-intents.png "Capture d’écran de la liste d’intentions avec le nom des intentions mis en surbrillance dans le tableau")](media/luis-quickstart-new-app/home-automation-intents.png)

Chaque intention comprend des exemples d’énoncés.

> [!NOTE]
> **Aucun** est une intention fournie par toutes les applications LUIS. Elle vous permet de gérer les énoncés qui ne correspondent pas aux fonctionnalités fournies par votre application. 

Sélectionnez l’intention **HomeAutomation.TurnOff**. Vous pouvez voir que l’intention contient une liste d’énoncés qui sont associés à des entités.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Capture d’écran de l’intention HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Former votre application

Sélectionnez **Former** dans la barre de navigation supérieure.

[![](media/luis-quickstart-new-app/trained.png "Capture d’écran de l’intention HomeAutomation.TurnOff avec notification de réussite en vert")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Test de l'application
Une fois que vous avez formé votre application, vous pouvez la tester. Sélectionnez **Tester** dans la barre de navigation supérieure. Saisissez un énoncé de test tel que « Éteindre les lumières » dans le volet Test interactif, puis appuyez sur Entrée. 

```
Turn off the lights
```

Vérifiez que l’intention avec le score le plus élevé correspond à l’intention attendue pour chaque énoncé de test.

Dans cet exemple, « Éteindre les lumières » doit apparaître comme l’intention avec le score le plus élevé pour « HomeAutomation.TurnOff ».

[![](media/luis-quickstart-new-app/test.png "Capture d’écran du panneau Test avec l’énoncé mis en surbrillance")](media/luis-quickstart-new-app/test.png)


Sélectionnez à nouveau **Tester** pour réduire le volet de test. 

## <a name="publish-your-app"></a>Publier votre application
Sélectionnez **Publier** dans la barre de navigation supérieure. 

[![](media/luis-quickstart-new-app/publish.png "Capture d’écran de l’application avec le bouton Publier mis en surbrillance")](media/luis-quickstart-new-app/publish.png)

Sélectionnez l’emplacement Production et le bouton **Publier**.

La barre de notification verte située en haut de la page indique que l’application a été publiée avec succès.

[![](media/luis-quickstart-new-app/published.png "Capture d’écran de l’application avec la notification de réussite de la publication")](media/luis-quickstart-new-app/published.png)

Une fois l’application publiée, vous pouvez utiliser l’URL du point de terminaison qui s’affiche sur la page **Publier l’application**.

[![](media/luis-quickstart-new-app/endpoint.png "Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Utiliser votre application
Vous pouvez tester votre point de terminaison publié dans un navigateur à l’aide de l’URL générée. Ouvrez cette URL dans votre navigateur et définissez le paramètre d’URL « &q » pour votre requête de test. Par exemple, ajoutez `turn off the living room light` à la fin de votre URL, puis appuyez sur Entrée. Le navigateur affiche la réponse JSON de votre point de terminaison HTTP.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "Capture d’écran du navigateur avec le résultat JSON identifiant l’intention TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Supprimer les ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Sélectionnez les points de suspension (***...***) à droite du nom de l’application dans la liste des applications, sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez appeler le point de terminaison à partir du code :

> [!div class="nextstepaction"]
> [Appeler un point de terminaison LUIS à l’aide d’un code](luis-get-started-cs-get-intent.md)
