---
title: Modifier une base de connaissances - QnA Maker
description: QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756715"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Modifier des paires de Q/R dans votre base de connaissances

QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.

Les paires de Q/R sont ajoutées à partir d’une source de données, comme un fichier ou une URL, ou ajoutés en tant que source éditoriale. Une source éditoriale indique que la paire de Q/R a été ajoutée manuellement dans le portail QnA. Toutes les paires de Q/R sont disponibles pour modification.

## <a name="add-an-editorial-qna-pair"></a>Ajouter une paire de Q/R éditoriale
1. Connectez-vous au [portail QnA](https://www.qnamaker.ai/), puis sélectionnez la base de connaissances à laquelle ajouter la paire de Q/R.
1. Dans la page **MODIFIER** de la base de connaissances, sélectionnez **Ajouter une paire de Q/R** pour ajouter une nouvelle paire de Q/R.

1. Dans la ligne de la nouvelle paire de Q/R, ajoutez les champs **Question** et **Réponse** obligatoires. Les autres champs sont facultatifs. Tous les champs peuvent être modifiés à tout moment.

1. Si vous le souhaitez, ajoutez une **formulation alternative**. Une formulation alternative est une forme de question qui est très différente de la question d’origine, mais qui doit fournir la même réponse.

    Quand votre base de connaissances est publiée et que vous avez activé [apprentissage actif](use-active-learning.md), QnA Maker collecte les choix de formulations alternatives que vous pouvez accepter. Ces options sont sélectionnées afin d’augmenter la précision de la prédiction.

1. Si vous le souhaitez, ajoutez des **métadonnées**. Pour afficher les métadonnées, sélectionnez **Afficher les options** dans le menu contextuel. Les métadonnées fournissent des filtres aux réponses fournies par l’application cliente, comme un bot conversationnel.

1. Si vous le souhaitez, ajoutez des **invites de suivi**. Les invites de suivi fournissent des chemins de conversation supplémentaires que l’application cliente peut présenter à l’utilisateur.

1. Sélectionnez **Enregistrer et entraîner** pour voir les prédictions incluant la paire QnA.

## <a name="edit-a-qna-pair"></a>Modifier une paire de Q/R

Vous pouvez modifier tous les champs d’une paire de Q/R, quelle que soit la source de données d’origine. Certains champs peuvent ne pas être visibles en raison de vos **Options d’affichage** actuelles, qui se trouvent dans la barre d’outils contextuelle.

## <a name="delete-a-qna-pair"></a>Supprimer une paire de Q/R

Pour supprimer une paire de Q/R, cliquez sur l’icône **Supprimer** à l’extrême droite de la ligne QnA. Il s’agit d’une opération définitive. Elle ne peut pas être annulée. Envisagez d’exporter votre base de connaissances à partir de la page **Publier** avant de supprimer des ensembles.

![Supprimer une paire QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Rechercher l’ID de la paire QnA

Si vous avez besoin de l’ID de la paire QnA, vous pouvez le trouver à deux endroits :

* Pointez sur l’icône de suppression sur la ligne de la paire QnA qui vous intéresse. Le texte affiché lors du survol comprend l’ID de la paire QnA.
* Exportez la base de connaissances. Chaque paire QnA définie dans la base de connaissances comprend l’ID de la paire QnA.

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Ajoutez des questions alternatives à une paire de Q/R existante pour améliorer la probabilité d’une correspondance à une requête utilisateur.

![Ajouter des questions alternatives](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Liaison de paires QnA

La liaison de paires de Q/R est fournie avec des [invites de suivi](multiturn-conversation.md). Il s’agit d’une connexion logique entre les paires QnA, gérée au niveau de la base de connaissances. Vous pouvez modifier les invites de suivi dans le portail QnA Maker.

Vous ne pouvez pas lier des paires QnA dans les métadonnées de la réponse.

## <a name="add-metadata"></a>Ajouter des métadonnées

Ajoutez des ensembles de métadonnées en sélectionnant d’abord **Afficher les options**, puis en sélectionnant **Afficher les métadonnées**. Cette opération affiche la colonne de métadonnées. Ensuite, sélectionnez le signe **+** pour ajouter un ensemble de métadonnées. Cet ensemble se compose d’une clé et une valeur.

## <a name="save-changes-to-the-qna-pairs"></a>Enregistrez les changements apportés aux paires QnA

Sélectionnez régulièrement **Enregistrer et entraîner** après avoir apporté des modifications afin d’éviter de perdre des modifications.

![Ajouter des métadonnées](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collaborer sur une base de connaissances](./collaborate-knowledge-base.md)

* [Gérer les ressources Azure utilisées par QnA Maker](set-up-qnamaker-service-azure.md)