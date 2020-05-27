---
title: 'Démarrage rapide : Ajouter des questions et des réponses dans le portail QnA Maker'
description: Ce guide de démarrage rapide montre comment ajouter des séries de questions et de réponses avec des métadonnées afin de permettre à vos utilisateurs de trouver la réponse à leur question.
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: fc8bac425723e9194c447f1cf6ee13547c09d772
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873688"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Démarrage rapide : Ajouter des questions et des réponses à l’aide du portail QnA Maker

Une fois qu’une base de connaissances est créée, ajoutez des jeux de questions et réponses (QnA) avec des métadonnées pour filtrer la réponse. Les questions du tableau ci-dessous concernent les limites du service Azure. Toutefois, chacune d’entre elles concerne un service Azure distinct.

<a name="qna-table"></a>

|Définissez|Questions|Réponse|Métadonnées|
|--|--|--|--|
|No 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|No 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Une fois les métadonnées ajoutées à une paire de Q/R, l’application cliente peut :

* Demander les réponses qui correspondent uniquement à certaines métadonnées
* Recevoir toutes les réponses, mais les traiter ensuite en fonction des métadonnées de chaque réponse


## <a name="prerequisites"></a>Prérequis

* Suivre le guide de [démarrage rapide précédent](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Se connecter au portail QnA Maker

1. Connectez-vous au [portail QnA Maker](https://www.qnamaker.ai).

1. Sélectionnez votre base de connaissances existante issue du guide de [démarrage rapide précédent](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Ajouter des questions supplémentaires formulées différemment

La base de connaissances actuelle contient des paires de Q/R de résolution des problèmes liés à QnA Maker. Ces séries ont été créées au moment où l’URL a été ajoutée à la base de connaissances durant le processus de création.

Une fois que cette URL a été importée, une seule question a été créée avec une seule réponse. Dans cette procédure, ajoutez des questions supplémentaires.

1. Dans la page **Edit** (Modifier), utilisez la zone de recherche située au-dessus des séries de questions et réponses pour trouver la question `How large a knowledge base can I create?`

1. Dans la colonne **Question**, sélectionnez **+ Add alternative phrasing** (Ajouter une autre formulation), puis ajoutez chacune des nouvelles formulations fournies dans le tableau suivant.

    |Autre formulation|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Sélectionnez **Save and train** (Enregistrer et entraîner) pour réentraîner la base de connaissances.

1. Sélectionnez **Test**, puis entrez une question proche de l’une des nouvelles autres formulations, mais pas identique :

    `What GB size can a knowledge base be?`

    La bonne réponse est retournée au format Markdown :

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Si vous sélectionnez **Inspect** (Inspecter) dans la réponse retournée, vous constatez que des réponses supplémentaires répondent à la question, mais pas avec le même niveau de confiance élevé.

    N’ajoutez pas toutes les combinaisons possibles de formulations alternatives. Quand vous activez l’[apprentissage actif](../how-to/improve-knowledge-base.md) de QnA Maker, cela permet de trouver les autres formulations qui permettent à votre base de connaissances de mieux répondre aux besoins de vos utilisateurs.

1. Sélectionnez à nouveau **Test** pour fermer la fenêtre de test.

## <a name="add-metadata-to-filter-the-answers"></a>Ajouter des métadonnées pour filtrer les réponses

L’ajout de métadonnées à une série de questions et réponses permet à votre application cliente de demander des réponses filtrées. Ce filtre est appliqué avant celui des [premier et deuxième outils de classement](../concepts/query-knowledge-base.md#ranker-process).

1. Ajoutez la deuxième série de questions et réponses, sans les métadonnées, à partir du [premier tableau de ce guide de démarrage rapide](#qna-table), puis continuez en effectuant les étapes suivantes.

1. Sélectionnez **View options** (Options d’affichage), puis **Show metadata** (Afficher les métadonnées).

1. Pour la paire de Q/R que vous venez d’ajouter, sélectionnez **Add metadata tags** (Ajouter des étiquettes de métadonnées), puis ajoutez le nom de `service` et la valeur de `search`. Voici à quoi cela ressemble : `service:search`.

1. Ajoutez une autre étiquette de métadonnées portant le nom `link_in_answer` et ayant pour valeur `false`. Voici à quoi cela ressemble : `link_in_answer:false`.

1. Recherchez la première réponse dans le tableau, `How large a knowledge base can I create?`.

1. Ajoutez des paires de métadonnées pour les deux mêmes balises de métadonnées :

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Vous avez désormais deux questions avec les mêmes balises de métadonnées et des valeurs différentes.

1. Sélectionnez **Save and train** (Enregistrer et entraîner) pour réentraîner la base de connaissances.

1. Sélectionnez **Publish** (Publier) dans le menu supérieur pour accéder à la page de publication.
1. Sélectionnez le bouton **Publish** pour publier la base de connaissances actuelle sur le point de terminaison.
1. Une fois la base de connaissances publiée, passez au démarrage rapide suivant pour apprendre à générer une réponse issue de votre base de connaissances.

## <a name="what-did-you-accomplish"></a>Qu’avez-vous fait ?

Vous avez modifié votre base de connaissances pour prendre en charge plus de questions et paires nom/valeur fournies afin de prendre en charge le filtrage lors de la recherche de la meilleure réponse ou du post-traitement, une fois la ou les réponses retournées.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne passez pas au démarrage rapide suivant, supprimez les ressources de QnA Maker et de Bot Framework dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une réponse avec Postman ou cURL](get-answer-from-knowledge-base-using-url-tool.md)