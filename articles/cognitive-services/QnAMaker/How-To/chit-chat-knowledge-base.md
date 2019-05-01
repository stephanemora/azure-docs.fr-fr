---
title: Ajout d’échanges de conversation à une base de connaissances QnA Maker
titleSuffix: Azure Cognitive Services
description: L’ajout d’échanges de conversation personnels à votre bot rend ses conversations plus naturelles et engageantes quand vous créez une base de connaissances. QnA Maker vous permet d’ajouter facilement un ensemble prédéfini d’échanges de conversation dans votre base de connaissances.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a908185f2d8475afb642250d1499cffa539aca4d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573503"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Ajouter des échanges de conversation à une base de connaissances

L’ajout d’échanges de conversation à votre bot rend ses conversations plus naturelles et engageantes. La fonctionnalité d’échanges de conversation de QnA Maker vous permet d’ajouter facilement un ensemble prédéfini d’échanges de conversation dans votre base de connaissances. Cela peut constituer un point de départ pour la personnalité de votre bot, et vous permettra d’économiser le temps et les efforts qui seraient nécessaires à leur écriture à partir de zéro.  

Ce jeu de données a des scénarios de 100 de conversation instantanée chit dans la voix de plusieurs personnes, tels que Professional, convivial et Witty. Choisissez la personnalité qui se rapproche le plus de la voix de votre bot. Quand il fait face à une requête de l’utilisateur, QnA Maker tente de la faire correspondre au QnA d’échanges de conversation connu le plus proche.  

Sont des exemples de l’autres personnalités ci-dessous. Vous pouvez voir tous les datasets de personnalité ainsi que les détails des personnalités [ici](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets).

<!-- added quotes so acrolinx doesn't score these sentences -->
|Requête de l’utilisateur|Professionnel|Convivial|Beau|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`Aw, I'm blushing.`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`Those who can, do. Those who can't, don't sing.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Definitely didn't see that coming!`|`Sure. Take me to city hall. See what happens.`|



> [!NOTE]
> Actuellement, les échanges de conversation sont disponibles en anglais uniquement. 

## <a name="add-chit-chat-during-kb-creation"></a>Ajouter des échanges de conversation lors de la création de la base de connaissances
Pendant la création de la base de connaissances, après l’ajout des vos URL et fichiers sources, il existe une option permettant d’ajouter des échanges de conversation. Choisissez la personnalité que vous souhaitez utiliser comme base pour les échanges de conversation. Si vous ne souhaitez pas ajouter d’échanges de conversation, ou si votre source de données prend déjà en charge les échanges de conversation, choisissez **None** (Aucun). 
   
![Ajouter des échanges de conversation lors de la création](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Ajouter des échanges de conversation à une base de connaissances existante
Sélectionnez votre base de connaissances et accédez à la page **Settings** (Paramètres). Il existe un lien vers tous les jeux de données d’échanges de conversation au format **.tsv** approprié. Téléchargez la personnalité souhaitée, puis chargez-la en tant que source de fichier. Veillez à ne pas modifier le format ou les métadonnées quand vous téléchargez et chargez le fichier. 
  
![Ajouter des échanges de conversation à une base de connaissances existante](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Modifier vos questions et réponses d’échanges de conversation
Quand vous modifierez votre base de connaissances, vous verrez une nouvelle source pour les échanges de conversation, basée sur la personnalité sélectionnée. Vous pouvez maintenant ajouter des questions modifiées, ou modifier les réponses, comme avec toute autre source. 

![Modifier les QnA d’échanges de conversation](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>Ajouter des questions et réponses d’échanges de conversation supplémentaires
Vous pouvez ajouter de nouveaux QnA d’échanges de conversation qui ne figurent pas dans le jeu prédéfini. Veillez à ne pas dupliquer une paire de QnA déjà couverte dans le jeu d’échanges de conversation. Quand vous ajoutez un nouveau QnA d’échanges de conversation, il est ajouté à votre source **éditoriale**. Pour être sûr que la fonctionnalité de classement comprend qu’il s’agit d’échanges de conversation, ajoutez la paire clé/valeur de métadonnées « Editorial: chit-chat », comme indiqué dans l’image suivante :
   
![Ajouter des QnA d’échanges de conversation](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Supprimer des échanges de conversation d’une base de connaissances existante
Sélectionnez votre base de connaissances et accédez à la page **Settings** (Paramètres). Votre source d’échanges de conversation spécifique est listée en tant que fichier, avec le nom de la personnalité sélectionnée. Vous pouvez supprimer ce fichier en tant que fichier source.

![Supprimer les échanges de conversation de la base de connaissances](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Importer une base de connaissances](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
