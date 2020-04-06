---
title: Modifier une base de connaissances - QnA Maker
description: QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131652"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>Modifier des ensembles de QnA dans votre base de connaissances

QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.

Les ensembles de QnA sont ajoutés à partir d’une source de données, comme un fichier ou une URL, ou ajoutés en tant que source éditoriale. Une source éditoriale indique que l’ensemble de QnA a été ajouté manuellement dans le portail QnA. Tous les ensembles de QnA sont disponibles pour modification.

## <a name="add-an-editorial-qna-set"></a>Ajouter un ensemble de QnA éditorial
1. Connectez-vous au [portail QnA](https://www.qnamaker.ai/), puis sélectionnez la base de connaissances pour ajouter le QnA défini.
1. Dans la page **MODIFIER** de la base de connaissances, sélectionnez **Ajouter un ensemble de QnA** pour ajouter un nouvel ensemble de QnA.

1. Dans la ligne du nouvel ensemble QnA, ajoutez les champs **Question** et **Réponse**. Les autres champs sont facultatifs. Tous les champs peuvent être modifiés à tout moment.

1. Si vous le souhaitez, ajoutez une **formulation alternative**. Une formulation alternative est une forme de question qui est très différente de la question d’origine, mais qui doit fournir la même réponse.

    Lorsque votre base de connaissances est publiée et que vous avez activé l’apprentissage actif, QnA Maker collecte les choix de formulations alternatives que vous pouvez accepter. Ces options sont sélectionnées afin d’augmenter la précision de la prédiction.

1. Si vous le souhaitez, ajoutez des **métadonnées**. Pour afficher les métadonnées, sélectionnez **Afficher les options** dans le menu contextuel. Les métadonnées fournissent des filtres aux réponses fournies par l’application cliente, comme un bot conversationnel.

1. Si vous le souhaitez, ajoutez des **invites de suivi**. Les invites de suivi fournissent des chemins de conversation supplémentaires que l’application cliente peut présenter à l’utilisateur.

1. Sélectionnez **Enregistrer et entraîner** pour voir les prédictions, y compris le nouvel ensemble de QnA.

## <a name="edit-a-qna-set"></a>Modifier un ensemble de QnA

Tout champ d’un ensemble de QnA peut être modifié, quelle que soit la source de données d’origine. Certains champs peuvent ne pas être visibles en raison de vos **Options d’affichage** actuelles, qui se trouvent dans la barre d’outils contextuelle.

## <a name="delete-a-qna-set"></a>Supprimer un ensemble QnA

Pour supprimer un QnA, cliquez sur l’icône **Supprimer** à l’extrême droite de la ligne QnA. Il s’agit d’une opération définitive. Elle ne peut pas être annulée. Envisagez d’exporter votre base de connaissances à partir de la page **Publier** avant de supprimer des ensembles.

![Supprimer ensemble QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>Rechercher l’ID de l’ensemble de QnA

Si vous avez besoin de l’ID de l’ensemble de QnA, vous pouvez le trouver à deux emplacements :

* Pointez sur l’icône de suppression sur la ligne de l’ensemble de QnA qui vous intéresse. Le texte affiché lors du survol comprend l’ID de l’ensemble de QnA.
* Exportez la base de connaissances. Chaque ensemble de QnA défini dans la base de connaissances comprend l’ID de l’ensemble de QnA.

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Ajoutez des questions alternatives à un ensemble QnA existant pour améliorer la probabilité d’une correspondance à une requête utilisateur.

![Ajouter des questions alternatives](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>Liaison d’ensembles de QnA

La liaison d’ensembles de QnA est fournie avec des [invites de suivi](multiturn-conversation.md). Il s’agit d’une connexion logique entre les ensembles de QnA, gérée au niveau de la base de connaissances. Vous pouvez modifier les invites de suivi dans le portail QnA Maker.

Vous ne pouvez pas lier d’ensembles de QnA dans les métadonnées de la réponse.

## <a name="add-metadata"></a>Ajouter des métadonnées

Ajoutez des ensembles de métadonnées en sélectionnant d’abord **Afficher les options**, puis en sélectionnant **Afficher les métadonnées**. Cette opération affiche la colonne de métadonnées. Ensuite, sélectionnez le signe **+** pour ajouter un ensemble de métadonnées. Cet ensemble se compose d’une clé et une valeur.

## <a name="save-changes-to-the-qna-sets"></a>Enregistrez les changements apportés aux ensembles QnA

Sélectionnez régulièrement **Enregistrer et entraîner** après avoir apporté des modifications afin d’éviter de perdre des modifications.

![Ajouter des métadonnées](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collaborer sur une base de connaissances](./collaborate-knowledge-base.md)

* [Gérer les ressources Azure utilisées par QnA Maker](set-up-qnamaker-service-azure.md)