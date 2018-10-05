---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: fcf3bf29e2cdf89bdc93c7ebac313e5d9a9c18f0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043992"
---
L’accès au point de terminaison de prédiction est fourni avec une clé de point de terminaison. Pour les besoins de ce guide de démarrage rapide, utilisez la clé de démarrage gratuite associée à votre compte LUIS. 
 
1. Connectez-vous à l’aide de votre compte LUIS. 

    [![Capture d’écran de la liste des applications LUIS (Language Understanding)](media/cognitive-services-luis/app-list.png "Capture d’écran de la liste des applications LUIS (Language Understanding)")](media/cognitive-services-luis/app-list.png)

2. Sélectionnez votre nom dans le menu en haut à droite, puis sélectionnez **Paramètres**.

    ![Accès au menu des paramètres utilisateur LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Copiez la valeur de la **Clé de création**. Vous l’utiliserez plus loin dans le guide de démarrage rapide. 

    [![Capture d’écran des paramètres utilisateur LUIS (Language Understanding)](media/cognitive-services-luis/get-user-authoring-key.png "Capture d’écran des paramètres utilisateur LUIS (Language Understanding)")](media/cognitive-services-luis/get-user-authoring-key.png)

    La clé de création autorise une quantité illimitée et gratuite de requêtes pour l’API de création, et jusqu’à 1000 requêtes pour l’API de point de terminaison de prédiction par mois pour toutes vos applications LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->