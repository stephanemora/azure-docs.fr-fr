---
title: Utiliser des appels d’API avec un modèle d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des appels d'API avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 815d1e9f6d1e4b9937647d55b67665e1b27f501e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240764"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Comment ajouter des appels d'API à un modèle d’Apprenant de conversation

Ce tutoriel montre comment ajouter des appels d’API à votre modèle. Les appels d’API sont des fonctions que vous définissez et écrivez dans votre bot et que l’Apprenant de conversation peut appeler.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 12](https://aka.ms/cl-tutorial-12-preview)](https://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot « tutorialAPICalls.ts » soit en cours d’exécution.

    npm run tutorial-api-calls

## <a name="details"></a>Détails

- Les appels d’API peuvent lire et manipuler des entités.
- Les appels d’API ont accès à l’objet de gestionnaire de mémoire.
- Les appels d’API peuvent également prendre des arguments : cela permet de réutiliser le même appel d’API pour des finalités différentes.

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur Tutorial-12-APICalls. 

### <a name="entities"></a>Entités

Nous avons défini une entité appelée Nombre dans le modèle.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Appels de l’API
Le code pour les appels d’API est défini dans ce fichier : C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Le premier rappel d’API est RandomGreeting. Il renvoie des salutations aléatoires définies dans la variable de salutations.
- Le rappel d’API Multiplier : il multipliera deux nombres fournis par l’utilisateur. Il retourne alors le résultat de la multiplication des deux nombres. Cela indique que les rappels de l’API peuvent prendre des entrées. Notez que le gestionnaire de mémoire est le premier argument. 
- Le rappel d’API ClearEntities : efface l’entité de nombre pour permettre à l’utilisateur d’entrer le nombre suivant. Ceci montre comment des appels d’API peuvent manipuler des entités.

### <a name="actions"></a>Actions

Nous avons créé quatre actions. 

![](../media/tutorial12_actions.PNG)

- En plus de « Quel nombre souhaitez-vous multiplier par 12 ? », qui est une action de communication, il existe trois appels d’API différents qui illustrent les modèles d’appel d’API classiques.

- RandomGreeting : est une action sans attente. Pour effectuer cette installation, dans la boîte de dialogue Créer une action, nous avons sélectionné le type d’action API_LOCAL, puis RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Le bouton Actualiser en regard de l’API est utilisé si nous devons arrêter le robot et apporter des modifications aux API. En cliquant sur Actualiser, vous choisissez les dernières modifications.

Voici comment nous avons créé l’action Multiplier : après avoir sélectionné API et API_Local, nous avons entré une entité ($number) pour la première valeur entrée (num1string) et une valeur (12) pour la deuxième valeur entrée (num2string). Cela fournit un niveau d’indirection entre le bot et les appels d’API, ce qui permet de mapper le même rappel dans quelques actions du système, qui diffèrent en fonction de la manière dont les actions sont assignées.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Boîte de dialogue d’apprentissage

Examinons une boîte de dialogue d’apprentissage.

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « Bonjour ».
2. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner RandomGreeting. Permet d’exécuter l’appel d’API de salutations aléatoire.
3. Cliquez pour sélectionner « Quel nombre souhaitez-vous multiplier par 12 ? »
4. Entrez « 8 ». Cliquez ensuite sur Attribuer un score aux actions.
4. Sélectionnez « Multiplier $number 12 ». Notez le résultat de la multiplication.
5. Sélectionnez « Effacer entités ».
    - La valeur de l’entité `number` a été effacée.
3. Cliquez pour sélectionner « Quel nombre souhaitez-vous multiplier par 12 ? »
4. Cliquez sur Tests terminés.

![](../media/tutorial12_dialog.PNG)

Vous savez maintenant comment inscrire des rappels d’API, leurs modèles courants, et comment y définir des arguments et y associer des valeurs et des entités.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cartes, 1ère partie](./13-cards-1.md)
