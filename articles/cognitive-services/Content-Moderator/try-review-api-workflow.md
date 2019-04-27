---
title: Définir des workflows de modération avec la console de l’API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Vous pouvez utiliser les API de révision de modérateur de contenu Azure pour définir le flux de travail personnalisés et les seuils en fonction de vos stratégies de contenu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605901"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Définir et utiliser des flux de travail de modération (REST)

Flux de travail est des filtres personnalisés basés sur le cloud qui vous permettent de gérer plus efficacement le contenu. Flux de travail peut se connecter à une variété de services pour filtrer le contenu de différentes façons et puis exécutez l’action appropriée. Ce guide vous montre comment utiliser le flux de travail API REST, via la console d’API, pour créer et utiliser des flux de travail. Une fois que vous comprenez la structure des API, vous pouvez facilement porter ces appels à n’importe quelle plateforme compatible avec REST.

## <a name="prerequisites"></a>Conditions préalables

- Connectez-vous ou créez un compte sur le modérateur de contenu [outil de révision](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="create-a-workflow"></a>Créer un workflow

Pour créer ou mettre à jour un flux de travail, accédez à la **[flux de travail - créer ou mettre à jour](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API référence la page, sélectionnez le bouton pour votre région clé (vous pouvez le trouver dans l’URL de point de terminaison sur le **informations d’identification**  page de la [outil de révision](https://contentmoderator.cognitive.microsoft.com/)). Cela démarre la console d’API, où vous pouvez facilement construire et exécuter des appels d’API REST.

![Sélection de la région sur la page Flux de travail - Créer ou mettre à jour](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Entrez les paramètres d’appel REST

Entrez les valeurs **équipe**, **workflowname**, et **Ocp-Apim-Subscription-Key**:

- **team** : ID d’équipe que vous avez créé lorsque vous configurez votre [outil de révision](https://contentmoderator.cognitive.microsoft.com/) compte (trouvée dans le **Id** champ sur l’écran des informations d’identification de votre outil de révision).
- **workflowname** : Le nom d’un nouveau flux de travail à ajouter (ou un nom existant, si vous souhaitez mettre à jour d’un workflow existant).
- **Ocp-Apim-Subscription-Key** : Votre clé de Content Moderator. Vous pouvez le trouver sur le **paramètres** onglet de la [outil de vérification](https://contentmoderator.cognitive.microsoft.com).

![Paramètres de requête et en-têtes de la console Flux de travail - Créer ou mettre à jour](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Entrez une définition de workflow

1. Modifier le **corps de la demande** zone pour entrer la requête JSON avec les détails pour **Description** et **Type** (soit `Image` ou `Text`).
2. Pour **Expression**, copier le flux de travail par défaut expression JSON. Votre chaîne JSON finale doit ressembler à ceci :

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Vous pouvez définir des expressions simples, complexes et imbriquées de même pour vos flux de travail à l’aide de cette API. Le [flux de travail - créer ou mettre à jour](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) documentation comporte des exemples montrant une logique plus complexe.

### <a name="submit-your-request"></a>Soumettre votre requête
  
Sélectionnez **Envoyer**. Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche `true`.

### <a name="examine-the-new-workflow"></a>Examiner le nouveau flux de travail

Dans le [outil de révision](https://contentmoderator.cognitive.microsoft.com/), sélectionnez **paramètres** > **des flux de travail**. Votre nouveau flux de travail doit apparaître dans la liste.

![Liste des flux de travail de l’outil de révision](images/workflow-console-new-workflow.PNG)

Sélectionnez le **modifier** option pour votre flux de travail et accédez à la **concepteur** onglet. Ici, vous pouvez voir une représentation de la logique JSON et intuitive.

![Onglet Concepteur pour un flux de travail sélectionné](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obtenir les détails des flux de travail

Pour extraire des informations sur un workflow existant, accédez à la **[Workflow - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API référence la page et sélectionnez le bouton de votre région (la région dans laquelle votre clé est administrée).

![Flux de travail - Sélection de région](images/test-drive-region.png)

Entrez les paramètres d’appel REST, comme dans la section ci-dessus. Assurez-vous que cette fois, **workflowname** est le nom d’un flux de travail existant.

![Obtenir les paramètres de requête et les en-têtes](images/workflow-get-default.PNG)

Sélectionnez **Envoyer**. Si l’opération réussit, le **état de la réponse** est `200 OK`et le **contenu de la réponse** boîte affiche le flux de travail au format JSON, comme suit :

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser des flux de travail avec des [travaux de modération du contenu](try-review-api-job.md).