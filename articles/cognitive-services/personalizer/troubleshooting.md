---
title: Résolution des problèmes - Personalizer
titleSuffix: Azure Cognitive Services
description: Les réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer figurent dans cet article.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955222"
---
# <a name="personalizer-troubleshooting"></a>Résolution des problèmes de Personalizer

Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.

## <a name="learning-loop"></a>Boucle d’apprentissage

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>La boucle d’apprentissage ne semble pas apprendre. Comment la corriger ?

La boucle d’apprentissage a besoin de quelques milliers d’appels Reward avant que les appels Rank n’établissent des priorités efficaces. 

En cas de doute sur le comportement actuel de votre boucle d’apprentissage, exécutez une [évaluation hors ligne](concepts-offline-evaluation.md) et appliquez la stratégie d’apprentissage corrigée. 

## <a name="next-steps"></a>Étapes suivantes

[Configurer la fréquence de mise à jour du modèle](how-to-settings.md#model-update-frequency)