---
title: Présentation de l’API Émotion | Microsoft Docs
description: Utilisez l’algorithme de reconnaissance d’émotion cloud avancé de Microsoft pour générer des applications plus personnalisées avec l’API Émotion de Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368629"
---
# <a name="what-is-emotion-api"></a>Qu’est-ce que l’API Émotion ?

> [!IMPORTANT]
> L’API Émotion est dépréciée depuis le 30 octobre 2017. La fonctionnalité fait désormais partie de l’[API Visage](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Bienvenue dans l’API Émotion de Microsoft, qui vous permet de personnaliser encore davantage vos applications avec un algorithme de reconnaissance d’émotion cloud de Microsoft.

### <a name="emotion-recognition"></a>Reconnaissance des émotions

La version bêta de l’API Émotion prend une image comme entrée et retourne un seuil de confiance par rapport à un ensemble d’émotions de chaque visage de l’image, ainsi qu’un cadre entourant le visage provenant de l’API Visage. Les émotions détectées sont le bonheur, la tristesse, la surprise, la colère, la peur, le mépris, le dégoût et la neutralité. Ces émotions sont exprimées partout dans le monde par les mêmes expressions simples du visage, où elles sont identifiées par l’API Émotion. 

**Interprétation des résultats :** 

Lors de l’interprétation des résultats à partir de l’API Émotion, l’émotion détectée doit être interprétée comme l’émotion ayant le score le plus élevé, les scores étant normalisés de façon à atteindre une somme égale à un. Les utilisateurs peuvent choisir de définir un seuil de confiance plus élevé dans leur application, en fonction de leurs besoins. 

Pour plus d’informations sur la détection d’émotions, consultez les informations de référence sur l’API : 
  * De base : si l’utilisateur a déjà appelé l’API Visage, il peut soumettre le rectangle entourant le visage comme entrée et utiliser le niveau de base. [Référence sur l’API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard : si l’utilisateur ne soumet pas de rectangle, il doit utiliser le mode standard.  [Référence sur l’API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Pour obtenir un exemple illustrant comment interpréter une vidéo en streaming avec l’API Émotion, consultez [Guide pratique pour analyser des vidéos en temps réel](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
