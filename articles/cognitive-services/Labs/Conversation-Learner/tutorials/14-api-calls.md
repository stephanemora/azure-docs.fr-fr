---
title: Utiliser des appels d’API avec un modèle d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des appels d'API avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f7c7c72703d7c3134dd2acdcc466fc0182fa38a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389936"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Comment ajouter des appels d'API à un modèle d’Apprenant de conversation

Ce tutoriel montre comment ajouter des appels d’API à votre modèle. Les appels d’API sont des fonctions que vous définissez et écrivez dans votre bot et que Conversation Learner peut appeler.

## <a name="video"></a>Vidéo

[![Aperçu de didacticiel d’appels d’API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot « tutorialAPICalls.ts » soit en cours d’exécution.

    npm run tutorial-api-calls

## <a name="details"></a>Détails

- Les appels d’API peuvent lire et manipuler des entités.
- Les appels d’API ont accès à l’objet de gestionnaire de mémoire.
- Les appels d’API peuvent accepter des arguments.

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans l’interface utilisateur web, cliquez sur « Import Tutorials » (Importer des tutoriels), puis sélectionnez le modèle nommé « Tutorial-14-APICalls ».

### <a name="entities"></a>Entités

Nous avons défini une entité appelée `number` dans le modèle.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Appels de l’API
Le code pour les appels de l’API est défini dans ce fichier : `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Le rappel `RandomGreeting` renvoie un message d’accueil aléatoire défini dans le tableau `greeting`.
- Le rappel `Multiply` multiplie deux nombres transmis par l’action qui l’appelle et retourne un résultat qui peut être rendu dans l’interface utilisateur.
    - Notez que le gestionnaire de mémoire est le premier argument. 
    - Notez que les rappels de l’API peuvent tirer plusieurs entrées, dans ce cas `num1string` et `num2string`.
- Le rappel `ClearEntities` efface l’entité Number afin que l’utilisateur puisse entrer un autre nombre. 
    - Cet exemple illustre la façon dont les appels de l’API peuvent manipuler des entités.

### <a name="actions"></a>Actions
Nous avons créé quatre actions. Trois d’entre elles sont des actions d’API « Non-Wait », la quatrième est une action « Text » qui pose à l’utilisateur une question similaire à celle utilisée dans d’autres didacticiels. Pour en savoir plus sur la création de chaque action, procédez comme suit :
1. Dans le volet gauche, cliquez sur « Actions », puis cliquez sur l’une des quatre actions répertoriées dans la grille.
2. Notez les valeurs de chaque champ du formulaire qui s’affiche.
3. Notez le bouton `Refresh` en regard du champ de l’API.
    - Si vous devez arrêter le bot et apporter des modifications aux API tandis que la page de l’interface utilisateur est opérationnelle, cliquez sur le bouton `Refresh` pour récupérer les dernières modifications.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, Multiply et RandomGreeting
Trois de ces actions sont de type API. Chacune d’elles s’appuie sur les fonctions de rappel d’API pour exécuter des tâches et éventuellement retourner une valeur qui sera présentée à l’utilisateur.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>« Quel numéro voulez-vous multiplier par 12 »
Il s’agit de l’action « Text ». Elle pose une question à l’utilisateur. Bien que cette action n’interagisse pas véritablement avec l’un des rappels d’API, elle invite l’utilisateur à répondre en entrant un nombre qui ira dans la mémoire d’une entité qui peut ensuite être utilisée par l’action « Multiply » qui en utilise les rappels de l’API.


### <a name="train-dialog"></a>Boîte de dialogue d’apprentissage

Nous allons étudier un « dialogue de formation ».

1. Dans le volet gauche, cliquez sur `Train Dialogs`, puis sur le bouton `New Train Dialog`.
2. Entrez « Bonjour ».
3. Cliquez sur le bouton `Score Actions`.
4. Sélectionnez `RandomGreeting`. 
    - Permet d’exécuter l’appel d’API de salutations aléatoire.
    - Il n’attend pas de réponse de l’utilisateur.
5. Sélectionnez `What number to do you want to multiply by 12?`.
6. Entrez n’importe quel nombre.
    - Notez que votre numéro a été automatiquement étiqueté en tant qu’entité `number`.
7. Cliquez sur le bouton `Score Actions`.
8. Sélectionnez l’action `Multiply`.
    - Notez le résultat de la multiplication par 12.
    - Notez que la mémoire contient toujours la valeur que vous avez entrée pour `number`
9. Sélectionnez l’action `ClearEntities`.
    - Notez que la valeur de l’entité pour `number` a été effacée de la mémoire.
10. Cliquez sur le bouton `Save`.

Vous savez maintenant comment inscrire des rappels d’API, leurs modèles courants, et comment y définir des arguments et y associer des valeurs et des entités.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cartes, 1ère partie](./15-cards.md)
