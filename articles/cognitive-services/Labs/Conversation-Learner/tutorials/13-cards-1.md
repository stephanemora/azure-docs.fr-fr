---
title: Comment utiliser des cartes avec un modèle d’Apprenant de conversation, 1ère partie - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des cartes avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 988a2433f098f41bca4796299825293efd4de44b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171132"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Comment utiliser des cartes (partie 1 de 2)

Ce tutoriel montre comment ajouter et utiliser une carte simple dans votre bot.

> [!NOTE]
> Actuellement, l’Apprenant de conversation attend que vos fichiers de définition de carte se trouvent dans un répertoire appelé « cartes », dans le répertoire où le bot est démarré. Nous rendrons prochainement cette option configurable.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 13](http://aka.ms/cl-tutorial-13-preview)](http://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

Les cartes sont des éléments de l’interface utilisateur qui permettent à l’utilisateur de sélectionner une option dans la conversation. 

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur Tutorial-13-Cards-1. 

### <a name="the-card"></a>La carte

La définition de la carte est à l’emplacement suivant : C:\<installedpath\>\src\cards\prompt.json.

Le système s’attend à trouver les définitions de la carte dans ce répertoire de cartes.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Notez le type de corps `TextBlock` et l’espace réservé `{{question}}` dans le champ de texte.
> Il y a deux boutons Envoyer et le texte qui est envoyé pour chacun.

### <a name="actions"></a>Actions

Nous avons créé trois actions. Comme vous le voyez ci-dessous, la première action est une carte.

![](../media/tutorial13_actions.PNG)

Voyons comment le type d’action de carte a été créé :

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> L’entrée de la question et les boutons 1 et 2. Ce sont des modèles de références dans la carte où vous entrez la question et les réponses respectives. Vous pouvez également référencer et utiliser des entités, ou un mélange de texte et d’entités.

L’icône en forme d’œil vous montre à quoi ressemble la carte.

### <a name="train-dialog"></a>Boîte de dialogue d’apprentissage

Examinons une boîte de dialogue d’apprentissage.

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « Bonjour ».
2. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner « Invite pour aller à gauche ou à droite ».
    - Cliquer sur « gauche » ou sur « droite » équivaut à taper « gauche » ou « droite » respectivement. 
4. Cliquez sur Attribuer un score aux actions.
4. Cliquez pour sélectionner « Gauche ». Créez une action sans attente.
6. Cliquez pour sélectionner « Invite pour aller à gauche ou à droite ».
4. Cliquez « droite ».
5. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner « droite ».
6. Cliquez pour sélectionner « Invite pour aller à gauche ou à droite ».
4. Cliquez sur Tests terminés.

Vous savez maintenant comment fonctionnent les cartes. Elles sont définies dans le répertoire de cartes en tant que modèles json. Les modèles apparaissent dans l’interface utilisateur, et vous pouvez les remplir à l’aide d’une chaîne ou d’une entité, ou d’une combinaison des deux.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cartes, 2e partie](./14-cards-2.md)
