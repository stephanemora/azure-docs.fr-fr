---
title: Inspecter la qualité des données pour la reconnaissance vocale personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Custom Speech fournit des outils qui vous permettent d’inspecter visuellement la qualité de la reconnaissance d’un modèle en comparant les données audio avec le résultat de reconnaissance correspondant. À partir du portail de la reconnaissance vocale personnalisé, vous pouvez lire son chargé et déterminer si le résultat de la reconnaissance fourni est correct.  Cet outil vous permet d’inspecter rapidement les qualité du modèle de reconnaissance vocale de Microsoft de référence ou un modèle formé personnalisé sans avoir à transcrire toutes les données audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025817"
---
# <a name="inspect-custom-speech-data"></a>Inspecter les données de la reconnaissance vocale personnalisé

> [!NOTE]
> Cette page suppose que vous avez lu [préparer les données de test pour la reconnaissance vocale personnalisé](how-to-custom-speech-test-data.md) et avez chargé un jeu de données pour l’inspection.

Custom Speech fournit des outils qui vous permettent d’inspecter visuellement la qualité de la reconnaissance d’un modèle en comparant les données audio avec le résultat de reconnaissance correspondant. À partir du portail de la reconnaissance vocale personnalisé, vous pouvez lire son chargé et déterminer si le résultat de la reconnaissance fourni est correct. Cet outil vous permet d’inspecter rapidement les qualité du modèle de reconnaissance vocale de Microsoft de référence ou un modèle formé personnalisé sans avoir à transcrire toutes les données audio.

Dans ce document, vous allez apprendre à inspecter visuellement la qualité d’un modèle avec les données d’apprentissage que vous avez téléchargé précédemment.

Dans cette page, vous allez apprendre à inspecter visuellement la qualité du modèle de reconnaissance vocale de Microsoft de référence et/ou un modèle personnalisé que vous avez formé. Vous utiliserez les données que vous avez téléchargé à le **données** onglet pour le test.

## <a name="create-a-test"></a>Créer un test

Suivez ces instructions pour créer un test :

1. Accédez à **parole-texte > vocal personnalisé > test**.
2. Cliquez sur **ajouter Test**.
3. Sélectionnez **Inspecter qualité (données Audio-only)**. Nommez le test, description, puis sélectionnez votre jeu de données audio.
4. Sélectionnez jusqu'à deux modèles que vous souhaitez tester.
5. Cliquez sur **Créer**.

Une fois un test a été créé avec succès, vous pouvez comparer les modèles côte à côte.

## <a name="side-by-side-model-comparisons"></a>Comparaisons côte à côte

Lorsque l’état de test est *Succeeded*, cliquez dans le nom d’élément de test pour afficher les détails du test. Cette page de détails répertorie tous les énoncés dans votre jeu de données, en indiquant les résultats de reconnaissance des deux modèles en même temps que la transcription du jeu de données soumis.

Pour aider à inspecter la comparaison côte à côte, vous pouvez basculer les divers types d’erreurs, y compris d’insertion, suppression et la substitution. En écoutant l’audio et de comparaison des résultats de reconnaissance dans chaque colonne (montrant la transcription étiquetés humaines et les résultats des deux modèles de reconnaissance vocale), vous pouvez décider quel modèle répond à vos besoins et où les améliorations sont nécessaires.

Inspection des tests de qualité est utile pour vérifier si la qualité d’un point de terminaison de reconnaissance vocale est suffisant pour une application.  Objectif de mesure de précision, nécessitant retranscrits audio, suivez les instructions figurant en cours de test : Évaluer la précision.

## <a name="next-steps"></a>Étapes suivantes

* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)
* [Formation d’un modèle](how-to-custom-speech-train-model.md)
* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer les données de test pour la reconnaissance vocale personnalisé](how-to-custom-speech-test-data.md)
