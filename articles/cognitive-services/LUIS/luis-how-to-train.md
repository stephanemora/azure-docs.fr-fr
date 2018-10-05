---
title: Entraîner votre version de LUIS
titleSuffix: Azure Cognitive Services
description: L’entraînement est le processus au cours duquel votre version d’application Language Understanding (LUIS) apprend afin d’améliorer sa compréhension du langage naturel. Entraînez votre application LUIS après des mises à jour du modèle, telles qu’un ajout, une édition, un étiquetage ou une suppression d’entités, d’intentions ou d’énoncés.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: f27716cc416b162a5b2df5542d709058f3b3e903
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182029"
---
# <a name="train-your-luis-app-version"></a>Entraîner votre version d’application LUIS

La formation est le processus au cours duquel votre application Language Understanding (LUIS) apprend afin d’améliorer sa compréhension du langage naturel. Formez votre application LUIS après des mises à jour du modèle, telles qu’un ajout, une édition, un étiquetage ou une suppression d’entités, d’intentions ou d’énoncés. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

La formation et le [test](luis-concept-test.md) d’une application est un processus itératif. Après avoir formé votre application LUIS, vous la testez avec des exemples d’énoncés afin de voir si les intentions et les entités sont reconnues correctement. Si ce n’est pas le cas, apportez des mises à jour à l’application LUIS, puis formez-la et testez-la à nouveau. 

## <a name="how-to-train"></a>Comment entraîner
Pour démarrer le processus itératif, vous devez commencer par former votre application LUIS au moins une fois. Assurez-vous que chaque intention a au moins un énoncé avant l’apprentissage.

1. Accédez à votre application en sélectionnant son nom dans la page **My Apps** (Mes applications). 

2. Dans le volet supérieur votre application, sélectionnez **Train** (Former). 

3. Une fois la formation terminée, une barre de notification verte s’affiche en haut du navigateur.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Si vous avez une ou plusieurs intentions dans votre application, qui ne contiennent pas d’exemple d’énoncé, vous ne pouvez pas former votre application. Ajoutez des énoncés pour tous vos intentions. Pour plus d’informations, voir [Ajouter des exemples d’énoncés](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Entraîner avec toutes les données
L’entraînement utilise un petit pourcentage d’échantillonnage négatif. Si vous souhaitez utiliser toutes les données plutôt que le petit échantillonnage négatif, utilisez l’[API de paramètres de version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) avec `UseAllTrainingData` défini sur true pour désactiver cette fonctionnalité. 

## <a name="next-steps"></a>Étapes suivantes

* [Étiqueter des énoncés suggérés avec LUIS](luis-how-to-review-endoint-utt.md) 
* [Utiliser les fonctionnalités pour améliorer les performances de votre application LUIS](luis-how-to-add-features.md) 