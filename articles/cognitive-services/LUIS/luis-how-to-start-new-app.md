---
title: Créer une application
titleSuffix: Language Understanding - Azure Cognitive Services
description: Créez et gérez vos applications sur la page web de Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: daffbe3f3158bb232f7db7ac90d766661e937643
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679642"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Créer une application LUIS dans le portail LUIS
Il existe deux façons de créer une application LUIS. Vous pouvez créer une application LUIS dans le portail [LUIS](https://www.luis.ai) ou par le biais des [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) de création LUIS.

## <a name="using-the-luis-portal"></a>Utilisation du portail LUIS
Vous pouvez créer une application dans le portail LUIS de plusieurs manières :

* Démarrez avec une application vide et créer des intentions, des énoncés et des entités.
* Démarrez avec une application vide et ajoutez un [domaine prédéfini](luis-how-to-use-prebuilt-domains.md).
* Importez une application LUIS à partir d’un fichier JSON qui contient déjà des intentions, des énoncés et des entités.

## <a name="using-the-authoring-apis"></a>Utilisation des API de création
Vous pouvez créer une application avec les API de création de deux manières :

* [Démarrez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) avec une application vide et créer des intentions, des énoncés et des entités.
* [Démarrez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) avec un domaine prédéfini.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Créer une application dans LUIS

1. Sur la page **Mes applications**, sélectionnez **Créer une application**.

    ![Liste des applications LUIS](./media/luis-create-new-app/apps-list.png)


2. Dans la boîte de dialogue, nommez votre application « TravelAgent ».

    ![Boîte de dialogue Créer une application](./media/luis-create-new-app/create-app.png)

3. Choisissez la culture de votre application (pour l’application TravelAgent, sélectionnez Anglais), puis sélectionnez **Terminé**. 

    > [!NOTE]
    > La culture ne peut pas être modifiée une fois que l’application est créée. 

## <a name="import-an-app-from-file"></a>Importer une application à partir du fichier

1. Sur la page **Mes applications**, sélectionnez **Import new app** (Importer une nouvelle application).
1. Dans la boîte de dialogue contextuelle, sélectionnez un fichier JSON d’application valide, puis **fait**.

### <a name="import-errors"></a>Erreurs d’importation

Erreurs possibles sont : 

* Une application portant ce nom existe déjà. Importer à nouveau l’application, puis définissez le **nom facultatif** vers un nouveau nom. 

## <a name="export-app-for-backup"></a>Exporter l’application pour la sauvegarde

1. Sur **mes applications** page, sélectionnez **exporter**.
1. Sélectionnez **exporter au format JSON**. Votre navigateur télécharge la version active de l’application.
1. Ajoutez ce fichier à votre système de sauvegarde pour archiver le modèle.

## <a name="export-app-for-containers"></a>Exporter app pour conteneurs

1. Sur **mes applications** page, sélectionnez **exporter**.
1. Sélectionnez **exporter en tant que conteneur** puis sélectionnez quel emplacement publié (production ou phase) que vous voulez exporter.
1. Utiliser ce fichier avec votre [conteneur de LUIS](luis-container-howto.md). 

    Si vous êtes intéressé par exportation un formé, mais ne pas encore publié de modèle à utiliser avec le conteneur de LUIS, accédez à la **Versions** page et d’exportation à partir de là. 

## <a name="delete-app"></a>Supprimer l’application

1. Sur la page **Mes applications**, sélectionnez les points de suspension (...) à la fin de la ligne de l’application.
1. Sélectionnez **Supprimer** dans le menu.
1. Sélectionnez **OK** dans la fenêtre de confirmation.

## <a name="next-steps"></a>Étapes suivantes

Votre première tâche dans l’application consiste à [ajouter des intentions](luis-how-to-add-intents.md).
