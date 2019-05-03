---
title: Évaluer la précision pour la reconnaissance vocale personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Dans ce document, vous allez apprendre à quantitative mesurer la qualité du modèle de reconnaissance vocale de Microsoft ou votre modèle personnalisé. Données de transcription audio + étiquetés humaines sont requises pour tester la précision et 30 minutes à 5 heures représentatif audio doit être fournies.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025908"
---
# <a name="evaluate-custom-speech-accuracy"></a>Évaluer la précision de la reconnaissance vocale personnalisé

Dans ce document, vous allez apprendre à quantitative mesurer la qualité du modèle de reconnaissance vocale de Microsoft ou votre modèle personnalisé. Données de transcription audio + étiquetés humaines sont requises pour tester la précision et 30 minutes à 5 heures représentatif audio doit être fournies.

## <a name="what-is-word-error-rate-wer"></a>Quel est le mot taux d’erreurs ?

Le standard pour mesurer la précision du modèle est *taux d’erreur Word* (WER). Rapport d’erreurs Windows compte le nombre de mots incorrects identifié lors de la reconnaissance, puis divise par le nombre total de mots fournis dans la transcription étiquetés humaines. Enfin, ce nombre est multiplié par 100 % pour calculer le rapport d’erreurs Windows.

![Formule de rapport d’erreurs Windows](./media/custom-speech/custom-speech-wer-formula.png)

Correctement identifiée automne de mots en trois catégories :

* Insertion (I) : Mots qui sont ajoutés de manière incorrecte dans la transcription d’hypothèse
* Suppression (D) : Mots qui sont détectées dans la transcription d’hypothèse
* Substitution (S) : Mots qui ont été substituées entre référence et d’hypothèse

Voici un exemple :

![Exemple de mots identifiés de manière incorrecte](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Résoudre les erreurs et d’améliorer le rapport d’erreurs Windows

Vous pouvez utiliser le rapport d’erreurs Windows à partir des résultats de la reconnaissance de machine pour évaluer la qualité du modèle que vous utilisez avec votre application, un outil ou un produit. Un rapport d’erreurs Windows de 5 à 10 % est considéré comme étant de bonne qualité et est prêt à utiliser. Un rapport d’erreurs Windows de 20 % est acceptable, cependant, vous souhaiterez une formation supplémentaire. Un rapport d’erreurs Windows d’au moins 30 % signale la qualité médiocre et nécessite une personnalisation et formation.

Il est importante de façon dont les erreurs sont distribuées. Lorsque de nombreuses erreurs de suppression sont rencontrées, il est généralement en raison de la puissance du signal audio faible. Pour résoudre ce problème, vous devez collecter des données audio de plus près à la source. Erreurs d’insertion signifient que l’audio a été enregistré dans un environnement bruyant et DIAPHONIE pouvant être présente, ce qui entraîne des problèmes de reconnaissance. Erreurs de substitution sont souvent rencontrés quand un exemple insuffisant des termes de spécifique à un domaine a été fourni sous la forme transcriptions étiquetés humaines ou lié texte.

En analysant les fichiers individuels, vous pouvez déterminer quel type d’erreurs existent, et les erreurs sont uniques à un fichier spécifique. Comprendre les problèmes au niveau du fichier vous permettra de cibler des améliorations.

## <a name="create-a-test"></a>Créer un test

Si vous souhaitez tester la qualité du modèle de ligne de base de la reconnaissance vocale de Microsoft ou un modèle personnalisé que vous avez formé, vous pouvez comparer deux modèles côte à côte pour évaluer la précision. La comparaison inclut les résultats de rapport d’erreurs Windows et de reconnaissance. En règle générale, un modèle personnalisé est comparé avec le modèle de ligne de base de Microsoft.

Pour évaluer des modèles côte à côte :

1. Accédez à **parole-texte > vocal personnalisé > test**.
2. Cliquez sur **ajouter Test**.
3. Sélectionnez **évaluer la précision**. Nommez le test, description et sélectionnez votre jeu de données de transcription audio + étiquetés humaines.
4. Sélectionnez jusqu'à deux modèles que vous souhaitez tester.
5. Cliquez sur **Créer**.

Une fois que votre test a été créé avec succès, vous pouvez comparer les résultats côte à côte.

## <a name="side-by-side-comparison"></a>Comparaison de côte à côte

Une fois le test terminé, indiqué par le changement d’état à *Succeeded*, vous trouverez un certain nombre de rapport d’erreurs Windows pour les deux modèles inclus dans votre test. Cliquez sur le nom de test pour afficher la page de détails de test. Cette page de détails répertorie tous les énoncés dans votre jeu de données, en indiquant les résultats de reconnaissance des deux modèles en même temps que la transcription du jeu de données soumis. Pour aider à inspecter la comparaison côte à côte, vous pouvez basculer les divers types d’erreurs, y compris d’insertion, suppression et la substitution. En écoutant l’audio et en comparant les résultats de reconnaissance dans chaque colonne, ce qui montre la transcription étiquetés humaines et les résultats de deux modèles de la parole-texte, vous pouvez décider quel modèle répond à vos besoins et où une formation supplémentaire et des améliorations sont Obligatoire.

## <a name="next-steps"></a>Étapes suivantes

* [Formation d’un modèle](how-to-custom-speech-train-model.md)
* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester vos données](how-to-custom-speech-test-data.md)
* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
