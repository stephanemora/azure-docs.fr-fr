---
title: Comment déployer un bot Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment déployer un bot Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56dab93087249082330c0f685e5457bc2585664c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55237929"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Comment déployer un bot Apprenant de conversation

Ce document explique comment déployer un bot Apprenant de conversation, localement ou dans Azure.

## <a name="prerequisite-determine-the-model-id"></a>Prérequis : déterminer l’ID du modèle 

Pour exécuter un bot en dehors de l’interface utilisateur de l’Apprenant de Conversation, vous devez définir l’ID de modèle Apprenant de conversation à utiliser par le bot : par exemple, l’ID du modèle Machine Learning dans le cloud de l’Apprenant de conversation.  (En revanche, lorsque vous exécutez le bot via l’interface utilisateur de l’Apprenant de conversation, c’est l’interface utilisateur qui choisit l’ID du modèle).  

Voici comment obtenir l’ID du modèle :

1. Démarrez votre bot et l’interface utilisateur de l’Apprenant de conversation.  Consultez le guide de démarrage rapide pour obtenir des instructions complètes. Pour résumer :

    Dans une fenêtre de commande :

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Dans une autre fenêtre de commande

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Ouvrez http://localhost:5050 dans la fenêtre du navigateur 

3. Cliquez sur le modèle Apprenant de conversation pour lequel vous souhaitez obtenir l’ID

4. Cliquez sur « Paramètres » à gauche dans la barre de navigation.

5. Le GUID « ID du modèle » s’affiche en haut de la page.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Option 1 : déploiement d’un bot Conversation Learner à exécuter localement

Déploie un bot sur votre ordinateur local et montre comment vous pouvez y accéder à l’aide de l’émulateur Bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Configurez votre bot pour l’accès à l’extérieur de l’interface utilisateur de l’Apprenant de Conversation

Lorsque vous exécutez un bot localement, ajoutez l’ID d’application au fichier `.env` du bot :

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Ensuite, démarrez votre bot :

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Le robot est maintenant en cours d’exécution locale.  Vous pouvez y accéder avec l’émulateur Bot Framework.

### <a name="download-and-install-the-emulator"></a>Téléchargez et installez l’émulateur

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Connectez l’émulateur à votre bot

1. Dans le coin supérieur gauche de l’émulateur, dans la zone « Entrez votre URL de point de terminaison », entrez `http://127.0.0.1:3978/api/messages`.  Laissez les autres champs vides, puis cliquez sur « Se connecter ».

2. Vous conversez maintenant avec votre robot.

## <a name="option-2-deploy-to-azure"></a>Option 2 : Déployer dans Azure

Publiez votre robot d’Apprenant de conversation de la même façon que vous publieriez n’importe quel autre bot. À un niveau élevé, vous chargez votre code sur un site web hébergé, définissez les valeurs de configuration appropriées, puis enregistrez le bot avec différents canaux. Cette vidéo donne des instructions détaillées pour publier votre bot à l’aide d’Azure Bot Service.

Une fois que le bot est déployé et en cours d’exécution, vous pouvez y connecter différents canaux tels que Facebook, Microsoft Teams, Skype, etc., à l’aide d’une inscription aux canaux de bots Azure. Pour voir de la documentation sur ce processus, consultez : https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Vous trouverez ci-dessous des instructions pas à pas pour le déploiement d’un bot Apprenant de Conversation sur Azure.  Ces instructions supposent que votre source de bot est disponible à partir d’une source basée sur le cloud, telle que Azure DevOps Services, GitHub, BitBucket ou OneDrive, et configure votre bot pour le déploiement continu.

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com

2. Créez une ressource « bot d’application web » 

    1. Donnez un nom au bot
    2. Cliquez sur « Modèle de bot », choisissez « Node.js », puis « De base », et cliquez sur le bouton « Sélectionner »
    3. Cliquez sur « Créer » pour créer le bot d’application web.
    4. Attendez que la ressource du bot d’application web soit créée.

3. Dans le portail Azure, modifiez la ressource bot d’application web que vous venez de créer.

    1. Cliquez à gauche sur l’élément de navigation « Paramètres de l'application »
    1. Faites défiler vers le bas jusqu’à la section « Paramètres de l’application »
    2. Ajoutez les paramètres suivants :

        Variable d’environnement | value
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_MODEL_ID      | Guide de l’lD d’application, obtenu à partir de l’interface utilisateur de l’Apprenant de conversation, sous « paramètres » du modèle>
        LUIS_AUTHORING_KEY               | Clé de création LUIS pour ce modèle
        LUIS_SUBSCRIPTION_KEY            | Non obligatoire, mais recommandée pour les bots publiés afin d’éviter d'utiliser votre quota de création.
    
    4. Cliquez sur « Enregistrer » dans le haut de la page
    5. Sur la gauche, ouvrez l’élément de navigation « Générer »
    6. Cliquez sur « Configurer le déploiement continu » 
    7. Sous les déploiements, cliquez sur l’icône « Installation »
    8. Cliquez sur « Paramètres requis »
    9. Sélectionnez la source où votre code bot est disponible et configurez-la.
