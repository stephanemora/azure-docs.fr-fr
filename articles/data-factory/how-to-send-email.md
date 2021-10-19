---
title: Comment envoyer un e-mail
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment envoyer un e-mail avec un pipeline Azure Data Factory ou Azure Synapse.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: 64f7fcf5cfd5c24cb8d34e29572f7fbb1314414a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668900"
---
# <a name="send-an-email-with-an-azure-data-factory-or-azure-synapse-pipeline"></a>Envoyer un e-mail avec un pipeline Azure Data Factory ou Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il est souvent nécessaire d’envoyer des notifications pendant ou après l’exécution d’un pipeline. La notification fournit des alertes proactives et réduit le besoin d’une supervision réactive pour découvrir les problèmes.  Cet article explique comment configurer des notifications par e-mail à partir d’un pipeline Azure Data Factory ou Azure Synapse. 

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- **Application logique**. Pour déclencher l’envoi d’un e-mail depuis le pipeline, vous utilisez [Logic Apps](../logic-apps/logic-apps-overview.md) pour définir le flux de travail. Pour plus d’informations sur la création d’un flux de travail Logic Apps, consultez [Guide pratique pour créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-email-workflow-in-your-logic-app"></a>Créer le workflow d’e-mail dans votre application logique

Créez un flux de travail Logic Apps nommé `SendEmailFromPipeline`. Définissez le déclencheur du flux de travail comme `When an HTTP request is received` et ajoutez une action `Office 365 Outlook – Send an email (V2)`.

:::image type="content" source="media/how-to-send-email/logic-app-workflow-designer.png" alt-text="Montre le concepteur de workflow des applications logiques avec l’action Envoyer un e-mail (v2) à partir d’un déclencheur de requête HTTP.":::

Pour le déclencheur de requête HTTP, fournissez le code JSON suivant pour `Request Body JSON Schema` :

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

La requête HTTP du concepteur d’application logique doit ressembler à ceci :

:::image type="content" source="media/how-to-send-email/logic-app-http-request-trigger.png" alt-text="Montre le concepteur de workflow des applications logiques pour le déclencheur de requête HTTP avec le champ de « Schéma JSON du corps de la demande » renseigné.":::

Pour l’action **Envoyer un e-mail (V2)** , personnalisez la mise en forme de l’e-mail à l’aide des propriétés issues du schéma JSON du corps de la demande :

:::image type="content" source="media/how-to-send-email/logic-app-email-action.png" alt-text="Montre le concepteur de workflow des applications logiques pour l’action Envoyer un e-mail (V2).":::

Enregistrez le workflow. Notez l’URL de votre nouveau workflow, puis :

:::image type="content" source="media/how-to-send-email/logic-app-workflow-url.png" alt-text="Montre l’onglet Vue d’ensemble du workflow d’application logique avec l’URL du workflow mise en évidence.":::

## <a name="create-a-pipeline-to-trigger-your-logic-app-email-workflow"></a>Créer un pipeline pour déclencher le workflow d’e-mail de votre application logique

Une fois que vous avez créé le workflow de l’application logique pour envoyer un e-mail, vous pouvez le déclencher à partir d’un pipeline à l’aide d’une activité **Web**.  

1. Créez un pipeline puis recherchez l’activité **Web** sous la catégorie **Général** pour la faire glisser sur le canevas de modification.

1. Sélectionnez la nouvelle activité **Web1**, puis sélectionnez l’onglet **Paramètres**.

   Fournissez l’URL du workflow d’application logique que vous avez créé dans le champ **URL**.

   Entrez le code JSON suivant dans le **corps** :
    ```json
       {
        "message" : "This is a custom dynamic message from your pipeline with run ID @{pipeline().RunId}.",
        "dataFactoryName" : "@{pipeline().DataFactory}", 
        "pipelineName" : "@{pipeline().Pipeline}", 
        "receiver" : "@{pipeline().parameters.receiver}"
       }
    ```
    
    Utilisez des expressions dynamiques afin de générer des messages utiles pour les événements de vos pipelines.  Notez que le format JSON correspond à celui que vous avez défini dans l’application logique, et que vous pouvez également les personnaliser en fonction des besoins.
    
    :::image type="content" source="media/how-to-send-email/pipeline-with-web-activity-calling-logic-app.png" alt-text="Montre un pipeline avec une activité web configuré avec l’URL du workflow de l’application logique et le corps du message JSON.":::

1. Sélectionnez la zone d’arrière-plan du concepteur de pipeline pour sélectionner la page des propriétés du pipeline et ajouter un nouveau paramètre appelé « Receiver » (destinataire), en fournissant une adresse e-mail comme valeur par défaut.
   
   Dans cet exemple, nous fournissons l’e-mail du destinataire à partir d’un paramètre de pipeline que nous pouvons définir librement.  La valeur du paramètre Receiver peut être extraite d’une expression, et même être associée à des sources de données.

   :::image type="content" source="media/how-to-send-email/pipeline-receiver-email-parameter.png" alt-text="Montre la configuration du paramètre Receiver dans le concepteur de pipeline.":::

1. Publiez votre pipeline, puis déclenchez-le manuellement pour être sûr que l’e-mail est envoyé comme attendu.

   :::image type="content" source="media/how-to-send-email/pipeline-manually-trigger.png" alt-text="Montre comment déclencher manuellement le pipeline."::: 

## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>Ajouter des messages dynamiques avec des variables système et des expressions

Vous pouvez utiliser des [variables système](control-flow-system-variables.md) et des [expressions](control-flow-expression-language-functions.md) pour rendre vos messages dynamiques. Par exemple :  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

Les expressions ci-dessus retourneront des messages d’erreur en cas d’échec de l’activité Copy. Vous pouvez alors être redirigé vers votre activité web qui envoie l’e-mail. Pour plus d’informations, consultez l’article [Propriétés de sortie de l’activité Copy](copy-activity-monitoring.md).

## <a name="next-steps"></a>Étapes suivantes

[Guide pratique pour envoyer des notifications Microsoft Teams à partir d’un pipeline](how-to-send-notifications-to-teams.md)
