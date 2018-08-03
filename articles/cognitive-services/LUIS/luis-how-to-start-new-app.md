---
title: Créer une application avec LUIS | Microsoft Docs
description: Créez et gérez vos applications sur la page web de Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225412"
---
# <a name="create-an-app"></a>Créer une application
Vous créez une application de différentes manières : 

* [Démarrez](#create-new-app) avec une application vide et créer des intentions, des énoncés et des entités.
* [Démarrez](#create-new-app) avec une application vide et ajoutez un [domaine prédéfini](luis-how-to-use-prebuilt-domains.md).
* [Importez une application LUIS](#import-new-app) à partir d’un fichier JSON qui contient déjà des intentions, des énoncés et des entités.

## <a name="what-is-an-app"></a>Qu’est-ce qu’une application ?
L’application contient des [versions](luis-how-to-manage-versions.md) de votre modèle, ainsi que tous les [collaborateurs](luis-how-to-collaborate.md) de l’application. Lorsque vous créez l’application, vous sélectionnez la culture ([langage](luis-supported-languages.md)) qui **ne peut pas être modifiée ultérieurement**. 

La version par défaut d’une nouvelle application est « 0.1 ». 

Vous pouvez créer et gérer vos applications sur la page **Mes applications**. Vous pouvez toujours accéder à cette page en sélectionnant **Mes applications** dans la barre de navigation supérieure du site web [LUIS](luis-reference-regions.md). 

[![](media/luis-create-new-app/apps-list.png "Capture d’écran de la liste des applications")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Créer une application

1. Sur la page **Mes applications**, sélectionnez **Créer une application**.
2. Dans la boîte de dialogue, nommez votre application « TravelAgent ».

    ![Boîte de dialogue Créer une application](./media/luis-create-new-app/create-app.png)

3. Choisissez la culture de votre application (pour l’application TravelAgent, sélectionnez Anglais), puis sélectionnez **Terminé**. 

    >[!NOTE]
    >La culture ne peut pas être modifiée une fois que l’application est créée. 

## <a name="import-new-app"></a>Importer une nouvelle application
Vous pouvez définir le nom (50 caractères max.), la version (10 caractères max.) et la description d’une application dans le fichier JSON. Des exemples de fichiers d’application JSON sont disponibles dans [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. Sur la page **Mes applications**, sélectionnez **Import new app** (Importer une nouvelle application).
2. Dans la boîte de dialogue **Import new app** (Importer une nouvelle application), sélectionnez le fichier JSON définissant l’application LUIS.

    ![Boîte de dialogue d’importation d’une nouvelle application](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Exporter l’application
1. Sur la page **Mes applications**, sélectionnez les points de suspension (***…***) à la fin de la ligne de l’application.

    [![](media/luis-create-new-app/apps-list.png "Capture d’écran de la boîte de dialogue contextuelle des actions par application")](media/luis-create-new-app/three-dots.png#lightbox)

2. Sélectionnez **Export app** (Exportation l’application) dans le menu. 

## <a name="rename-app"></a>Renommer l’application

1. Sur la page **Mes applications**, sélectionnez les points de suspension (***…***) à la fin de la ligne de l’application. 
2. Sélectionnez **Renommer** dans le menu.
3. Entrez le nouveau nom de l’application et sélectionnez **Terminé**.

## <a name="delete-app"></a>Supprimer l’application

> [!CAUTION]
> Vous supprimez l’application pour tous les collaborateurs et le propriétaire. [Exportez](#export-app) l’application avant de la supprimer. 

1. Sur la page **Mes applications**, sélectionnez les points de suspension (***…***) à la fin de la ligne de l’application. 
2. Sélectionnez **Supprimer** dans le menu.
3. Sélectionnez **OK** dans la fenêtre de confirmation.

## <a name="export-endpoint-logs"></a>Exporter les journaux du point de terminaison
Les journaux contiennent la requête, l’heure UTC et la réponse JSON LUIS.

1. Sur la page **Mes applications**, sélectionnez les points de suspension (***…***) à la fin de la ligne de l’application. 
2. Sélectionnez **Export endpoint logs** (Exporter les journaux du point de terminaison) dans le menu.

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Étapes suivantes

Votre première tâche dans l’application consiste à [ajouter des intentions](luis-how-to-add-intents.md).