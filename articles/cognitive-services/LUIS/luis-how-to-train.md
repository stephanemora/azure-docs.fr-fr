---
title: Entraîner une application
titleSuffix: Language Understanding - Azure Cognitive Services
description: L’entraînement est le processus au cours duquel votre version d’application Language Understanding (LUIS) apprend afin d’améliorer sa compréhension du langage naturel. Formez votre application LUIS après des mises à jour du modèle, telles qu’un ajout, une édition, un étiquetage ou une suppression d’entités, d’intentions ou d’énoncés.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: ba450258a03fc21a06476a6bdd81dc5a886056de
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137622"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Entraîner votre version active de l’application LUIS 

La formation est le processus au cours duquel votre application Language Understanding (LUIS) apprend afin d’améliorer sa compréhension du langage naturel. Formez votre application LUIS après des mises à jour du modèle, telles qu’un ajout, une édition, un étiquetage ou une suppression d’entités, d’intentions ou d’énoncés. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

La formation et le [test](luis-concept-test.md) d’une application est un processus itératif. Après avoir formé votre application LUIS, vous la testez avec des exemples d’énoncés afin de voir si les intentions et les entités sont reconnues correctement. Si ce n’est pas le cas, apportez des mises à jour à l’application LUIS, puis formez-la et testez-la à nouveau. 

La formation est appliquée à la version active dans le portail LUIS. 

## <a name="how-to-train-interactively"></a>Comment effectuer l’apprentissage de manière interactive

Pour démarrer le processus itératif dans le [portail LUIS](https://www.luis.ai), vous devez commencer par effectuer l’apprentissage de votre application LUIS au moins une fois. Assurez-vous que chaque intention a au moins un énoncé avant l’apprentissage.

1. Accédez à votre application en sélectionnant son nom dans la page **My Apps** (Mes applications). 

2. Dans le volet supérieur votre application, sélectionnez **Train** (Former). 

3. Une fois la formation terminée, une barre de notification verte s’affiche en haut du navigateur.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Si vous avez une ou plusieurs intentions dans votre application, qui ne contiennent pas d’exemple d’énoncé, vous ne pouvez pas former votre application. Ajoutez des énoncés pour tous vos intentions. Pour plus d’informations, voir [Ajouter des exemples d’énoncés](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Entraîner avec toutes les données

L’entraînement utilise un petit pourcentage d’échantillonnage négatif. Si vous souhaitez utiliser toutes les données plutôt que le petit échantillonnage négatif, utilisez l’[API de paramètres de version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) avec `UseAllTrainingData` défini sur true pour désactiver cette fonctionnalité. 

## <a name="unnecessary-training"></a>Formation inutile

Vous n’avez pas besoin d’effectuer l’apprentissage après chaque modification. La formation doit être effectuée après l’application d’un groupe de modifications au modèle et l’étape suivante consiste à tester ou à publier. Si vous n’avez pas besoin de tester ou de publier, la formation n’est pas nécessaire. 

## <a name="training-with-the-rest-apis"></a>Formation avec les API REST

Pour effectuer l’apprentissage dans le portail LUIS, il suffit de cliquer sur le bouton **Former**. La formation avec les API REST est un processus en deux étapes. La première consiste à [demander une apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) avec HTTP POST. Ensuite, demandez l’[état de la formation](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) avec HTTP Get. 

Pour savoir quand la formation est terminée, vous devez interroger l’état jusqu’à ce que tous les modèles sont correctement formés. 

## <a name="next-steps"></a>Étapes suivantes

* [Étiqueter des énoncés suggérés avec LUIS](luis-how-to-review-endoint-utt.md) 
* [Utiliser les fonctionnalités pour améliorer les performances de votre application LUIS](luis-how-to-add-features.md) 