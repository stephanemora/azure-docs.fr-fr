---
title: Amélioration d’une base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: L’apprentissage actif vous permet d’améliorer la qualité de votre base de connaissances en suggérant d’autres questions à votre paire de question/réponse basées sur les soumissions d’utilisateurs. Après avoir passé en revue ces suggestions, ajoutez-les aux questions existantes ou rejetez-les. Votre base de connaissances ne change pas automatiquement. Vous devez accepter les suggestions pour toute modification prenne effet. Ces suggestions ajoutent des questions, mais elles ne modifient pas les questions existantes pas plus qu’elles ne les suppriment.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: 739ae64c6b32958271260bcbd01b339c1b108f11
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337423"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Utilisez la formation active pour améliorer votre base de connaissances

L’apprentissage actif vous permet d’améliorer la qualité de votre base de connaissances en suggérant d’autres questions à votre paire de question/réponse basées sur les soumissions d’utilisateurs. Après avoir passé en revue ces suggestions, ajoutez-les aux questions existantes ou rejetez-les. 

Votre base de connaissances ne change pas automatiquement. Vous devez accepter les suggestions afin que toute modification prenne effet. Ces suggestions ajoutent des questions, mais elles ne modifient pas les questions existantes pas plus qu’elles ne les suppriment.

## <a name="what-is-active-learning"></a>Nouveautés d’apprentissage actif ?

QnA Maker apprend de nouvelles variantes de question à l’aide des commentaires implicites et explicites.
 
* Commentaires implicites : l’outil de classement comprend lorsqu’une question d’utilisateur a plusieurs réponses dont les scores sont très proches et la considère comme un commentaire. 
* Commentaires explicites : si plusieurs réponses avec peu de variations dans les scores sont retournées à partir de la base de connaissances, l’application cliente demande à l’utilisateur quelle question est appropriée. Les commentaires explicites de l’utilisateur sont envoyés à QnA Maker avec l’API Train. 

Les deux méthodes fournissent à l’outil de classement des requêtes similaires qui sont ordonnées en cluster.

## <a name="how-active-learning-works"></a>Fonctionnement de l’apprentissage actif

L’apprentissage actif est déclenché en fonction des scores des principales réponses retournées par QnA Maker pour une requête donnée. Si les différences de scores s’inscrivent dans une petite plage, la requête est considérée comme une _suggestion_ possible pour chacune des réponses possibles. 

Toutes les suggestions sont regroupées par similarité, et les principales suggestions pour les questions alternatives sont affichées en fonction de la fréquence des requêtes particulières des utilisateurs finals. L’apprentissage actif offre les meilleures suggestions possible dans les cas où les points de terminaison reçoivent une quantité raisonnable de différentes requêtes d’utilisation.

Lorsque les requêtes de 5 ou plus similaires sont mis en cluster, toutes les 30 minutes, QnA Maker suggère les questions sur l’utilisateur vers le Concepteur de base de connaissances pour accepter ou rejeter.

Une fois que les questions sont suggérées dans le portail QnA Maker, vous devez consulter et accepter ou rejeter ces suggestions. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Mettre à niveau votre version pour utiliser l’apprentissage actif

L’apprentissage actif est pris en charge dans la version 4.4.0 du runtime et dans les versions ultérieures. Si votre base de connaissances a été créée dans une version antérieure, [mettez à niveau votre runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) pour utiliser cette fonctionnalité. 

## <a name="best-practices"></a>Bonnes pratiques

Pour prendre connaissance des meilleures pratiques lors de l’utilisation de l’apprentissage actif, consultez la rubrique [Bonnes pratiques](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Proximité des scores entre les questions de la base de connaissances

Lorsque le niveau de confiance du score d’une question est élevé, par exemple 80 %, la plage des scores pris en compte pour l’apprentissage actif est large, environ 10 %. À mesure que le score de confiance diminue, par exemple 40 %, la plage des scores décroît également et passe à environ 4 %. 

L’algorithme permettant de déterminer la proximité n’est pas un calcul simple. Les plages indiquées dans les exemples précédents ne sont censées être fixes, mais doivent servir de guide pour comprendre l’impact de l’algorithme uniquement.

## <a name="turn-on-active-learning"></a>Activation de l’apprentissage actif

L’apprentissage actif est désactivé par défaut. Activez-le pour afficher les suggestions de questions. 

1. Sélectionnez **publier** pour publier la base de connaissances. Requêtes d’apprentissage actif sont collectés à partir de l’API de GenerateAnswer prédiction point de terminaison uniquement. Les requêtes vers le volet de Test dans le portail Qna Maker n’affectent pas l’apprentissage actif.

1. Pour activer l’apprentissage actif, cliquez sur votre **Nom**, puis accédez à [**Paramètres du service**](https://www.qnamaker.ai/UserSettings) dans le portail QnA Maker, dans l’angle supérieur droit.  

    ![Allumez alternatives de question suggéré d’apprentissage actif à partir de la page des paramètres. Sélectionnez votre nom d’utilisateur dans le menu supérieur droit, puis sélectionnez les paramètres du Service.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Recherchez le service QnA Maker, puis activez la stratégie **Apprentissage actif**. 

    [![Sur la page des paramètres, basculez sur la fonctionnalité d’apprentissage actif. Si vous n’êtes pas en mesure d’activer/désactiver la fonctionnalité, vous devrez peut-être mettre à niveau votre service.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Une fois que la stratégie **Apprentissage actif** est activée, la base de connaissances suggère de nouvelles questions à intervalles réguliers en fonction des questions soumises par l’utilisateur. Vous pouvez désactiver la stratégie **Apprentissage actif** en basculant de nouveau le paramètre.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Ajout d’une suggestion d’apprentissage actif à la base de connaissances

1. Pour afficher les suggestions de questions, dans la page **Edit knowledge base** (Modifier la base de connaissances), sélectionnez **Afficher les suggestions**. 

    [![Dans la section Modifier du portail, sélectionnez Afficher les Suggestions afin de voir les nouvelles alternatives de question d’apprentissage actif.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrer la base de connaissances avec des paires questions et réponses pour afficher uniquement des suggestions en sélectionnant **Filter by Suggestions**.

    [![Utilisez le filtre par activer/désactiver des suggestions pour afficher uniquement les alternatives suggérées question d’apprentissage actif.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Chaque section de question présentant des suggestions affiche les nouvelles questions avec une coche, `✔`, pour les accepter ou avec un symbole `x` pour rejeter les suggestions. Sélectionnez la coche pour ajouter la question. 

    [![Sélectionnez ou rejeter des alternatives de question suggéré d’apprentissage actif en sélectionnant la coche verte ou rouge de suppression.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Vous pouvez ajouter ou supprimer _toutes les suggestions_ en sélectionnant **Ajouter tout** ou **Reject all** (Rejeter tout).

1. Sélectionnez **Save and Train** (Enregistrer et effectuer l’apprentissage) pour enregistrer les modifications apportées à la base de connaissances.

1. Sélectionnez **publier** pour autoriser les modifications soient disponibles à partir de l’API GenerateAnswer.

    Lorsque les requêtes de 5 ou plus similaires sont mis en cluster, toutes les 30 minutes, QnA Maker suggère les questions sur l’utilisateur vers le Concepteur de base de connaissances pour accepter ou rejeter.

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Identification du meilleur choix lorsque plusieurs questions ont des scores similaires

Lorsque le score d’une question est trop proche de celui d’autres questions, le développeur de l’application cliente peut demander des précisions.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Utilisation de la propriété principale dans la requête GenerateAnswer

Lorsque vous soumettez une question à QnA Maker pour obtenir une réponse, le corps JSON permet de retourner plusieurs réponses principales :

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Lorsque l’application cliente (par exemple, un bot conversationnel) reçoit la réponse, les 3 questions principales sont retournées :

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Suivi de l’application cliente lorsque les questions présentent des scores similaires

L’application cliente affiche toutes les questions avec une option permettant à l’utilisateur de sélectionner la question qui représente le mieux son intention. 

Une fois que l’utilisateur sélectionne l’une des questions existantes, l’application cliente envoie le choix de l’utilisateur en tant que commentaires à l’aide des API de Train de QnA Maker. Cette évaluation termine actif boucle de rétroaction d’apprentissage. 

Utilisez le [Azure Bot C# exemple](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot) pour voir un apprentissage actif dans un scénario de bout en bout.

## <a name="train-api"></a>API d’apprentissage

Commentaires d’apprentissage actif sont envoyé à QnA Maker avec la demande de publication de l’API Train. La signature de l’API est :

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Propriété de demande HTTP|Nom|Type|Objectif|
|--|--|--|--|
|Paramètre de routage d’URL|ID de la base de connaissances|string|GUID de votre base de connaissances.|
|Sous-domaine de l’hôte|Nom de la ressource QnAMaker|string|Le nom d’hôte pour votre API QnA Maker dans votre abonnement Azure. Il est disponible dans la page Paramètres une fois que vous publiez la base de connaissances. |
|En-tête|Content-Type|string|type de média du corps envoyé à l’API. Valeur par défaut est : `application/json`|
|En-tête|Authorization|string|clé de votre point de terminaison (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corps de publication|Objet JSON|JSON|Les commentaires de formation|

Le corps JSON a plusieurs paramètres :

|Propriété de corps JSON|Type|Objectif|
|--|--|--|--|
|`feedbackRecords`|array|Liste des commentaires.|
|`userId`|string|L’ID utilisateur de la personne acceptant les suggestions de questions. Le format d’ID utilisateur est à vous. Par exemple, une adresse de messagerie peut être un ID d’utilisateur valide dans votre architecture. facultatif.|
|`userQuestion`|string|Texte exact de la question. Requis.|
|`qnaID`|number|ID de la question, trouvée dans le [GenerateAnswer réponse](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Un exemple de corps JSON ressemble à :

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Une réponse réussie retourne un état 204 et aucun JSON du corps de réponse. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Apprentissage actif est enregistré dans la base de connaissances exportée

Lorsque votre application dispose d’apprentissage actif est activé et que vous exportez l’application, le `SuggestedQuestions` colonne dans le fichier tsv conserve les données d’apprentissage actif. 

Le `SuggestedQuestions` colonne est un objet JSON d’informations d’implicite (`autosuggested`) et explicites (`usersuggested`) [commentaires](#active-learning). Un exemple de cet objet JSON pour une seule question soumises par l’utilisateur de `help` est :

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Lorsque vous réimportez cette application, l’apprentissage actif continue à collecter des informations et recommandons des suggestions pour votre base de connaissances. 

## <a name="next-steps"></a>Étapes suivantes
 
> [!div class="nextstepaction"]
> [Utiliser des métadonnées avec les API de GenerateAnswer](metadata-generateanswer-usage.md)
