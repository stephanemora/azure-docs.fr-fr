---
title: Guide pratique pour utiliser les rappels de session avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les rappels de session avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703962"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Guide pratique pour utiliser les rappels de session avec un modèle Conversation Learner

Ce didacticiel présente des sessions, leur gestion et les rappels onSessionStart et onSessionEnd de Conversation Learner.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel Rappels de session](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite l’exécution du bot « tutorialSessionCallbacks.ts ».

    npm run tutorial-session-callbacks

## <a name="details"></a>Détails
Ce didacticiel décrit le concept d’une session, comment les sessions sont gérées par défaut et comment vous pouvez remplacer ce comportement.

Dans Conversation Learner, une session représente un échange interactif ininterrompu avec le bot. Les sessions peuvent avoir plusieurs tours, mais être terminées par programmation en cas d’inactivité si plus de trente minutes se sont écoulées.  Consultez la page d’aide sur les « Limites » pour obtenir des informations sur la modification de la durée du délai d’expiration de la session par défaut.

Cette longue période d’inactivité entraîne la création par le bot d’une nouvelle session et la réinitialisation du réseau neuronal récurrent à son état initial. Par défaut, toutes les valeurs de l’entité seront effacées. Ce comportement par défaut permettant d’effacer les valeurs d’entité peut être modifié comme indiqué ci-dessous.

### <a name="load-the-demo-model"></a>Charger le modèle de démonstration

Dans l’interface utilisateur web, cliquez sur « Importer des didacticiels », puis sélectionnez le modèle nommé « Tutorial-13-SessionCallbacks ».

### <a name="code-for-the-callbacks"></a>Code pour les rappels

Des exemples de code pour deux rappels de ce modèle sont disponibles dans : `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback : cette méthode définit l’entité BotName.
- OnSessionEndCallback : vous pouvez spécifier ce que vous voulez conserver. Cela effacera toutes les entités sauf le nom d’utilisateur et le téléphone de l’utilisateur.

Chaque rappel est facultatif.

### <a name="actions"></a>Actions

Quatre actions sont définies dans le modèle. Les actions existantes sont affichées dans l’affichage de grille pour « Actions ».

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Création d’une action de fin de session (pour invoquer le rappel)

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle action ».
2. Sélectionnez le « Type d’entité » « ENDSESSION ».
3. Dans le champ « Données... », tapez « Terminé ».
4. Cliquez sur le bouton « Créer ».

### <a name="edit-an-existing-action"></a>Modifier une action existante

1. Sélectionnez l’action « Donc, $UserName, vous êtes dans $UserLocation » dans l’affichage de grille.
2. Décochez la case « Attendre une réponse ».
3. Cliquez sur le bouton « Enregistrer ».

### <a name="chaining-actions"></a>Chaînage d’actions

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue d'apprentissage », puis sur le bouton « Nouveau dialogue d'apprentissage ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Bonjour ».
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse « Bonjour, je suis Botty. Quel est votre nom ? ».
5. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Lars ».
6. Sélectionnez la réponse « Bonjour, Lars. Quel est votre numéro de téléphone ? ».
7. Dans le panneau de conversation, sous « Entrez votre message... », tapez « 555-555-5555 ».
8. Cliquez sur le bouton « Noter les actions ».
9. Sélectionnez la réponse, « Pouvez-vous indiquer votre emplacement à Botty, Lars ? ».
10. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Seattle ».
11. Cliquez sur le bouton « Noter les actions ».
12. Sélectionnez la réponse, « Donc, Lars, vous êtes à Seattle ».
13. Sélectionnez la réponse « Terminé ».
14. Cliquez sur le bouton « Enregistrer ».

### <a name="testing-the-model"></a>Tester le modèle

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue du journal », puis sur le bouton « Nouvelle boîte de dialogue du journal ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Bonjour ».
3. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Lars ».
4. Dans le panneau de conversation, sous « Entrez votre message... », tapez « 555-555-5555 ».
5. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Seattle ».
    - À ce stade, toutes les valeurs d’entité à l’exclusion de l’emplacement doivent être conservées.
6. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Bonjour ».
7. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Détroit ».
8. Cliquez sur le bouton « Délai d’expiration de session ».
    - Cliquer sur ce bouton exerce le bot à répondre après de longues périodes d’inactivité.
9. Cliquez sur le bouton « OK ».
10. Cliquez sur le bouton « Test terminé ».

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appels d’API](./14-api-calls.md)
