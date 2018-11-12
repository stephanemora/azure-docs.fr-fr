---
title: Modèle Conversation Learner de démonstration, lanceur d’applications de réalité virtuelle – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Conversation Learner de démonstration.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 31efcb2b2f05bd7a49701be708a89db8f2ef26f7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229695"
---
# <a name="demo-virtual-reality-app-launcher"></a>Démonstration : Lanceur d’application de réalité virtuelle

Cette démonstration illustre un lanceur d’applications de réalité virtuelle prenant en charge des commandes comme « lance Skype et mets-le sur le mur ». L’utilisateur doit indiquer le nom et l’emplacement de l’application pour lancer l’application. Le lancement du modèle est géré par un appel d’API. Lorsqu’un nom d’application est reconnu par l’utilisateur, entityDetectionCallback vérifie si l’application demandée correspond à une ou plusieurs applications dans la liste des applications installées. Il gère le cas où l’application demandée ne serait pas installée et si le nom de l’application est ambigu (correspond à plusieurs applications installées).

## <a name="video"></a>Vidéo

[![Aperçu de démonstration Application de réalité virtuelle](https://aka.ms/cl-demo-vrapp-preview)](https://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Configuration requise

Pour les besoins de ce tutoriel, le bot VRAppLauncher doit être en cours d’exécution :

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur VRAppLauncher. 

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
    - LUIS le reconnaît comme étant une entité.
5. Cliquez sur Score Actions (Actions de score).
3. Cliquez pour sélectionner « où voulez-vous la placer ? »
4. Entrez « sur le mur ».
    - LUIS le reconnaît comme étant un PlacementLocation.
2. Entrez Score Actions (Attribuer un score aux actions).
6. Sélectionnez « LaunchApp »
7. Système : « démarrage d’outlook sur le mur ».
    - Cela a déclenché un appel d’API. Le code de cet appel est à C:\<\installedpath>\src\demos\demoVRAppLauncher.ts. Toutefois, il ne contient pas réellement le code de lancement d’Outlook dans cette démonstration.
    - Il efface les entités AppName et PlacementLocation. Il renvoie la chaîne ci-dessus en tant que réponse.
4. Cliquez sur Apprentissage terminé.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Ouvrons une autre session d’apprentissage pour la gestion des entités inconnues et ambigües.

1. Cliquez sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « démarrer OneNote ». 
    - Le modèle reconnaît OneNote comme étant un nom d’application. La fonction `EntityDetectionCallback` définie dans le code traduit le nom entré par l’utilisateur en un nom d’application par correspondance avec la liste d’applications définie dans le code. Cela retourne ensuite l’ensemble de toutes les applications correspondantes. 
    - Si la liste des correspondances est de zéro, cela signifie que l’application n’est pas installée. Elle est placée dans unknownAppName.
    - Si elle trouve plusieurs applications, elle les copie dans `DisambigAppNames` et efface l’entité AppName.
2. Cliquez sur Score Action (Action de score).
3. Cliquez pour sélectionner « Désolé, je ne connais pas l’application $UknownAppName. »
4. Entrez « lancer Amazon ». Nous allons essayer l’autre chemin d’accès.
5. Cliquez sur Score Actions (Actions de score).
    - Amazon Video et Amazon Music se trouvent maintenant dans la mémoire `DisambigAppNames`, et OneNote a été effacé.
3. Cliquez pour sélectionner « Il existe quelques applications qui ressemblent à ... »
    - Le score n’est pas très élevé, car nous n’avons à ce stade défini que quelques dialogues d’apprentissage. S’il y en avait d’autres, le modèle serait plus catégorique.
2. Entrez Score Actions (Attribuer un score aux actions).
4. Cliquez sur Apprentissage terminé.

Vous savez maintenant comment effectuer la résolution d’entité. La démonstration a également illustré les rappels d’API et montré un modèle permettant de collecter des informations, de vérifier la présence et l’ambiguïté et de prendre en fonction la mesure qui s’impose.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement d’un bot Conversation Learner](../deploy-to-bf.md)
