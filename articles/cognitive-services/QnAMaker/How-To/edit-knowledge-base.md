---
title: Modifier une base de connaissances - QnA Maker
description: QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 8d9f54c1fcd142c77fb73c294b81bf0f11ee083f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99557689"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Modifier des paires de Q/R dans votre base de connaissances

QnA Maker vous permet de gérer le contenu de votre base de connaissances en fournissant une expérience d’édition simple d’utilisation.

Les paires de Q/R sont ajoutées à partir d’une source de données, comme un fichier ou une URL, ou ajoutés en tant que source éditoriale. Une source éditoriale indique que la paire de Q/R a été ajoutée manuellement dans le portail QnA. Toutes les paires de Q/R sont disponibles pour modification.

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>Paires de questions-réponses

Une base de connaissances est composée de paires question/réponse (Q/R).  Chaque paire a une réponse et contient toutes les informations associées à cette _réponse_. Une réponse peut un peu s’apparenter à une ligne de base de données ou à une instance de structure de données. Les paramètres **obligatoires** dans une paire question/réponse (Q/R) sont les suivants :

* Une **question** : texte de la requête utilisateur, utilisé pour le machine learning de QnA Maker, afin de s’aligner avec le texte de la question de l’utilisateur ayant une formulation différente mais la même réponse.
* La **réponse** : la réponse de la paire est ce qui est retourné quand une correspondance est établie entre une requête utilisateur et la question associée.

Chaque paire est représenté par un **ID**.

Les paramètres **facultatifs** pour une paire sont les suivants :

* **Autres formes de la question** : aident QnA Maker à retourner la bonne réponse pour une variété plus large de formulations de questions.
* **Métadonnées** : les métadonnées sont des étiquettes associées à une paire QnA. Elles sont représentées sous forme de paires clé-valeur. Les balises de métadonnées servent à filtrer les paires QnA et à limiter le jeu sur lequel la mise en correspondance de requête est effectuée.
* **Invites multitours** : utilisées pour continuer une conversation multitour.

![ID de base de connaissances QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>Ajouter une paire de Q/R éditoriale

En l'absence de contenu préexistant pour alimenter la base de connaissances, vous pouvez ajouter des paires QnA en tant que rédacteur sur le portail QnA Maker.

1. Connectez-vous au [portail QnA](https://www.qnamaker.ai/), puis sélectionnez la base de connaissances à laquelle ajouter la paire de Q/R.
1. Dans la page **MODIFIER** de la base de connaissances, sélectionnez **Ajouter une paire de Q/R** pour ajouter une nouvelle paire de Q/R.

    > [!div class="mx-imgBorder"]
    > ![Add QnA pair](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Dans la ligne de la nouvelle paire de Q/R, ajoutez les champs Question et Réponse obligatoires. Les autres champs sont facultatifs. Tous les champs peuvent être modifiés à tout moment.

1. Si vous le souhaitez, ajoutez une **[formulation alternative](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** . Une formulation alternative est une forme de question qui est très différente de la question d’origine, mais qui doit fournir la même réponse.

    Quand votre base de connaissances est publiée et que vous avez activé [apprentissage actif](use-active-learning.md), QnA Maker collecte les choix de formulations alternatives que vous pouvez accepter. Ces options sont sélectionnées afin d’augmenter la précision de la prédiction.

1. Si vous le souhaitez, ajoutez des **[métadonnées](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** . Pour afficher les métadonnées, sélectionnez **Afficher les options** dans le menu contextuel. Les métadonnées fournissent des filtres aux réponses fournies par l’application cliente, comme un bot conversationnel.

1. Si vous le souhaitez, ajoutez des **[invites de suivi](multiturn-conversation.md)** . Les invites de suivi fournissent des chemins de conversation supplémentaires que l’application cliente peut présenter à l’utilisateur.

1. Sélectionnez **Enregistrer et entraîner** pour voir les prédictions incluant la paire QnA.

## <a name="rich-text-editing-for-answer"></a>Modification de texte enrichi pour la réponse

La modification du texte enrichi de votre réponse vous permet d’obtenir un style Markdown dans une barre d’outils simple.

1. Sélectionnez la zone de texte pour une réponse, et la barre d’outils Éditeur de texte enrichi s’affiche sur la ligne de la paire Q/R.

    > [!div class="mx-imgBorder"]
    > ![Screenshot of the rich-text editor with the question and answer of a QnA pair row.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Tout texte déjà présent dans la réponse s’affiche correctement, car il est visible par l’utilisateur à partir d’un bot.

1. Modifiez le texte. Sélectionnez les fonctionnalités de mise en forme dans la barre d’outils Éditeur de texte enrichi ou utilisez la fonctionnalité activer/désactiver pour basculer vers la syntaxe Markdown.

    > [!div class="mx-imgBorder"]
    > ![Use the rich-text editor to write and format text and save as markdown.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Fonctionnalités de l’Éditeur de texte enrichi|Raccourci clavier|
    |--|--|
    |Basculement entre l’Éditeur de texte enrichi et Markdown. `</>`|Ctrl+M|
    |Gras. **B**|Ctrl+B|
    |Italique, indiqué par un **_I_** en italique|Ctrl+I|
    |Liste non triée||
    |Liste triée||
    |Style de paragraphe||
    |Image : ajout d’une image disponible à partir d’une URL publique|Ctrl+G|
    |Ajout d’un lien vers une URL accessible publiquement|Ctrl+K|
    |Émoticône : ajout à partir d’une sélection d’émoticônes|Ctrl+E|
    |Menu avancé : annuler|Ctrl+Z|
    |Menu avancé : rétablir|Ctrl+Y|

1. Ajoutez une image à la réponse à l’aide de l’icône d’image dans la barre d’outils de texte enrichi. L’éditeur en place a besoin de l’URL de l’image publiquement accessible et du texte de remplacement de l’image.


    > [!div class="mx-imgBorder"]
    > ![Capture d'écran représentant l'éditeur en place avec l'URL de l'image publiquement accessible et le texte de remplacement de l'image.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Ajoutez un lien vers une URL soit en sélectionnant le texte dans la réponse, puis en choisissant l’icône de lien dans la barre d’outils, soit en sélectionnant l’icône de lien dans la barre d’outils, puis en entrant le nouveau texte et l’URL.

    > [!div class="mx-imgBorder"]
    > ![Use the rich-text editor add a publicly accessible image and its ALT text.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Modifier une paire de Q/R

Vous pouvez modifier tous les champs d’une paire de Q/R, quelle que soit la source de données d’origine. Certains champs peuvent ne pas être visibles en raison de vos **Options d’affichage** actuelles, qui se trouvent dans la barre d’outils contextuelle.

## <a name="delete-a-qna-pair"></a>Supprimer une paire QnA

Pour supprimer un QnA, cliquez sur l’icône **Supprimer** à l’extrême droite de la ligne QnA. Il s’agit d’une opération définitive. Elle ne peut pas être annulée. Envisagez d’exporter votre base de connaissances à partir de la page **Publier** avant de supprimer des paires.

![Supprimer une paire QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Rechercher l’ID de la paire QnA

Si vous avez besoin de l’ID de la paire QnA, vous pouvez le trouver à deux endroits :

* Pointez sur l’icône de suppression sur la ligne de la paire QnA qui vous intéresse. Le texte affiché lors du survol comprend l’ID de la paire QnA.
* Exportez la base de connaissances. Chaque paire QnA définie dans la base de connaissances comprend l’ID de la paire QnA.

## <a name="add-alternate-questions"></a>Ajouter des questions alternatives

Ajoutez des questions alternatives à une paire QnA existante pour améliorer la probabilité d’une correspondance à une requête utilisateur.

![Ajouter des questions alternatives](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Liaison de paires QnA

La liaison de paires de Q/R est fournie avec des [invites de suivi](multiturn-conversation.md). Il s’agit d’une connexion logique entre les paires QnA, gérée au niveau de la base de connaissances. Vous pouvez modifier les invites de suivi dans le portail QnA Maker.

Vous ne pouvez pas lier des paires QnA dans les métadonnées de la réponse.

## <a name="add-metadata"></a>Ajouter des métadonnées

Ajoutez des paires de métadonnées en sélectionnant d’abord **Afficher les options**, puis en sélectionnant **Afficher les métadonnées**. Cette opération affiche la colonne de métadonnées. Ensuite, sélectionnez le signe **+** pour ajouter une paire de métadonnées. Cette paire se compose d’une clé et une valeur.

Apprenez-en plus sur les métadonnées dans le guide de démarrage rapide du portail QnA Maker pour les métadonnées :
* [Création – Ajouter des métadonnées à une paire QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prédiction de requête – Filtrer les réponses par métadonnées](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Enregistrez les changements apportés aux paires QnA

Sélectionnez régulièrement **Enregistrer et entraîner** après avoir apporté des modifications afin d’éviter de perdre des modifications.

![Ajouter des métadonnées](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Choisir entre la modification de texte enrichi et Markdown

[Modifier le texte enrichi](#add-an-editorial-qna-set) de réponses vous permet, en tant qu’auteur, d’utiliser une barre d’outils de mise en forme pour sélectionner le texte et le mettre en forme rapidement.

[Markdown](../reference-markdown-format.md) est un outil plus approprié lorsque vous devez générer automatiquement du contenu pour créer des bases de connaissances à importer dans le cadre d’un pipeline CI/CD ou pour des [tests par lots](../index.yml).

## <a name="editing-your-knowledge-base-locally"></a>Modification de votre base de connaissances en local

Une fois qu’une base de connaissances est créée, il est recommandé d’apporter des modifications au texte associé dans le [portail QnA Maker](https://qnamaker.ai), plutôt que d’exporter et de réimporter via des fichiers locaux. Toutefois, vous pouvez être amené à modifier une base de connaissances localement.

Exportez la base de connaissances à partir de la page **Paramètres**, puis modifiez la base de connaissances avec Microsoft Excel. Si vous choisissez d’utiliser une autre application pour modifier votre fichier exporté, celle-ci peut introduire des erreurs de syntaxe, car elle ne prend pas totalement en charge le format TSV. Les fichiers TSV de Microsoft Excel n’entraînent généralement pas d’erreurs de mise en forme.

Une fois que vous avez terminé vos modifications, réimportez le fichier TSV à partir de la page **Paramètres**. Cela remplace complètement la base de connaissances actuelle par la base de connaissances importée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collaborer sur une base de connaissances](../index.yml)

* [Gérer les ressources Azure utilisées par QnA Maker](set-up-qnamaker-service-azure.md)
