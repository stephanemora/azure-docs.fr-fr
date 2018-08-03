---
title: Guide pratique pour créer un modèle Conversation Learner avec Node.js – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Conversation Learner avec Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 68ff9c5402c3fa409999e9933a6c1f7bf6d5a089
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172328"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Guide pratique pour créer un modèle Conversation Learner avec Node.js

Conversation Learner simplifie la création de bots. Il permet un flux de travail hybride pour le développement, ce qui permet au code écrit manuellement et à la Machine Learning de réduire la quantité de code nécessaire pour écrire des bots. Certains éléments fixes du modèle, comme le fait de vérifier si l’utilisateur est connecté ou d’effectuer une demande d’API pour vérifier le stock du magasin, peuvent quand même être codés. Toutefois, les autres modifications de sélection d’action et d’état peuvent être apprises à partir des exemples de boîtes de dialogue fournis par l’expert du domaine ou le développeur.

## <a name="invitation-required"></a>Invitation requise

*Une invitation est requise pour accéder à Project Conversation Learner.*

Project Conversation Learner se compose d’un kit de développement logiciel (SDK) que vous ajoutez à votre bot et d’un service cloud auquel le kit de développement logiciel (SDK) accède pour la Machine Learning.  Actuellement, l’accès au service cloud Project Conversation Learner nécessite une invitation.  Si vous n’avez pas encore été invité, [demandez une invitation](https://aka.ms/conversation-learner-request-invite).  Si vous n’avez pas reçu d’invitation, vous ne pourrez pas accéder à l’API cloud.

## <a name="prerequisites"></a>Prérequis

- Node 8.5.0 ou version ultérieure et npm 5.3.0 ou version ultérieure. Installer à partir de [https://nodejs.org](https://nodejs.org).
  
- Clé de création LUIS :

  1. Connectez-vous à [http://www.luis.ai](http://www.luis.ai).

  2. Cliquez sur votre nom dans le coin supérieur droit, puis sur « paramètres »

  3. La clé de création s’affiche sur la page qui apparaît

  (Votre clé de création LUIS a deux rôles.  Tout d’abord, elle sert de clé de création Project Conversation Learner.  En second lieu, Conversation Learner utilise LUIS pour l’extraction d’entité ; la clé de création LUIS est utilisée pour créer des modèles LUIS en votre nom.)

- Navigateur web Google Chrome. Installer à partir de [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- git. Installer à partir de [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Installer à partir de [https://code.visualstudio.com/](https://code.visualstudio.com/). Notez que cette action n’est pas requise, mais recommandée.

## <a name="quick-start"></a>Démarrage rapide 

1. Installer et générer :

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Au cours de `npm install`, vous pouvez ignorer cette erreur si elle se produit : `gyp ERR! stack Error: Can't find Python executable`

2. Configurer :

   Créez un fichier nommé `.env` dans le répertoire `cl-bot-01`.  Le contenu du fichier doit être :

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Démarrer le bot :

    ```
    npm start
    ```

    Cette commande exécute le bot vide générique dans `cl-bot-01/src/app.ts`.

3. Exécuter l’interface utilisateur de Conversation Learner :

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Ouvrez http://localhost:5050 dans la fenêtre du navigateur 

Vous utilisez maintenant Conversation Learner et pouvez créer et enseigner un modèle Conversation Learner.  

> [!NOTE]
> Au démarrage, Project Conversation Learner est disponible sur invitation.  Si http://localhost:5050 affiche une erreur `403` HTTP, cela signifie que votre compte n’a pas été invité.  Veuillez [demander une invitation](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Didacticiels, démonstrations et changement de bot

Les instructions ci-dessus permettent de démarrer le bot générique vide.  Pour exécuter un bot de didacticiel ou de démonstration à la place :

1. Si l’interface utilisateur web Conversation Learner est ouverte, revenez à la liste des modèles : http://localhost:5050/home.
    
2. Si un autre bot est en cours d’exécution (comme `npm start` ou `npm run demo-pizza`), arrêtez-le.  Vous n’avez pas besoin d’arrêter le processus de l’interface utilisateur ni de fermer le navigateur web.

3. Exécutez un bot de démonstration à partir de la ligne de commande (étape 2 ci-dessus).  Les démonstrations incluent :

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Si vous ne l’avez pas déjà fait, basculez sur l’interface utilisateur web Conversation Learner dans Chrome en chargeant http://localhost:5050/home. 

5. Cliquez sur « Import tutorials » (Importer des didacticiels) (cette action n’est nécessaire qu’une seule fois).  Cette opération prend environ une minute et copie les modèles Conversation Learner pour tous les didacticiels dans votre compte Conversation Learner.

6. Cliquez sur le modèle de démonstration dans l’interface utilisateur Conversation Learner qui correspond à la démonstration que vous avez démarrée.

Les fichiers sources des démonstrations sont sous `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Créer un bot qui inclut le code principal

1. Si l’interface utilisateur web Conversation Learner est ouverte, revenez à la liste des modèles : http://localhost:5050/home.
    
2. Si un bot est en cours d’exécution (comme `npm run demo-pizza`), arrêtez-le.  Vous n’avez pas besoin d’arrêter le processus de l’interface utilisateur ni de fermer le navigateur web.

3. Si vous le souhaitez, modifiez le code dans `cl-bot-01/src/app.ts`.

4. Régénérer puis redémarrer le bot :

    ```bash    
    npm run build
    npm start
    ```

5. Si vous ne l’avez pas déjà fait, basculez sur l’interface utilisateur web Conversation Learner dans Chrome en chargeant http://localhost:5050/home. 

6. Créez un modèle Conversation Learner dans l’interface utilisateur et commencez l’apprentissage.

7. Pour apporter des modifications au code dans `cl-bot-01/src/app.ts`, répétez les étapes ci-dessus, en commençant à l’étape 2.

## <a name="vscode"></a>VSCode

Dans VSCode, il existe des configurations d’exécution pour chaque démonstration et pour le « bot vide » dans `cl-bot-01/src/app.ts`.  Ouvrez le dossier `cl-bot-01` dans VSCode.

## <a name="advanced-configuration"></a>Configuration avancée

Il existe un modèle de fichier `.env.example` qui indique les variables d’environnement que vous pouvez définir pour configurer les exemples.

Vous pouvez ajuster ces ports pour éviter les conflits avec d’autres services en cours d’exécution sur votre ordinateur en ajoutant un fichier `.env` à la racine du projet :

```bash
cp .env.example .env
```

Cette opération utilise la configuration standard, ce qui vous permet d’exécuter votre bot localement et de commencer à utiliser Conversation Learner.  (Plus tard, pour déployer votre bot sur l’infrastructure de bot, certaines modifications devront être apportées à ce fichier.)

## <a name="support"></a>Support

- Étiqueter des questions sur [Stack Overflow](https://stackoverflow.com) avec « microsoft cognitive »
- Demander une fonctionnalité sur notre [page User Voice](https://aka.ms/conversation-learner-uservoice)
- Ouvrir un problème sur notre [référentiel github](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Contribution

Ce projet a adopté le [Code de conduite open source de Microsoft](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, consultez la [FAQ sur le code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou envoyez vos questions ou vos commentaires à [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="source-repositories"></a>Référentiels source

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Hello world](./tutorials/1-hello-world.md)
