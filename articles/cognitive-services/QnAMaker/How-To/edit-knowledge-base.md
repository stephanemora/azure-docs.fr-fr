---
title: Comment modifier une base de connaissances - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Comment modifier une base de connaissances
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369649"
---
# <a name="edit-a-knowledge-base"></a>Modifier une base de connaissances

QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.

## <a name="edit-your-knowledge-base-content"></a>Modifier le contenu de votre base de connaissances

1.  Sélectionnez **Mes bases de connaissances** dans la barre de navigation supérieure. 

    Vous pouvez voir tous les services, que vous avez créés ou qui ont été partagés avec vous, triés dans l’ordre décroissant de la date de **dernière modification**.

    ![Mes bases de connaissances](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Sélectionnez une base de connaissances particulière pour y apporter des modifications.

3. Une fois que vous avez terminé d’apporter des modifications à la base de connaissances, cliquez sur **Save and train** (Enregistrer et former) dans le coin supérieur droit de la page afin de conserver les modifications.    

    ![Enregistrer et former](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Quitter la page avant de cliquer sur Save and train (Enregistrer et former) ne conserve pas les modifications.

## <a name="add-a-qna-pair"></a>Ajouter une paire QnA

Sélectionnez **Add QnA pair** (Ajouter une paire QnA) pour ajouter une nouvelle ligne à la table de base de connaissances.

![Ajouter une paire QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Supprimer une paire QnA

Pour supprimer un QnA, cliquez sur l’icône **Supprimer** à l’extrême droite de la ligne QnA.

![Supprimer une paire QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Ajoutez des questions alternatives à une paire QnA existante pour améliorer la probabilité d’une correspondance à une requête utilisateur.

![Ajouter des questions alternatives](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Ajouter des métadonnées


Ajouter des paires de métadonnées en sélectionnant l’icône de filtre

![Ajouter des métadonnées](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Veillez à enregistrer et former régulièrement la base de connaissances après avoir apporté des modifications pour éviter de perdre des modifications.

## <a name="manage-large-knowledge-bases"></a>Gérer de grandes bases de connaissances

1. Les QnA sont **regroupés** par la source de données de laquelle ils ont été extraits. Vous pouvez développer ou réduire la source de données.
2. Vous pouvez **rechercher** dans la base de connaissances en saisissant dans la zone de texte en haut de la table de Base de connaissances. Cliquez sur Entrée pour effectuer des recherches sur le contenu de la question, de la réponse ou des métadonnées. Cliquez sur l’icône X pour supprimer le filtre de recherche.
3. La **pagination** vous permet de gérer de grandes bases de connaissances

    ![Rechercher, paginer, regrouper](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collaborer sur une base de connaissances](./collaborate-knowledge-base.md)
