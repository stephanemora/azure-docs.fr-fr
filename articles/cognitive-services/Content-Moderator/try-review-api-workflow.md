---
title: Flux de travail de modération du contenu à partir de la console d’API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilisez les opérations de flux de travail dans Azure Content Moderator pour créer ou mettre à jour un flux de travail, ou obtenir des informations sur le flux de travail à l’aide de l’API de révision.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: c71358828be1c5a2a83092f8079eca37c9e757dd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207205"
---
# <a name="workflows-from-the-api-console"></a>Flux de travail à partir de la console d’API

Utilisez les [opérations de flux de travail](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dans Azure Content Moderator pour créer ou mettre à jour un flux de travail, ou obtenir des informations sur le flux de travail à l’aide de l’API de révision. Vous pouvez définir des expressions simples, complexes et même imbriquées pour vos flux de travail à l’aide de cette API. Les flux de travail s’affichent dans l’outil de révision pour que votre équipe puisse les utiliser. Les flux de travail sont également utilisés dans le cadre des opérations de l’API de révision.

## <a name="prerequisites"></a>Prérequis

1. Accédez à l’[outil de révision](https://contentmoderator.cognitive.microsoft.com/). Inscrivez-vous si ce n’est pas encore fait. 
2. Dans l’outil de révision, sous **Paramètres**, sélectionnez l’onglet **Flux de travail**, comme indiqué dans le [didacticiel de flux de travail](Review-Tool-User-Guide/Workflows.md) de l’outil de révision.

### <a name="browse-to-the-workflows-screen"></a>Accéder à l’écran Flux de travail

Dans le tableau de bord Content Moderator, sélectionnez **Réviser** > **Paramètres** > **Flux de travail**. Un flux de travail par défaut s’affiche.

  ![Flux de travail par défaut](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Obtenir la définition JSON du flux de travail par défaut

Sélectionnez l’option **Modifier** pour votre flux de travail, puis sélectionnez l’onglet **JSON**. L’expression JSON suivante apparaît :

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>Obtenir les détails des flux de travail

Utilisez l’opération **Flux de travail - Obtenir** pour obtenir les détails de votre flux de travail par défaut existant.

Dans l’outil de révision, accédez à la section [Informations d’identification](Review-Tool-User-Guide/credentials.md#the-review-tool).

### <a name="browse-to-the-api-reference"></a>Accéder aux informations de référence sur l’API

1. Dans la fenêtre **Informations d’identification**, sélectionnez [Référence API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Lorsque la page **Flux de travail - Créer ou mettre à jour** s’ouvre, accédez à la référence [Flux de travail - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58).

### <a name="select-your-region"></a>Sélectionner votre région

Pour l’option **Open API testing console** (Ouvrir la console de test d’API), sélectionnez la région qui décrit le mieux votre emplacement.

  ![Flux de travail - Sélection de région](images/test-drive-region.png)

  La console de l’API **Flux de travail - Obtenir** s’ouvre.

### <a name="enter-parameters"></a>Saisie des paramètres

Entrez les valeurs de **team**, **workflowname** et **Ocp-Apim-Subscription-Key** (votre clé d’abonnement) :

- **équipe** : ID d’équipe que vous avez créé quand vous avez configuré votre [compte de l’outil de révision](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname** : nom de votre flux de travail. Utilisez `default`.
- **Ocp-Apim-Subscription-Key** : valeur située dans l'onglet **Paramètres**. Pour plus d’informations, consultez la [vue d’ensemble ](overview.md) de Content Moderator.

  ![Obtenir les paramètres de requête et les en-têtes](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Soumettre votre requête
  
Sélectionnez **Envoyer**. Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche le flux de travail JSON suivant :

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Créer un workflow

Dans l’outil de révision, accédez à la section [Informations d’identification](Review-Tool-User-Guide/credentials.md#the-review-tool).

### <a name="browse-to-the-api-reference"></a>Accéder aux informations de référence sur l’API

Dans la fenêtre **Informations d’identification**, sélectionnez [Référence API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). La page **Flux de travail - Créer ou mettre à jour** s’ouvre.

### <a name="select-your-region"></a>Sélectionner votre région

Pour l’option **Open API testing console** (Ouvrir la console de test d’API), sélectionnez la région qui décrit le mieux votre emplacement.

  ![Sélection de la région sur la page Flux de travail - Créer ou mettre à jour](images/test-drive-region.png)

  La console de l’API **Flux de travail - Créer ou mettre à jour** s’ouvre.

### <a name="enter-parameters"></a>Saisie des paramètres

Entrez les valeurs de **team**, **workflowname** et **Ocp-Apim-Subscription-Key** (votre clé d’abonnement) :

- **équipe** : ID d’équipe que vous avez créé quand vous avez configuré votre [compte de l’outil de révision](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname** : nom de votre nouveau flux de travail.
- **Ocp-Apim-Subscription-Key** : valeur située dans l'onglet **Paramètres**. Pour plus d’informations, consultez la [vue d’ensemble ](overview.md) de Content Moderator.

  ![Paramètres de requête et en-têtes de la console Flux de travail - Créer ou mettre à jour](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Saisir la définition du flux de travail

1. Modifiez la zone **Corps de la demande** pour entrer la requête JSON avec des détails pour la **description** et le **type** (image ou texte). 
2. Pour **Expression**, copiez l’expression de flux de travail par défaut à partir de la section précédente, comme illustré ici :

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Le corps de la demande ressemble à la requête JSON suivante :

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>Soumettre votre requête
  
Sélectionnez **Envoyer**. Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche `true`.

### <a name="check-out-the-new-workflow"></a>Extraire le nouveau flux de travail

Dans l’outil de révision, sélectionnez **Réviser** > **Paramètres** > **Flux de travail**. Votre nouveau flux de travail s’affiche et est prêt à être utilisé.

  ![Liste des flux de travail de l’outil de révision](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Réviser les détails de votre nouveau flux de travail

1. Sélectionnez l’option **Modifier** pour votre flux de travail, puis sélectionnez les onglets **Concepteur** et **JSON**.

   ![Onglet Concepteur pour un flux de travail sélectionné](images/workflow-console-new-workflow-designer.PNG)

2. Pour voir l’affichage JSON du flux de travail, sélectionnez l’onglet **JSON**.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des exemples de flux de travail plus complexes, voir [Vue d’ensemble des flux de travail](workflow-api.md).
* Découvrez comment utiliser des flux de travail avec des [travaux de modération du contenu](try-review-api-job.md).
