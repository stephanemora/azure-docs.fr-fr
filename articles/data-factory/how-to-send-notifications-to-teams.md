---
title: Envoi de notifications à un canal Microsoft Teams
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment envoyer des notifications à un canal Microsoft Teams à partir d’un pipeline Azure Data Factory ou Synapse Analytics.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.custom: synapse
ms.topic: how-to
ms.subservice: tutorials
ms.date: 09/29/2021
ms.openlocfilehash: d1a23e166c322a4a74c3ec175ea672d78ed23de9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368347"
---
# <a name="send-notifications-to-a-microsoft-teams-channel-from-an-azure-data-factory-or-synapse-analytics-pipeline"></a>Envoyer des notifications à un canal Microsoft Teams à partir d’un pipeline Azure Data Factory ou Synapse Analytics

Il est souvent nécessaire d’envoyer des notifications pendant ou après l’exécution d’un pipeline. La notification fournit des alertes proactives et réduit le besoin d’une surveillance réactive pour découvrir les problèmes.  Vous pouvez en savoir plus sur l’[envoi de notifications par e-mail à l’aide d’applications logiques](tutorial-control-flow-portal.md#create-email-workflow-endpoints) qu’un pipeline Data Factory ou Synapse peut appeler.  De nombreuses entreprises utilisent également de plus en plus Microsoft Teams pour la collaboration.  Cet article explique comment configurer des notifications à partir d’alertes de pipeline dans Microsoft Teams. 

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir envoyer des notifications à Teams depuis vos pipelines, vous devez créer un [webhook entrant](/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using) pour votre canal Teams. Si vous devez créer un nouveau canal Teams à cet effet, reportez-vous à la [documentation de Teams](https://support.microsoft.com/office/create-a-channel-in-teams-fda0b75e-5b90-4fb8-8857-7e102b014525).  

1.  Ouvrez Microsoft Teams et rendez-vous dans l’onglet Applications. Recherchez « Webhook entrant » et sélectionnez le connecteur Webhook entrant.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-incoming-webhook-connector.png" alt-text="Montre l’application Webhook entrant sous l’onglet Applications dans Teams.":::

1.  Ajoutez le connecteur au site Teams où vous souhaitez envoyer les notifications.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-connector-to-site.png" alt-text="Met en évidence le bouton &quot;Ajouter à une équipe&quot; pour l’application Webhook entrant.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-prod-notifications.png" alt-text="Montre l’invite de sélection de l’équipe dans la boîte de dialogue de configuration de l’application Webhook entrant dans Teams.":::

1.  Nommez le webhook comme il convient et, si vous le souhaitez, chargez une icône pour identifier vos messages.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-icon.png" alt-text="Met en évidence la propriété du nom, le chargement d’une image facultative et le bouton &quot;Créer&quot; dans la page des options du webhook entrant.":::  

1.  Copiez le magasin de l’URL de webhook qui est générée lors de la création pour une utilisation ultérieure dans ADF.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-copy-webhook-url.png" alt-text="Montre la nouvelle URL de webhook dans la page des options du webhook entrant après la création.":::

1.  Vous pouvez voir la notification dans le canal où vous ajoutez le connecteur du webhook.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-channel-notification.png" alt-text="Montre la notification dans le canal Teams où vous avez ajouté le connecteur du webhook.":::
        
## <a name="steps-to-send-notifications-on-teams-channel-from-a-pipeline"></a>Étapes pour envoyer des notifications sur le canal Teams à partir d’un pipeline :

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

1.  Créez un nouveau **pipeline à partir d’un modèle**. La galerie de modèles fournit un modèle de pipeline qui facilite la prise en main des notifications d’équipes.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template.png" alt-text="Montre le menu &quot;Pipeline à partir d’un modèle&quot; dans Azure Data Factory Studio.":::

1.  Recherchez « teams », puis sélectionnez et utilisez le modèle **Envoyer une notification à un canal dans Microsoft Teams**.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog.png" alt-text="Montre le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot; dans la galerie de modèles.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template.png" alt-text="Montre les détails du modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot; dans la galerie de modèles.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Montre les propriétés du pipeline créé par le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot;.":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

1.  Créez un nouveau **pipeline à partir d’un modèle**. La galerie de modèles fournit un modèle de pipeline qui facilite la prise en main des notifications d’équipes.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template-synapse.png" alt-text="Montre le menu &quot;Pipeline à partir d’un modèle&quot; dans Azure Data Factory Studio.":::

1.  Recherchez « teams », puis sélectionnez et utilisez le modèle **Envoyer une notification à un canal dans Microsoft Teams**.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog-synapse.png" alt-text="Montre le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot; dans la galerie de modèles.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template-synapse.png" alt-text="Montre les détails du modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot; dans la galerie de modèles.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Montre les propriétés du pipeline créé par le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot;.":::

---

3.  Nous recommandons d’ajouter l’**ID d’abonnement** Data Factory actuel, le **groupe de ressources** et l’**URL du webhook Teams** (voir les [prérequis](#prerequisites)) pour connaître la valeur par défaut des paramètres appropriés.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/webhook-recommended-properties.png" alt-text="Montre les propriétés recommandées du pipeline créé par le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot;.":::

    Ces paramètres sont utilisés pour construire l’URL de surveillance. Supposons que vous ne fournissez pas un abonnement et un groupe de ressources valides (appartenant à la même fabrique de données que les pipelines). Dans ce cas, la notification ne contiendra pas d’URL de surveillance de pipeline valide, mais les messages fonctionneront quand même.  En outre, l’ajout de ces paramètres permet d’éviter de devoir toujours transmettre ces valeurs depuis un autre pipeline. Si vous avez l’intention de contrôler ces valeurs par une approche basée sur les métadonnées, vous devez les modifier en conséquence.
    
1.  Ajoutez une activité **Execute Pipeline** dans le pipeline à partir duquel vous souhaitez envoyer des notifications sur le canal Teams. Sélectionnez le pipeline généré à partir du modèle **Envoyer une notification à un canal dans Microsoft Teams** en tant que **pipeline appelé** dans l’activité **Execute Pipeline**.

     :::image type="content" source="media/how-to-send-notifications-to-teams/execute-pipeline-activity.png" alt-text="Montre l’activité &quot;Execute Pipeline&quot; dans le pipeline créé par le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot;.":::

1.  Personnalisez les paramètres selon les besoins en fonction du type d’activité.

    :::image type="content" source="media/how-to-send-notifications-to-teams/customize-parameters-by-activity-type.png" alt-text="Montre la personnalisation des paramètres du pipeline créé par le modèle &quot;Envoyer une notification à un canal dans Microsoft Teams&quot;.":::   
  
1.  Recevez des notifications dans Teams.

    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-notifications-view-pipeline-run.png" alt-text="Montre les notifications de pipeline dans un canal Teams.":::
## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>Ajouter des messages dynamiques avec des variables système et des expressions

Vous pouvez utiliser des [variables système](control-flow-system-variables.md) et des [expressions](control-flow-expression-language-functions.md) pour rendre vos messages dynamiques. Par exemple :  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

Les expressions ci-dessus renvoient les messages d’erreur pertinents en cas d’échec, qui peuvent être envoyés sous forme de notification sur un canal Teams. Pour plus d’informations, consultez l’article [Propriétés de sortie de l’activité Copy](copy-activity-monitoring.md).

Nous vous encourageons également à consulter le [schéma des charges utiles de notification](https://adaptivecards.io/explorer/AdaptiveCard.html) prises en charge par Microsoft Teams et à personnaliser davantage le modèle ci-dessus en fonction de vos besoins.
