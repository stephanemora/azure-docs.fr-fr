---
title: Guide pratique pour utiliser le rappel de détection d’entités avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser le rappel de détection d’entités avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f168018a23d03ffb957da2dd1f67881420a21208
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171101"
---
# <a name="how-to-use-entity-detection-callback"></a>Comment utiliser le rappel de détection d’entités

Ce tutoriel montre le rappel de détection d’entités et illustre un modèle courant pour résoudre des entités.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel 10](http://aka.ms/cl-tutorial-10-preview)](http://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Configuration requise
Pour les besoins de ce tutoriel, le bot `tutorialEntityDetectionCallback` doit être en cours d’exécution.

    npm run tutorial-entity-detection

## <a name="details"></a>Détails
Le rappel de détection d’entité permet d’utiliser du code personnalisé pour gérer des règles d’entreprise concernant des entités. Cette démonstration utilise des rappels et des entités programmatiques pour traduire le nom de ville entré par l’utilisateur en un nom canonique, par exemple, « Big Apple » en « New York ».

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles, cliquez sur Tutorial-10-EntityDetectionCallback. 

### <a name="entities"></a>Entités

Trois entités sont définies dans le modèle.

![](../media/tutorial10_entities.PNG)

1. La ville est une entité personnalisée fournie par l’utilisateur en entrée textuelle.
2. CityUnknown est une entité de programmation. Elle sera remplie par le système. Il copiera l’entrée de l’utilisateur si le système ne sait pas de quelle ville il s’agit.
3. CityResolved est la ville que le système connaît. Ce sera le nom canonique de la ville ; par exemple, « Big Apple » sera traduit en « New York ».

### <a name="actions"></a>Actions

Trois actions sont définies dans le modèle.

![](../media/tutorial10_actions.PNG)

1. La première action est « Quelle ville souhaitez-vous ? »
2. La deuxième est « Je ne connais pas cette ville, $CityUknown. Quelle ville souhaitez-vous ? »
3. La troisième est « Vous avez dit $City, et j’ai résolu avec $CityResolved. »

### <a name="callback-code"></a>Code de rappel

Examinons le code. Vous pouvez trouver la méthode EntityDetectionCallback dans le fichier C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial10_callbackcode.PNG)

Cette fonction est appelée après la résolution d’entité.
 
- La première chose qu’elle fera est de supprimer $CityUknown. $CityUknown persiste uniquement une fois, puisqu’elle est toujours supprimée au début.
- Ensuite, elle récupère la liste des villes qui ont été reconnues. Prenez la première et essayez de la résoudre.
- La fonction qui résout (resolveCity) est définie plus haut dans le code. Elle contient une liste de noms de villes canoniques. Si elle trouve le nom de ville dans la liste, elle le retourne. Sinon, elle recherche dans 'cityMap' et renvoie le nom mappé. Si elle ne trouve pas de ville, elle retourne la valeur null.
- Enfin, si la ville est résolu en un nom, nous le stockons dans l’entité $CityKnown. Sinon, supprimez ce que l’utilisateur a dit et remplissez l’entité $CityUknown.

### <a name="train-dialogs"></a>Boîtes de dialogue d’apprentissage

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville souhaitez-vous ? »
2. Entrez « new york ».
    - Le texte est reconnu comme étant une entité de ville.
5. Cliquez sur Attribuer un score aux actions
    - `City` et `CityResolved` ont été remplis.
6. Sélectionnez « Vous avez dit $City, et j’ai résolu avec $CityResolved ».
7. Cliquez sur Apprentissage terminé.

Ajoutez un autre exemple de dialogue :

1. Cliquez sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville souhaitez-vous ? »
2. Entrez « big apple ».
    - Le texte est reconnu comme étant une entité de ville.
5. Cliquez sur Attribuer un score aux actions
    - `CityResolved` montre l’effet du code en cours d’exécution.
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
    - `CityUknown` a été effacé, et `CityResolved` est rempli.
6. Sélectionnez « Vous avez dit $City, et j’ai résolu avec $CityResolved ».
7. Cliquez sur Apprentissage terminé.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rappels de session](./11-session-callbacks.md)
