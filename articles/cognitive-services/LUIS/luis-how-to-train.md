---
title: Former votre application LUIS - Azure | Microsoft Docs
description: Utiliser Language Understanding (LUIS) pour former votre modèle.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223182"
---
# <a name="train-your-luis-app"></a>Formez votre application LUIS

La formation est le processus au cours duquel votre application Language Understanding (LUIS) apprend afin d’améliorer sa compréhension du langage naturel. Formez votre application LUIS après des mises à jour du modèle, telles qu’un ajout, une édition, un étiquetage ou une suppression d’entités, d’intentions ou d’énoncés. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

La formation et le [test](luis-concept-test.md) d’une application est un processus itératif. Après avoir formé votre application LUIS, vous la testez avec des exemples d’énoncés afin de voir si les intentions et les entités sont reconnues correctement. Si ce n’est pas le cas, apportez des mises à jour à l’application LUIS, puis formez-la et testez-la à nouveau. 

## <a name="train-your-app"></a>Former votre application
Pour démarrer le processus itératif, vous devez commencer par former votre application LUIS au moins une fois. Assurez-vous que chaque intention a au moins un énoncé avant l’apprentissage.

1. Accédez à votre application en sélectionnant son nom dans la page **My Apps** (Mes applications). 

2. Dans le volet supérieur votre application, sélectionnez **Train** (Former). 

    ![Bouton Train](./media/luis-how-to-train/train-button.png)

3. Une fois la formation terminée, une barre de notification verte s’affiche en haut du navigateur.

    ![Page Train & Test App (Former et tester l’application)](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Si vous avez une ou plusieurs intentions dans votre application, qui ne contiennent pas d’exemple d’énoncé, vous ne pouvez pas former votre application. Ajoutez des énoncés pour tous vos intentions. Pour plus d’informations, voir [Ajouter des exemples d’énoncés](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Étapes suivantes

* [Étiqueter des énoncés suggérés avec LUIS](luis-how-to-review-endoint-utt.md) 
* [Utiliser les fonctionnalités pour améliorer les performances de votre application LUIS](luis-how-to-add-features.md) 