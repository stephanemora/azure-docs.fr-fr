---
title: Comment utiliser le rappel de détection d’entités avec une application d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser le rappel de détection d’entités avec une application d’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369601"
---
# <a name="how-to-use-entity-detection-callback"></a>Comment utiliser le rappel de détection d’entités

Ce tutoriel montre le rappel de détection d’entités et illustre un modèle courant pour résoudre des entités.

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot « tutorialEntityDetectionCallback » soit en cours d’exécution.

    npm run tutorial-entity-detection

## <a name="details"></a>Détails
Le rappel de détection d’entité permet d’utiliser du code personnalisé pour gérer des règles d’entreprise concernant des entités. Dans cette démonstration, nous allons utiliser des rappels et des entités par programmation pour résoudre le nom de ville entré par l’utilisateur à un nom canonique, par exemple, résolution de « la grosse pomme » à « new york ».

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des applications, cliquez sur Tutorial-10-EntityDetenctionCallback. 

### <a name="entities"></a>Entités

Nous avons défini trois entités dans l’application.

![](../media/tutorial10_entities.PNG)

1. La ville est une entité personnalisée que l’utilisateur fournit sous forme d’entrée de texte.
2. CityUnknown est une entité de programmation. Cela sera rempli par le système. Il copiera l’entrée de l’utilisateur si le système ne sait pas de quelle ville il s’agit.
3. CityResolved est la ville que le système connaît. Ce sera le nom canonique de la ville par exemple « grosse pomme » sera converti en « new york ».

### <a name="actions"></a>Actions

Nous avons créé trois actions. 

![](../media/tutorial10_actions.PNG)

1. La première action est « Quelle ville souhaitez-vous ? »
2. La deuxième est « Je ne connais pas cette ville, $CityUknown. Quelle ville souhaitez-vous ? »
3. La troisième est « Vous avez dit $City, et j’ai résolu avec $CityResolved. »

### <a name="callback-code"></a>Code de rappel

Examinons le code. Vous pouvez trouver la méthode EntityDetectionCallback dans le fichier C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial10_callbackcode.PNG)

Cette fonction est appelée après la résolution d’entité.
 
- La première chose qu’elle fera est de supprimer $CityUknown. $CityUknown persiste uniquement une fois, puisqu’elle est toujours supprimée au début.
- Ensuite, nous obtenons la liste des villes qui ont été reconnues. Prenez la première et essayez de la résoudre.
- La fonction qui résout (resolveCity) est définie plus haut dans le code. Elle contient une liste de noms de villes canoniques. Si elle trouve le nom de ville dans la liste, elle le retourne. Sinon, elle recherche dans 'cityMap' et renvoie le nom mappé. Si elle ne trouve pas de ville, elle retourne la valeur null.
- Enfin, si la ville est résolu en un nom, nous le stockons dans l’entité $CityKnown. Sinon, supprimez ce que l’utilisateur a dit et remplissez l’entité $CityUknown.

### <a name="train-dialogs"></a>Boîtes de dialogue d’apprentissage

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville souhaitez-vous ? »
2. Entrez « new york ».
    - Notez qu’elle est reconnue comme entité de ville.
5. Cliquez sur Attribuer un score aux actions
    - Notez que la ville et CityResolved ont été renseignées.
6. Sélectionnez « Vous avez dit $City, et j’ai résolu avec $CityResolved ».
7. Cliquez sur Apprentissage terminé.

Ajoutez un autre exemple de dialogue :

1. Cliquez sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville souhaitez-vous ? »
2. Entrez « big apple ».
    - Notez qu’elle est reconnue comme entité de ville.
5. Cliquez sur Attribuer un score aux actions
    - Notez que CityResolved montre l’effet du code en cours d’exécution.
6. Sélectionnez « Vous avez dit $City, et j’ai résolu avec $CityResolved ».
7. Cliquez sur Apprentissage terminé.

Ajoutez un autre exemple de dialogue :

1. Cliquez sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville souhaitez-vous ? »
2. Entrez « foo ».
    - C’est un exemple d’une ville que le système ne connaît pas. 
5. Cliquez sur Attribuer un score aux actions
6. Sélectionnez « Je ne connais pas cette ville, $CityUknown. Quelle ville souhaitez-vous ? ».
7. Entrez « new york ».
8. Cliquez sur Attribuer un score aux actions.
    - Notez que CityUknown a été supprimé et que CityResolved est rempli.
6. Sélectionnez « Vous avez dit $City, et j’ai résolu avec $CityResolved ».
7. Cliquez sur Apprentissage terminé.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rappels de session](./11-session-callbacks.md)
