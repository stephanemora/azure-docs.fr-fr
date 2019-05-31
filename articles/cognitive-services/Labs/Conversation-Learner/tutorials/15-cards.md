---
title: Comment utiliser des cartes avec un modèle d’Apprenant de conversation, 1ère partie - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des cartes avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: d0b87597c506aca5c3b4f6f3815f58656203ac3c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389516"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Comment utiliser des cartes (partie 1 de 2)

Ce tutoriel montre comment ajouter et utiliser une carte simple dans votre bot.

> [!NOTE]
> Conversation Learner attend actuellement que vos fichiers de définition de carte se trouvent dans un répertoire appelé « cards », dans le répertoire où le Bot est démarré.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel Cards](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

Les cartes sont des éléments de l’interface utilisateur qui permettent à l’utilisateur de sélectionner une option dans la conversation. 

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans l’interface utilisateur web, cliquez sur « Import Tutorials » (Importer des tutoriels), puis sélectionnez le modèle nommé « Tutorial-15-Cards ».

### <a name="the-card"></a>La carte

La définition de la carte se trouve à l’emplacement suivant : C:\<installedpath\>\src\cards\prompt.json.

Le système s’attend à trouver les définitions de la carte dans ce répertoire « cards ».

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Notez le type de corps « TextBlock » et l’espace réservé « {{question}} » dans le champ de texte.
> Il y a deux boutons Envoyer et le texte qui est envoyé pour chacun.

### <a name="actions"></a>Actions

Nous avons créé trois actions. Comme vous le voyez ci-dessous, la première action est une carte.

![](../media/tutorial13_actions.PNG)

Voyons comment le type d’action de carte a été créé :

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> La carte contient trois paramètres différents : question input, button 1 et button 2. Ces éléments sont des modèles de références dans la carte où vous entrez la question et les réponses respectives. Vous pouvez également référencer et utiliser des entités, ou un mélange de texte et d’entités.

L’icône en forme d’œil vous montre à quoi ressemble la carte.

### <a name="practicing-creating-card-actions"></a>S’entraîner à créer des actions de cartes

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Sélectionnez « CARD » pour le « Type d’action ».
3. Sélectionnez l’option « prompt » (« invite ») dans la liste « Template » (« Modèle »).
4. Dans le champ « question », tapez « Go left or right » (« Aller à gauche ou à droite »)
5. Dans le champ « button1 », tapez « left » (« gauche »)
6. Dans le champ « button2 », tapez « right » (« droite »)
7. Cliquez sur le bouton « Cancel » (« Annuler »).

### <a name="train-dialog-using-an-adaptive-card"></a>Boîte de dialogue d’apprentissage utilisant une carte adaptative

1. Dans le volet gauche, cliquez sur « Train Dialogs » (« Boîtes de dialogue d’apprentissage »), puis le bouton « New Train Dialog » (« Nouveau dialogue d’apprentissage »).
2. Dans le panneau de conversation, sous « Type your message... » (« Entrez votre message... »), tapez « hi » (« Bonjour »).
3. Cliquez sur le bouton « Score Actions ».
4. Sélectionnez la réponse, « prompt: question: Go left or right? » (« Aller à gauche ou à droite ? »)
    - L’icône représentant un œil peut être utilisé pour afficher un aperçu de la carte
5. Dans le panneau de conversation, cliquez sur le bouton « Left » (« Gauche ») de l’invite affichée.
6. Cliquez sur le bouton « Score Actions ».
7. Sélectionnez la réponse « Left » (« Gauche »)
8. Cliquez sur le bouton « Save » (« Enregistrer »).
9. Sélectionnez la réponse, « prompt: question: Go left or right? » (« Aller à gauche ou à droite ? »)
10. Dans le panneau de conversation, cliquez sur le bouton « Right » (« Droite ») de l’invite affichée.
11. Cliquez sur le bouton « Score Actions ».
12. Sélectionnez la réponse « Right» (« Droite »)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bots hybrides](./16-hybrid-bots.md)
