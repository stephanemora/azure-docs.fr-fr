---
title: 'Démarrage rapide : Reconnaître la voix, les intentions et les entités, Python – Service Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660488"
---
Pour terminer le démarrage rapide de reconnaissance de l’intention, vous devez créer un compte LUIS et un projet à partir de la préversion du portail LUIS. Ce démarrage rapide nécessite uniquement un abonnement LUIS. Il n’est pas nécessaire de disposer d’un abonnement au service Speech.

La première chose à faire est de créer un compte et une application LUIS à partir de la préversion du portail LUIS. L’application LUIS que vous créez utilisera un domaine prédéfini pour la domotique, qui fournit des intentions, des entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS s’exécutant dans le cloud et que vous pourrez appeler à l’aide du kit SDK Speech. 

Pour créer votre application LUIS, suivez ces instructions : 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Démarrage rapide : Créer une application de domaine prédéfini</a>

Quand vous aurez terminé, vous aurez besoin de trois éléments : 

* Votre clé LUIS
* Votre région LUIS
* Votre ID d’application LUIS

Voici comment vous pouvez trouver ces informations sur la [préversion du portail LUI](https://preview.luis.ai/) :

1. À partir de la préversion du portail LUIS, sélectionnez **Manage** (Gérer), puis sélectionnez **Azure Resources** (Ressources Azure). Dans cette page, vous trouverez votre clé et votre localisation LUIS (parfois appelée _région_).  

   > [!div class="mx-imgBorder"]
   > ![Clé et localisation LUIS](../../../media/luis/luis-key-region.png)

2. Après vous être procuré la clé et la localisation, vous aurez besoin de l’ID d’application. Sélectionnez **Application Settings** (Paramètres d’application) – votre ID d’application est disponible dans cette page.

   > [!div class="mx-imgBorder"]
   > ![ID d’application LUIS](../../../media/luis/luis-app-id.png)