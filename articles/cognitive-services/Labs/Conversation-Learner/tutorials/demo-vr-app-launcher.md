---
title: Application Conversation Learner de démonstration, lanceur d’application de réalité virtuelle - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer une application Conversation Learner de démonstration.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369636"
---
# <a name="demo-virtual-reality-app-launcher"></a>Démonstration : Lanceur d’application de réalité virtuelle

Cette démonstration illustre un lanceur d’application de réalité virtuelle prenant en charge des commandes telles que « démarrer Skype et le placer sur le mur ». L’utilisateur doit indiquer le nom et l’emplacement de l’application pour lancer l’application. Le lancement d’application est géré par un appel d’API. Lorsqu’un nom d’application est reconnu par l’utilisateur, entityDetectionCallback vérifie si l’application demandée correspond à une ou plusieurs applications dans la liste des applications installées. Il gère le cas où l’application demandée ne serait pas installée et si le nom de l’application est ambigu (correspond à plusieurs applications installées).

## <a name="requirements"></a>Configuration requise

Ce didacticiel requiert que le bot VRAppLauncher soit en cours d’exécution

    npm run demo-vrapp

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des applications de l’interface utilisateur web, cliquez sur VRAppLauncher. 

## <a name="entities"></a>Entités

Nous avons créé quatre entités :

- AppName : par exemple Skype
- PlacementLocation : par exemple mur
- UnknownAppName : entité de programmation définie par le système lorsqu’il ne reconnaît pas un nom d’entité énoncé par l’utilisateur, par exemple, parce qu’elle n’a pas été installée.
- DisAmbigAppNames : tableau de deux noms d’applications installées ou plus qui correspondent à ce que l’utilisateur a énoncé. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Actions

Nous avons créé un ensemble d’actions qui inclut une API appelée LaunchApp, qui démarrera l’appel de fonction pour lancer une application.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Dialogues d’apprentissage
Nous avons défini un certain nombre de dialogues d’apprentissage.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Par exemple, nous allons tester une session d’apprentissage.

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « Bonjour ».
2. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner « quelle application voulez-vous démarrer ? »
4. Entrez « outlook ».
    - Notez que LUIS le reconnaît comme une entité.
5. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner « où voulez-vous la placer ? »
4. Entrez « sur le mur ».
    - Notez que LUIS le reconnaît comme PlacementLocation.
2. Entrez Score Actions (Attribuer un score aux actions).
6. Sélectionnez « LaunchApp »
7. Système : « démarrage d’outlook sur le mur ».
    - Notez que cela a déclenché un appel d’API. Le code de cet appel est à C:\<\installedpath>\src\demos\demoVRAppLauncher.ts. Toutefois, il n’inclut pas réellement la logique de lancement d’Outlook pour cette démonstration.
    - Il efface les entités AppName et PlacementLocation. Il renvoie la chaîne ci-dessus en tant que réponse.
4. Cliquez sur Apprentissage terminé.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Ouvrons une autre session d’apprentissage pour la gestion des entités inconnues et ambigües.

1. Cliquez sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « démarrer OneNote ». 
    - Ceci est reconnu comme un nom d’application car EntityDetectionCallback défini dans le code utilise le nom que l’utilisateur a entré et le résout en un nom d’application en le comparant à la liste des applications définie dans le code. Cela retourne ensuite l’ensemble de toutes les applications correspondantes. 
    - Si la liste des correspondances est de zéro, cela signifie que l’application n’est pas installée. Elle est placée dans unknownAppName.
    - Si plusieurs applications sont trouvées, elles seront copiées dans DisambigAppNames et l’entité AppName sera effacée.
2. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner « Désolé, je ne connais pas l’application $UknownAppName. »
4. Entrer « démarrer amazon ». Nous allons essayer l’autre chemin d’accès.
5. Cliquez sur Attribuer un score aux actions.
    - Notez qu’Amazon Video et Amazon Music sont désormais dans la mémoire DisambigAppNames. Et que OneNote a été effacé.
3. Cliquez pour sélectionner « Il existe quelques applications qui ressemblent à ... »
    - Notez que le score n’est pas très élevé, car nous n’avons quelques boîtes de dialogue d’apprentissage à ce stade. Vous en auriez besoin d’autres pour que le modèle soit plus déterminant.
2. Entrez Score Actions (Attribuer un score aux actions).
4. Cliquez sur Apprentissage terminé.

Vous savez maintenant comment effectuer la résolution d’entité. La démonstration a également illustré les rappels d’API et montré un modèle de collecte d’informations, de vérification de la présence et de l’ambiguïté et d’exécution de l’action appropriée sur cette base.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement d’un bot Conversation Learner](../deploy-to-bf.md)
