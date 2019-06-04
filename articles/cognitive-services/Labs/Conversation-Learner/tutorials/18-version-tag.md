---
title: Comment utiliser le marquage de Version avec un modèle d’apprenant de Conversation - Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser le contrôle de version et le marquage avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 4067c7fb43cc200b8f49dbc14151c69a188e4e8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475712"
---
# <a name="how-to-use-version-tagging"></a>Comment utiliser le marquage de version

Ce tutoriel montre comment marquer des versions de votre modèle Conversation Learner et définir la version « Live ».  

## <a name="requirements"></a>Configuration requise
Ce tutoriel requiert l’utilisation de Bot Framework Emulator pour créer des boîtes de dialogue de journal, pas l’interface utilisateur web de la boîte de dialogue de journal.  

Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution :

    npm run tutorial-general

## <a name="details"></a>Détails

Les versions avec balises du modèle sont statiques, ce qui signifie que vous e pouvez pas les modifier ou en changer. Lorsque vous modifiez votre modèle, vous apportez toujours des modifications à la version Master. Lorsque vous ajoutez une nouvelle étiquette, Conversation Learner capture un instantané du modèle à ce moment précis. 

Votre Bot utilisera la version du Modèle que vous avez sélectionnée comme version « Live », mais les conversations qu’il possède ne seront visibles que lorsque la propriété « Editing Tag » sera définie sur « Master ». Si la propriété « Editing Tag » du Modèle est définie sur une valeur autre que « Master », l’instantané du Modèle est en lecture seule.

## <a name="steps"></a>Étapes

### <a name="install-the-bot-framework-emulator"></a>Installer l’émulateur Bot Framework

1. Accédez à [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Téléchargez et installez l’émulateur.

### <a name="create-a-model"></a>Création d'un modèle

1. Dans la page d’accueil Model List, cliquez sur le bouton `New Model`.
2. Dans le type de champ `Name`, « Tutorial-18-Versioning », appuyez sur la touche Entrée.
4. Dans le panneau gauche, cliquez sur « Settings » (« Paramètres »).
5. Copiez le contenu du champ CONVERSATION_LEARNER_MODEL_ID dans le presse-papiers.

### <a name="configure-the-emulator"></a>Configurer l’émulateur

1. Dans le dossier racine de Conversation Learner, ouvrez le fichier « .env ».
2. Ajoutez une ligne au fichier « .env » comme suit :
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Redémarrez le service d’Apprenant de conversation en sortant de l’invite de commande et en réexécutant :
    - `npm run tutorial-general`
4. Dans l’émulateur Bot Framework, créez une nouvelle configuration de bot, définissez l’URL du point de terminaison sur `http://localhost:3978/api/messages`

### <a name="version-1"></a>version 1

Nous allons créer une seule action pour la version 1.

1. Dans le volet gauche de l’interface utilisateur Web, cliquez sur « Actions », puis cliquez sur le bouton `New Action`.
2. Dans le champ « Bot's Response » (« Réponse du robot »), entrez « hi there (version 1) » (« Salut (version 1) »).
3. Cliquez sur le bouton `Save`.

Nous allons maintenant l’étiqueter en tant que « Version 1 » du Modèle.

1. Dans le panneau de gauche, cliquez sur « Settings », puis cliquez sur l’icône ![](../media/tutorial18_version_tags.PNG)« Version Tags » pour faire apparaître le bouton `New Tag` sur lequel vous devez cliquer.
    - Appelez-le « Version 1 »
1. Dans la liste déroulante « Live Tag », sélectionnez « Version 1 ».  
    - Les canaux faisant appel à ce Bot utiliseront désormais la « Version 1 » de notre modèle.
    - Les entités, actions et boîtes de dialogue de formation de cet modèle Version 1 ne sont plus modifiables.
    - Si vous sélectionnez « Version 1 » en tant que « Editing Tag » (« Balise de modification »), le Modèle sera disponible en lecture seule.
    - Conservez la valeur « Master » pour la balise de modification car il s’agit de la seule version du modèle qui peut être modifiée.

« Version 1 » figure à présent dans la grille « Version Tags » (« Balises de version »).

### <a name="version-2"></a>version 2

Nous allons maintenant modifier notre modèle pour le distinguer de la version 1.

1. Dans le panneau de gauche, cliquez sur « Actions ».
2. Dans la grille Actions, cliquez sur « bonjour (version 1) ».
3. Dans le champ « Bot’s response » (« Réponse du bot »), entrez « hi there (version 2) » (« Salut (version 2) »).
4. Cliquez sur le bouton `Save`.
5. Cliquez sur le bouton `New Action`.
6. Dans le champ « Bot’s response » (« Réponse du bot »), entrez « bye bye (version 2) » (« Au revoir (version 2) »).

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Confirmer que l’Émulateur de Bot Framework utilise la version 1

1. Dans l’Émulateur de Bot Framework, tapez le message, « Hey there » (« Bonjour »).
2. Notez que le bot répond « hi there (version 1) » (« bonjour (version 1) »).
    - Cela permet de vérifier que la version 1 est active.

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Afficher les journaux d’activité de conversation dans l’interface Web de Conversation Learner

1. Dans le panneau gauche, cliquez sur « Log Dialogs »
    - Si vous ne voyez pas de boîtes de dialogue, cliquez sur le bouton Actualiser.
2. Notez la présence de l’étiquette « Version 1 » dans la grille.
3. Dans la grille, cliquez « hi there (version 1) » (« bonjour (version 1) »).

> [!NOTE]
> Nous pouvons apporter des corrections en choisissant parmi toutes les fonctionnalités de Conversation Learner actuellement disponibles, cependant, ces modifications seront apportées à la version maître et non pas à la version 1.

Vous savez maintenant comment le contrôle de version fonctionne et comment vous pouvez interagir avec le bot à l’aide de l’émulateur Bot Framework.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités d’enum et définir les actions d’entité](./tutorial-enum-set-entity.md)
