---
title: Résolution de problèmes
titleSuffix: Azure Cognitive Services
description: Les réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer figurent dans cet article.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: 5136bd295c12c4439a894b4dcf0b868d32ce43ca
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312932"
---
# <a name="personalizer-troubleshooting"></a>Résolution des problèmes de Personalizer

Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.

## <a name="learning-loop"></a>Boucle d’apprentissage

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>La boucle d’apprentissage ne semble pas apprendre. Comment la corriger ?

La boucle d’apprentissage a besoin de quelques milliers d’appels Reward avant que les appels Rank n’établissent des priorités efficaces. 

En cas de doute sur le comportement actuel de votre boucle d’apprentissage, exécutez une [évaluation hors ligne](concepts-offline-evaluation.md) et appliquez la stratégie d’apprentissage corrigée. 

## <a name="next-steps"></a>Étapes suivantes

[Configurer la fréquence de mise à jour du modèle](how-to-settings.md#model-update-frequency)