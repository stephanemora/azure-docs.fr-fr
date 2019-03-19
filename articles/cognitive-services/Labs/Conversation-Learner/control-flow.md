---
title: Flux de contrôle de Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez le flux de contrôle de Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e0a0a88e249c0a032e5afaeea14b9b3cfcbdc319
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080658"
---
## <a name="control-flow"></a>Flux de contrôle

Ce document décrit le flux de contrôle de Conversation Learner (CL) tel qu’illustré par le schéma ci-dessous.

![](media/controlflow.PNG)

1. L’utilisateur entre un terme ou une phrase dans le bot, par exemple « what’s the weather in Seattle ? »
1. CL passe l’entrée d’utilisateur à un modèle d’apprentissage automatique qui extrait des entités.
   - Ce modèle est généré par Conversation Learner et hébergé par www.luis.ai.
1. Toutes les entités extraites, ainsi que le texte d’entrée d’utilisateur, sont passés à la méthode de rappel de détection d’entités dans le code du bot.
    - Ce code peut définir/effacer/manipuler des valeurs d’entités.
1. Le réseau neuronal de CL prend ensuite la sortie de l’extraction d’entités et l’entrée d’utilisateur, et attribue un score à toutes les actions définies dans le bot.
   - Dans cet exemple, l’action de probabilité la plus élevée consiste à fournir les prévisions météorologiques :

     ![](media/controlflow_forecast.PNG)

1. L’action sélectionnée, dans ce cas, nécessite un appel d’API pour extraire les prévisions météorologiques. 
1. Cette API, qui avait été inscrite à l’aide de la méthode CL.AddCallback, est alors appelée.  Le résultat de cette API est ensuite retourné à l’utilisateur sous forme de message, par exemple, « Sunny with a high of 67 ».
1. Un appel est ensuite effectué au réseau neuronal afin de déterminer l’action suivante en fonction de l’étape précédente.
1. Le réseau neuronal prédit alors l’ensemble suivant d’actions possibles, et l’action sélectionnée est présentée à l’utilisateur, (ici, « Anything else ? »).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment animer avec apprenant de Conversation](./how-to-teach-cl.md)
