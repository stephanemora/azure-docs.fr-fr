---
title: Guide pratique pour utiliser des cartes avec un modèle Conversation Learner, 2e partie – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des cartes avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1c7c88742c69041594006add76f7e3c642c64dec
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170570"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Comment utiliser des cartes (partie 1 de 2)
Ce tutoriel montre comment ajouter une carte de formulaire à remplir à votre bot. Il indique comment les champs de formulaire se déplacent vers les entités.

L’Apprenant de conversation attend que vos fichiers de définition de carte se trouvent dans un répertoire appelé « cartes », dans le répertoire où le bot est démarré.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel 14](http://aka.ms/cl-tutorial-14-preview)](http://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

Les cartes sont des éléments de l’interface utilisateur qui permettent à l’utilisateur de sélectionner une option dans la conversation. 

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur Tutorial-14-Cards-2. 

### <a name="the-card"></a>La carte

La définition de la carte est à l’emplacement suivant : C:\<installedpath\>\src\cards\shippingAddress.json.

Cette carte réunit trois champs de l’adresse de livraison : rue, ville et état.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Actions

Nous avons créé trois actions. Comme vous le voyez ci-dessous, la première action est une carte.

![](../media/tutorial14_actions.PNG)

Voyons comment le type d’action de carte a été créé :

- Notez Address-Street, ou le type est Input.text et son ID.
- De même, il y a Address-City et une liste déroulante avec l’ID d’Address-State.

Ces ID sont importants, comme quand les champs sont renseignés et envoyés, puisqu’il s’agit des noms d’entités qui recevront ces valeurs dans le bot.

## <a name="entities"></a>Entités
Nous avons défini trois entités correspondant à la carte, comme nous l’avons vu ci-dessus.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Actions

Nous avons défini deux actions.

![](../media/tutorial14_actions.PNG)

- La première est la carte d’adresse d’expédition, où le type d’action est CARTE, et où le modèle est sélectionné dans la liste déroulante shippingAddress.
- La deuxième est une action simple pour relire l’adresse de livraison.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Boîte de dialogue d’apprentissage

Examinons une boîte de dialogue d’apprentissage.

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « Bonjour ».
2. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner «Adresse d’expédition ».
4. Renseignez la carte et envoyez-la.
    - Notez que ces valeurs ont désormais été déplacées dans la mémoire de l’entité. Aucune analyse n’est nécessaire, puisque le formulaire déjà partitionné les entrées.
5. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner «$Adresse d’expédition ».
4. Cliquez sur Tests terminés.

![](../media/tutorial14_train_dialog.PNG)

Vous savez maintenant comment obtenir les valeurs de la carte, qui comprend des champs à renseigner et des listes déroulantes, et comment les capturer et les regrouper dans des entités de bot.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Création et suppression de branche](./15-branching-and-undo.md)
