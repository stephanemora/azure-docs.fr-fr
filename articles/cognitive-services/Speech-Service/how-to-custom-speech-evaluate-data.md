---
title: Évaluer le niveau de justesse de Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Dans ce document, vous allez apprendre à quantifier la qualité de notre modèle de reconnaissance vocale ou de votre modèle personnalisé. Des données de transcription audio et étiquetées à la main sont nécessaires pour tester la précision ; il faut également fournir entre 30 minutes et 5 heures d’audio représentatif.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806094"
---
# <a name="evaluate-custom-speech-accuracy"></a>Évaluer la précision de Custom Speech

Dans ce document, vous allez apprendre à quantifier la qualité du modèle de reconnaissance vocale de Microsoft ou de votre modèle personnalisé. Des données de transcription audio et étiquetées à la main sont nécessaires pour tester la précision ; il faut également fournir entre 30 minutes et 5 heures d’audio représentatif.

## <a name="what-is-word-error-rate-wer"></a>Qu’est-ce que le taux d'erreur de mots ?

Le standard de mesure de la précision d’un modèle est le *taux d’erreur de mots*  (WER, de l’anglais « Word Error Rate »). Il compte le nombre de mots incorrects identifiés lors de la reconnaissance, puis le divise par le nombre total de mots fournis dans la transcription étiquetée à la main. Enfin, ce nombre est multiplié par 100 % pour calculer le taux WER.

![Formule du taux WER](./media/custom-speech/custom-speech-wer-formula.png)

Les mots mal identifiés se décomposent en trois catégories :

* Insertion (I) : mots ajoutés à tort à l’hypothèse de transcription
* Suppression (D) : mots non détectés dans l’hypothèse de transcription
* Substitution (S) : mots substitués entre la référence et l’hypothèse

Voici un exemple :

![Exemple de mots mal identifiés](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Résoudre les erreurs et améliorer le taux WER

Le taux WER peut être utilisé à partir des résultats de la reconnaissance automatique pour évaluer la qualité du modèle d’une application, d’un outil ou d’un produit. Un taux WER compris entre 5 et 10 % est considéré comme étant de bonne qualité et prêt à l’emploi. Un taux de 20 % est acceptable, mais un apprentissage supplémentaire est envisageable. Un taux supérieur ou égal à 30 % est signe de qualité médiocre et demande une personnalisation et un apprentissage.

La distribution des erreurs est importante. La présence de nombreuses erreurs de suppression est généralement due à une puissance faible du signal audio. Pour résoudre ce problème, vous devrez collecter des données audio plus près de la source. Les erreurs d’insertion signifient que l’audio a été enregistré dans un environnement bruyant et qu’il y a un risque de diaphonie, ce qui pose des problèmes de reconnaissance. Les erreurs de substitution se rencontrent souvent lorsque l’échantillon de termes propres à un domaine fourni sous la forme de transcriptions étiquetées à la main ou de texte lié est insuffisant.

En analysant les fichiers un par un, vous pouvez identifier les différents types d’erreurs, ainsi que les erreurs propres à un fichier donné. Il est essentiel de comprendre les problèmes au niveau du fichier pour fixer des objectifs d’amélioration.

## <a name="create-a-test"></a>Créer un test

Si vous souhaitez tester la qualité du modèle de reconnaissance vocale de référence de Microsoft ou d’un modèle personnalisé entraîné par vos soins, vous pouvez comparer deux modèles côte à côte pour évaluer la précision. La comparaison englobe le taux WER et les résultats de reconnaissance. On compare en général un modèle personnalisé avec le modèle de référence de Microsoft.

Pour évaluer des modèles côte à côte :

1. Connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech).
2. Accédez à **Speech-to-text > Custom Speech > Testing**.
3. Cliquez sur **Add Test** (Ajouter un test).
4. Sélectionnez **Évaluer la précision**. Donnez au test un nom et une description et sélectionnez votre jeu de données de transcription audio + étiquetées à la main.
5. Sélectionnez les modèles que vous voulez tester (deux au maximum).
6. Cliquez sur **Créer**.

Une fois votre test créé, vous pouvez comparer les résultats côte à côte.

## <a name="side-by-side-comparison"></a>Comparaison côte à côte

Une fois terminé (ce qu’indique le passage à l’état *Réussi*), le test comporte un nombre WER pour les deux modèles. Cliquez sur le nom du test pour afficher la page de détails correspondante. Cette page de détails liste tous les énoncés de votre jeu de données, indiquant les résultats de la reconnaissance des deux modèles avec la transcription du jeu de données soumis. Pour inspecter plus facilement la comparaison côte à côte, vous pouvez voir les différents types d’erreurs (insertion, suppression et substitution). En écoutant l’audio et en comparant les résultats de la reconnaissance dans les deux colonnes (transcription étiquetée à la main et résultats des deux modèles de reconnaissance vocale), vous pouvez identifier le modèle qui répond à vos besoins et déterminer où un apprentissage et des améliorations s’imposent.

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner un modèle](how-to-custom-speech-train-model.md)
* [Déployer un modèle](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester les données](how-to-custom-speech-test-data.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)
