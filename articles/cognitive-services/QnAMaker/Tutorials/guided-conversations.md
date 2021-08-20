---
title: Ajouter des conversations guidées avec des invites multitours
description: Dans ce tutoriel, apprenez à réaliser des conversations guidées avec des invites multitours.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 5c664dd2d08bb743ebe3e813808c559a30d901fc
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235221"
---
# <a name="add-guided-conversations-with-multi-turn-prompts"></a>Ajouter des conversations guidées avec des invites multitours

 Dans ce tutoriel, nous utilisons la [FAQ relative au stylet Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) pour créer une base de connaissances.

Pour cet exemple, supposons que les utilisateurs demandent des détails supplémentaires sur le stylet Surface, en particulier comment résoudre les problèmes liés à leur stylet, mais qu’ils n’obtiennent pas les bonnes réponses. Nous ajoutons donc des invites pour prendre en charge d’autres scénarios et guider les utilisateurs vers les bonnes réponses à l’aide d’invites multitours.

## <a name="view-qnas-with-context"></a>Afficher les paires question/réponse avec leur contexte
Lors de la création de la base de connaissances pour la [FAQ relative au stylet Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98), nous choisissons d’activer l’extraction multitour à partir du document source. Pour plus d’informations, consultez [Créer une invite multitour à partir d’un document](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure). Cela répertorie les invites multitours associées aux paires question/réponse, qui peuvent être affichées en utilisant **Afficher le contexte** sous **Afficher les options**.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran avec Afficher les options et Afficher le contexte dans des encadrés rouges]( ../media/guided-conversations/show-context.png) ]( ../media/guided-conversations/show-context.png#lightbox)

Ceci affiche l’arborescence de contexte dans laquelle toutes les invites de suivi associées à une paire question/réponse sont affichées : 

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de la page FAQ relative au stylet Surface avec des réponses aux questions courantes]( ../media/guided-conversations/source.png) ]( ../media/guided-conversations/source.png#lightbox)

## <a name="add-new-qna-pair-with-follow-up-prompts"></a>Ajouter une nouvelle paire question/réponse avec des invites de suivi

Pour aider l’utilisateur à résoudre les problèmes liés à son stylet Surface, nous ajoutons des invites de suivi :

1.  Ajoutez une nouvelle paire question/réponse avec deux invites de suivi.
2.  Ajoutez une invite de suivi à l’une des invites récemment ajoutées.

**Étape 1** : Ajoutez une nouvelle paire question/réponse avec deux invites de suivi **Vérifiez la compatibilité** et **Vérifiez les paramètres du stylet**. À l’aide de l’éditeur, nous ajoutons une nouvelle paire question/réponse avec une invite de suivi en cliquant sur **Ajouter une paire question/réponse**.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de l’interface utilisateur avec « Ajouter une paire question/réponse » mis en évidence dans un encadré rouge]( ../media/guided-conversations/add-pair.png) ]( ../media/guided-conversations/add-pair.png#lightbox)

Une nouvelle ligne dans **Éditorial** est créée, dans laquelle nous entrons la paire question/réponse comme indiqué ci-dessous :

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de l’interface utilisateur avec « Ajouter une invite de suivi » mis en évidence dans un encadré rouge]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

Nous ajoutons ensuite une invite de suivi à la paire question/réponse nouvellement créée en choisissant **Ajouter une invite de suivi**. En cliquant sur ce dernier, nous renseignons les détails de l’invite comme indiqué :

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran de l’interface utilisateur avec « Ajouter une invite de suivi » mis en évidence dans un encadré rouge]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

Nous proposons **Vérifiez la compatibilité** comme « Texte d’affichage » pour l’invite et essayons de la lier à une question/réponse. Puisqu’aucune paire question/réponse n’est disponible pour être liée à l’invite, lorsque nous recherchons « Vérifiez la compatibilité de votre stylet Surface », nous créons une nouvelle paire question/réponse en cliquant sur **Créer nouveau**. Une fois les modifications **enregistrées**, l’écran suivant s’affiche, où une nouvelle paire question/réponse pour l’invite de suivi peut être entrée comme indiqué ci-dessous :

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran d’une question et sa réponse à propos de la vérification de la compatibilité du stylet Surface]( ../media/guided-conversations/check-compatibility.png) ]( ../media/guided-conversations/check-compatibility.png#lightbox)

De même, nous ajoutons un autre invite **Vérifiez les paramètres du stylet** pour aider l’utilisateur à résoudre les problèmes liés à son stylet et y ajoutons une paire question/réponse.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran d’une question et sa réponse à propos de la vérification des paramètres du stylet Surface]( ../media/guided-conversations/check-pen-settings.png) ]( ../media/guided-conversations/check-pen-settings.png#lightbox)

**Étape 2** : Ajoutez une autre invite de suivi à l’invite qui vient d’être créée. Nous ajoutons maintenant « Remplacez la pointe du stylet » comme invite de suivi à l’invite « Vérifiez les paramètres du stylet » précédemment créée.

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran d’une question et sa réponse concernant la vérification des paramètres du stylet Surface avec un encadré rouge autour des informations relatives au remplacement de la pointe du stylet]( ../media/guided-conversations/replace-pen-tips.png) ]( ../media/guided-conversations/replace-pen-tips.png#lightbox)

Nous enregistrons enfin les modifications et testons ces invites dans le volet de test : 

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran du volet de test de QnA Maker]( ../media/guided-conversations/test-pane.png) ]( ../media/guided-conversations/test-pane.png#lightbox)

Pour une requête de l’utilisateur intitulée **Problèmes avec le stylet Surface**, le système renvoie une réponse et présente les nouvelles invites à l’utilisateur. L’utilisateur sélectionne ensuite l’une des invites, **Vérifiez les paramètres du stylet**, et la réponse associée est renvoyée à l’utilisateur avec une autre invite, **Remplacez la pointe du stylet**, qui, lorsqu’elle est sélectionnée, fournit plus d’informations à l’utilisateur. Par conséquent, le multitour est utilisé pour aider et guider l’utilisateur vers la réponse souhaitée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Enrichir votre base de connaissances avec l’apprentissage actif](active-learning.md)