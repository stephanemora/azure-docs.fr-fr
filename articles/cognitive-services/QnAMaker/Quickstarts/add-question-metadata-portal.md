---
title: 'Démarrage rapide : Ajouter des questions et des réponses dans le portail QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment ajouter des séries de questions et de réponses avec des métadonnées afin de permettre à vos utilisateurs de trouver la réponse à leur question.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 664d6006ab78f91a8ed0e199cf78fae9512efd73
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843037"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Démarrage rapide : Ajouter des questions et des réponses à l’aide du portail QnA Maker

Une fois qu’une base de connaissances a été créée, ajoutez des séries de questions et de réponses avec des métadonnées afin de permettre à vos utilisateurs de trouver la réponse appropriée à leur question.

La réponse appropriée est une seule et même réponse, mais il peut y avoir de nombreuses façons pour un client de poser la question menant à cette réponse.

Par exemple, les questions du tableau ci-dessous concernent les limites du service Azure. Toutefois, chacune d’entre elles concerne un service Azure distinct.

<a name="qna-table"></a>


|Définissez|Questions|Réponse|Métadonnées|
|--|--|--|--|
|No 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|No 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Une fois les métadonnées ajoutées à une série de questions et réponses, l’application cliente peut :

* Demander les réponses qui correspondent uniquement à certaines métadonnées
* Recevoir toutes les réponses, mais les traiter ensuite en fonction des métadonnées de chaque réponse

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

* Un service QnA Maker
* Une base de connaissances créée dans ce service QnA Maker

Tous deux ont été créés dans le [premier guide de démarrage rapide](../how-to/create-knowledge-base.md).

## <a name="sign-in-to-the-qna-maker-portal"></a>Se connecter au portail QnA Maker

1. Connectez-vous au [portail QnA Maker](https://www.qnamaker.ai).

1. Sélectionnez votre base de connaissances existante. Si vous n’avez pas de base de connaissances, retournez au [guide de démarrage rapide précédent](../how-to/create-knowledge-base.md), puis terminez les étapes permettant de créer votre base de connaissances.

## <a name="add-additional-alternatively-phrased-questions"></a>Ajouter des questions supplémentaires formulées différemment

La base de connaissances actuelle du [précédent guide de démarrage rapide](../how-to/create-knowledge-base.md) contient les séries de questions et de réponses QnA Maker liées à la résolution des problèmes. Ces séries ont été créées au moment où l’URL a été ajoutée à la base de connaissances durant le processus de création.

Une fois que cette URL a été importée, une seule question a été créée avec une seule réponse.

Dans cette procédure, ajoutez des questions supplémentaires.

1. Dans la page **Edit** (Modifier), utilisez la zone de recherche située au-dessus des séries de questions et réponses pour trouver la question `How large a knowledge base can I create?`

1. Dans la colonne **Question**, sélectionnez **+ Add alternative phrasing** (Ajouter une autre formulation), puis ajoutez chacune des nouvelles formulations fournies dans le tableau suivant.

    |Autre formulation|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Sélectionnez **Save and train** (Enregistrer et entraîner) pour réentraîner la base de connaissances.

1. Sélectionnez **Test**, puis entrez une question proche de l’une des nouvelles autres formulations, mais pas identique :

    `What GB size can a knowledge base be?`

    La bonne réponse est retournée au format Markdown : `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Si vous sélectionnez **Inspect** (Inspecter) dans la réponse retournée, vous constatez que des réponses supplémentaires répondent à la question, mais pas avec le même niveau de confiance élevé.

    N’ajoutez pas toutes les combinaisons possibles de formulations alternatives. Activez l’[apprentissage actif](../how-to/improve-knowledge-base.md) de QnA Maker. Cela permet de trouver les autres formulations qui permettent à votre base de connaissances de mieux répondre aux besoins de vos utilisateurs.

1. Sélectionnez à nouveau **Test** pour fermer la fenêtre de test.

## <a name="add-metadata-to-filter-the-answers"></a>Ajouter des métadonnées pour filtrer les réponses

L’ajout de métadonnées à une série de questions et réponses permet à votre application cliente de demander des réponses filtrées. Ce filtre est appliqué avant celui des [premier et deuxième outils de classement](../concepts/query-knowledge-base.md#ranker-process).

1. Ajoutez la deuxième série de questions et réponses, sans les métadonnées, à partir du [premier tableau de ce guide de démarrage rapide](#qna-table), puis continuez en effectuant les étapes suivantes.

1. Sélectionnez **View options** (Options d’affichage), puis **Show metadata** (Afficher les métadonnées).

1. Pour la série de questions et réponses que vous venez d’ajouter, sélectionnez **Add metadata tags** (Ajouter des balises de métadonnées), puis ajoutez le nom de `service` et la valeur de`search`, `service:search`.

1. Ajoutez une autre balise de métadonnées ayant pour nom `link_in_answer` et la valeur `false`, `link_in_answer:false`.

1. Recherchez la première réponse dans le tableau, `How large a knowledge base can I create?`.
1. Ajoutez des paires de métadonnées pour les deux mêmes balises de métadonnées :

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Vous avez désormais deux questions avec les mêmes balises de métadonnées et des valeurs différentes.

1. Sélectionnez **Save and train** (Enregistrer et entraîner) pour réentraîner la base de connaissances.

1. Sélectionnez **Publish** (Publier) dans le menu supérieur pour accéder à la page de publication.
1. Sélectionnez le bouton **Publish** pour publier la base de connaissances actuelle sur un point de terminaison interrogeable.
1. Une fois la base de connaissances publiée, sélectionnez l’onglet **Curl** pour voir un exemple de commande cURL permettant de générer une réponse à partir de la base de connaissances.
1. Copiez la commande dans le Bloc-notes ou tout autre environnement modifiable pour pouvoir la modifier. Modifiez votre nom de ressource, votre ID de base de connaissances et votre clé de point de terminaison :

    |Replace|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Notez que la question est un simple mot, `size`, qui peut retourner l’une ou l’autre des séries de questions et réponses. Le paramètre `strictFilters` indique à la réponse de se réduire uniquement aux réponses de `qna_maker`.

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. La réponse inclut uniquement la réponse qui répond aux critères de filtre.

    La réponse cURL suivante a été mise en forme pour en améliorer la lisibilité :

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    S’il existe une série de questions et réponses qui ne correspond pas au terme recherché, mais qui est conforme au filtre, il n’est pas retourné. À la place, la réponse générale `No good match found in KB.` est retournée.

    Veillez à conserver vos paires nom/valeur de métadonnées dans les limites nécessaires.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une réponse avec Postman ou cURL](get-answer-from-knowledge-base-using-url-tool.md)