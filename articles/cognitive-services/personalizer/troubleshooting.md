---
title: Résolution des problèmes - Personalizer
titleSuffix: Azure Cognitive Services
description: Les réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer figurent dans cet article.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663683"
---
# <a name="personalizer-troubleshooting"></a>Résolution des problèmes de Personalizer

Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.

## <a name="learning-loop"></a>Boucle d’apprentissage

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>La boucle d’apprentissage ne semble pas apprendre. Comment la corriger ?

La boucle d’apprentissage a besoin de quelques milliers d’appels Reward avant que les appels Rank n’établissent des priorités efficaces. 

En cas de doute sur le comportement actuel de votre boucle d’apprentissage, exécutez une [évaluation hors ligne](concepts-offline-evaluation.md) et appliquez la stratégie d’apprentissage corrigée. 

## <a name="next-steps"></a>Étapes suivantes

[Configurer la fréquence de mise à jour du modèle](how-to-settings.md#model-update-frequency)