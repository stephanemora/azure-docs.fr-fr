---
title: Ajout d’échanges de conversation à une base de connaissances QnA Maker
titleSuffix: Azure Cognitive Services
description: L’ajout d’échanges de conversation personnels à votre bot rend ses conversations plus naturelles et engageantes quand vous créez une base de connaissances. QnA Maker vous permet d’ajouter facilement un ensemble prédéfini d’échanges de conversation dans votre base de connaissances.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 7c54628faba4a3d7a89c1944e2f434a83035707b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081760"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Ajouter des échanges de conversation à une base de connaissances

L’ajout d’échanges de conversation à votre bot rend ses conversations plus naturelles et engageantes. La fonctionnalité d’échanges de conversation de QnA Maker vous permet d’ajouter facilement un ensemble prédéfini d’échanges de conversation dans votre base de connaissances. Cela peut constituer un point de départ pour la personnalité de votre bot, et vous permettra d’économiser le temps et les efforts qui seraient nécessaires à leur écriture à partir de zéro.  

Ce jeu de données compte environ 100 scénarios d’échanges de conversation avec la voix de trois personnes : le professionnel, l’ami et le comique. Choisissez la personnalité qui se rapproche le plus de la voix de votre bot. Quand il fait face à une requête de l’utilisateur, QnA Maker tente de la faire correspondre au QnA d’échanges de conversation connu le plus proche. 

Voici quelques exemples des différentes personnalités : <!-- added quotes so acrolinx doesn't score these sentences -->
|Requête de l’utilisateur|Le professionnel|L’ami|Le comique|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Aw, that's sweet.`|`Sure. Take me to city hall. See what happens.`|


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
