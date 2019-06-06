---
title: Apprendre à intégrer le schéma d’alerte commun avec Logic Apps
description: Découvrez comment créer une application logique qui s’appuie sur le schéma d’alerte courantes pour gérer toutes vos alertes.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734889"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Apprendre à intégrer le schéma d’alerte commun avec Logic Apps

Cet article vous montre comment créer une application logique qui s’appuie sur le schéma d’alerte courantes pour gérer toutes vos alertes.

## <a name="overview"></a>Présentation

Le [schéma alerte commun](https://aka.ms/commonAlertSchemaDocs) fournit un schéma JSON normalisé et extensible sur tous vos différents types d’alerte. Le schéma commun de l’alerte est particulièrement utile lorsque exploitées par programmation : par le biais des applications logiques, des runbooks et des webhooks. Dans cet article, nous montrons comment une application logique unique peut être créée pour gérer toutes vos alertes. Les mêmes principes peuvent être appliquées à d’autres méthodes de programmation. L’application de la logique décrite dans cet article crée des variables bien définis pour le [champs « essentielles »](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields)et décrit également comment vous pouvez gérer [type d’alerte](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) logique spécifique.


## <a name="prerequisites"></a>Conditions préalables 

Cet article suppose que le lecteur est familiarisé avec 
* Configuration des règles d’alerte ([métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [journal](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [journal d’activité](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Configurer [groupes d’actions](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* L’activation de la [schéma alerte commun](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) au sein des groupes d’actions

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Créer une application logique en tirant parti du schéma commun de l’alerte

1. Suivez le [étapes décrites pour créer votre application logique](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Sélectionnez le déclencheur : **Lors de la réception d’une requête HTTP**.

    ![Déclencheurs d’application logique](media/action-groups-logic-app/logic-app-triggers.png "Déclencheurs d’application logique")

1.  Sélectionnez **Modifier** pour changer de déclencheur de requête HTTP.

    ![Déclencheurs de requête HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Déclencheurs de requête HTTP")


1.  Copiez et collez le schéma suivant :

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Sélectionnez **+** **Nouvelle étape**, puis **Ajouter une action**.

    ![Ajouter une action](media/action-groups-logic-app/add-action.png "Ajouter une action")

1. À ce stade, vous pouvez ajouter divers connecteurs (Microsoft Teams, Slack, Salesforce, etc.) en fonction de vos besoins professionnels spécifiques. Vous pouvez utiliser le « champs essentiels » out-of-the-box. 

    ![Champs essentiels](media/alerts-common-schema-integrations/logic-app-essential-fields.png "champs essentiels")
    
    Ou bien, vous pouvez créer une logique conditionnelle basée sur le type d’alerte à l’aide de l’option 'Expression'.

    ![Expression d’application logique](media/alerts-common-schema-integrations/logic-app-expressions.png "expression d’application logique")
    
     Le ['monitoringService' champ](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) permet d’identifier de manière unique le type d’alerte en fonction sur laquelle vous pouvez créer la logique conditionnelle.

    
    Par exemple, le ci-dessous extrait de code vérifie si l’alerte est une alerte de journal d’Application Insights en fonction et si tel est le cas imprime les résultats de recherche. Sinon, il imprime « NA ».

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     En savoir plus sur [écrire des expressions de l’application logique](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les groupes d’actions](../../azure-monitor/platform/action-groups.md).
* [En savoir plus sur le schéma d’alerte commun](https://aka.ms/commonAlertSchemaDocs).

