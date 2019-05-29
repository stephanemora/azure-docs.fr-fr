---
title: Exploration – Personalizer
titleSuffix: Azure Cognitive Services
description: Avec une exploration, le service Personalizer est en mesure de continuer à fournir de bons résultats, même si le comportement de l’utilisateur change. Le choix d’un paramètre d’exploration est une décision opérationnelle concernant la proportion d’interactions d’utilisateur nécessaires pour explorer, afin d’améliorer le modèle.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: 0e11dd962f4cc1ff9b1938b309a1b405dbf20232
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606991"
---
# <a name="exploration-and-exploitation"></a>Exploration et exploitation

Avec une exploration, le service Personalizer est en mesure de continuer à fournir de bons résultats, même si le comportement de l’utilisateur change.

Quand le service Personalizer reçoit un appel de classement, il retourne un ID RewardActionID basé sur l’une des approches suivantes :
* une exploitation pour faire correspondre le comportement d’utilisateur le plus probable compte tenu du modèle Machine Learning actif ;
* une exploration ne correspondant pas à l’action la plus probable dans le classement.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Le service Personalizer utilise actuellement un algorithme nommé *epsilon greedy* pour explorer. 

## <a name="choosing-an-exploration-setting"></a>Choix d’un paramètre d’exploration

Vous configurez le pourcentage de trafic à utiliser pour l’exploration dans la page **Paramètres** du portail Azure pour le service Personalizer. Ce paramètre détermine le pourcentage d’appels de classement qui effectuent l’exploration. 

Le service Personalizer détermine s’il faut explorer ou exploiter avec cette probabilité sur chaque appel de classement. Cela diffère du comportement de certaines infrastructures A/B qui verrouillent un traitement sur des ID utilisateur spécifiques.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Meilleures pratiques pour le choix d’un paramètre d’exploration

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

Le choix d’un paramètre d’exploration est une décision opérationnelle concernant la proportion d’interactions d’utilisateur nécessaires pour explorer, afin d’améliorer le modèle. 

Une valeur égale à zéro annule de nombreux avantages du servie Personalizer. Avec ce paramètre, le service Personalizer n’utilise aucune interaction d’utilisateur pour découvrir de meilleures interactions d’utilisateur. Cela conduit à une stagnation, à une dérive et finalement à une chute des performances du modèle.

Une valeur trop élevée pour le paramètre annule les bénéfices de l’apprentissage du comportement de l’utilisateur. La valeur 100 % implique une randomisation de constante, de sorte qu’aucun comportement appris des utilisateurs n’a d’influence sur le résultat.

Il est important de ne pas modifier le comportement de l’application selon que le service Personalizer explore ou exploite. Cela conduirait à des biais d’apprentissage qui finiraient par réduire les performances potentielles.

## <a name="next-steps"></a>Étapes suivantes

[Apprentissage par renforcement](concepts-reinforcement-learning.md) 