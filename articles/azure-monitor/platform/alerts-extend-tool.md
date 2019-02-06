---
title: Étendre des alertes de Log Analytics au cloud Azure Government
description: Cet article décrit les outils et les API qui permettent d’étendre des alertes à partir de Log Analytics vers Azure Alerts.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103375"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Étendre des alertes à partir de Log Analytics vers Azure Alerts
La fonctionnalité des alertes dans le portail OMS est remplacée par Azure Alerts dans le cloud Azure Government. Dans le cadre de cette transition, les alertes que vous avez configurées à l’origine dans Log Analytics vont être étendues à Azure. Si vous ne souhaitez pas attendre qu’elles soient déplacées automatiquement dans Azure, vous pouvez lancer le processus :

- Manuellement à partir du portail Operations Management Suite 
- Par programmation en utilisant l’API AlertsVersion  

> [!NOTE]
> Depuis le 1er mars 2019, Microsoft étend automatiquement à Azure Alerts les alertes créées dans des instances de Log Analytics sur le portail OMS Azure Government. Si vous rencontrez des problèmes pour créer des [groupes d’actions](../../azure-monitor/platform/action-groups.md), utilisez [ces étapes correctives](alerts-extend-tool.md#troubleshooting) afin de créer automatiquement des groupes d’actions. Vous pouvez utiliser ces étapes jusqu’au 15 mars 2019 dans le portail OMS Azure Government.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Option 1 : Lancement à partir du portail Operations Management Suite
Les étapes suivantes expliquent comment étendre les alertes pour l’espace de travail à partir du portail Operations Management Suite pour le cloud Azure Government.  

1. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans le volet des abonnements Log Analytics, sélectionnez un espace de travail, puis sélectionnez la vignette **Portail OMS**.
![Capture d’écran du volet des abonnements Log Analytics, avec la vignette Portail OMS sélectionnée](media/alerts-extend-tool/azure-portal-01.png) 
3. Après avoir été redirigé vers le portail Operations Management Suite, sélectionnez l’icône **Paramètres**.
![Capture d’écran du portail Operations Management Suite, avec l’icône Paramètres sélectionnée](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Dans la page **Paramètres**, sélectionnez **Alertes**.  
5. Sélectionnez **Étendre à Azure**.
![Capture d’écran de la page des paramètres d’alerte du portail Operations Management Suite, avec l’option Étendre à Azure sélectionnée](media/alerts-extend-tool/ExtendInto.png)
6. Un Assistant en trois étapes s’affiche dans le volet **Alertes**. Lisez la présentation, puis sélectionnez **Suivant**.
![Capture d’écran de l’étape 1 de l’Assistant](media/alerts-extend-tool/ExtendStep1.png)  
7. Dans la deuxième étape, vous voyez un résumé des modifications proposées, qui répertorie les [groupes d’actions](../../azure-monitor/platform/action-groups.md) appropriés pour les alertes. Si des actions similaires concernent plusieurs alertes, l’Assistant propose de toutes les associer dans un groupe d’action unique.  La convention d’affectation de noms est la suivante : *NomEspaceDeTravail_GA_ #Numéro*. Pour continuer, sélectionnez **Suivant**.
![Capture d’écran de l’étape 2 de l’Assistant](media/alerts-extend-tool/ExtendStep2.png)  
8. Dans la dernière étape de l’Assistant, sélectionnez **Terminer** et confirmez le lancement du processus lorsque vous y êtes invité. Si vous le souhaitez, vous pouvez fournir une adresse e-mail pour être tenu informé de la fin du processus et de la réussite du déplacement de toutes les alertes dans Azure Alerts.
![Capture d’écran de l’étape 3 de l’Assistant](media/alerts-extend-tool/ExtendStep3.png)

Lorsque l’Assistant a terminé, l’option permettant d’étendre les alertes à Azure est supprimée dans la page **Paramètres d’alerte**. En arrière-plan, vos alertes sont déplacées vers Azure, ce qui peut prendre un certain temps. Pendant le déroulement de l’opération, vous ne pouvez apporter aucune modification aux alertes à partir du portail Operations Management Suite. Vous pouvez voir la progression de cette opération dans la bannière affichée en haut du portail. Si vous aviez fourni une adresse e-mail, vous êtes averti par un message de la fin du processus.  


Le portail Operations Management Suite continue de lister les alertes, même après leur déplacement vers Azure.
![Capture d’écran de la page des paramètres d’alerte dans le portail Operations Management Suite](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Option 2 : Utilisation de l’API AlertsVersion
Vous pouvez utiliser l’API AlertsVersion de Log Analytics pour étendre les alertes de Log Analytics à Azure Alerts, à partir de n’importe quel client pouvant appeler une API REST. Vous accédez à l’API depuis PowerShell par le biais de [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source. Vous pouvez générer les résultats au format JSON.  

Pour utiliser l’API, vous créez tout d’abord une requête GET. Une évaluation est réalisée et un résumé des modifications proposées est retourné, avant que vous tentiez réellement l’extension à Azure par le biais d’une requête POST. Les résultats répertorient vos alertes et une liste est proposée de [groupes d’actions](../../azure-monitor/platform/action-groups.md), au format JSON. Si des actions similaires concernent plusieurs alertes, le service propose de toutes les associer dans un groupe d’actions unique. La convention d’affectation de noms est la suivante : *NomEspaceDeTravail_GA_ #Numéro*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Si la requête GET aboutit, un code d’état HTTP 200 est retourné, accompagné d’une liste d’alertes avec les groupes d’actions proposés, sous la forme de données JSON. Voici un exemple de réponse :

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Si aucune règle d’alerte n’est définie dans l’espace de travail spécifié, les données JSON retournent le message suivant :

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Si toutes les règles d’alerte de l’espace de travail spécifié ont déjà été étendues à Azure, la réponse à la requête GET est la suivante :

```json
{
    "version": 2
}
```

Pour démarrer la migration des alertes vers Azure, lancez une réponse POST. La réponse POST confirme votre intention, ainsi que votre acceptation, d’étendre les alertes à Azure depuis Log Analytics. L’activité est planifiée et les alertes sont traitées comme indiqué, selon les résultats obtenus lors de l’exécution de la réponse GET précédemment. Le cas échéant, vous pouvez fournir une liste d’adresses e-mail auxquelles un rapport est envoyé par Log Analytics à la fin de l’exécution planifiée du processus de migration des alertes en arrière-plan. Vous pouvez utiliser l’exemple de requête suivant :

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Le résultat de la migration des alertes vers Azure Alerts peut varier en fonction du résumé fourni par la réponse GET. Lorsqu’elles sont planifiées, les alertes dans Log Analytics ne sont temporairement pas modifiables depuis le portail Operations Management Suite. Vous pouvez, en revanche, créer de nouvelles alertes. 

Si la requête POST aboutit, elle retourne un état HTTP 200 OK, accompagné de la réponse suivante :

```json
{
    "version": 2
}
```

Cette réponse indique que les alertes ont été correctement étendues à Azure Alerts. La propriété version sert uniquement à vérifier que les alertes ont bien été étendues à Azure, et qu’il n’existe aucune relation à l’[API Recherche Log Analytics](../../azure-monitor/platform/api-alerts.md). Lorsque les alertes sont correctement étendues à Azure, un rapport est envoyé aux adresses e-mail fournies avec la requête POST. Si l’extension de toutes les alertes de l’espace de travail spécifié est déjà planifiée, la réponse à votre requête POST est que la tentative a été interdite (code d’état 403). Pour afficher un message d’erreur ou savoir si le processus est bloqué, vous pouvez soumettre une requête GET. Si un message d’erreur existe, il est retourné avec les informations de résumé.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>Résolution de problèmes 
Pendant le processus d’extension des alertes, des problèmes peuvent empêcher le système de créer les [groupes d’actions](../../azure-monitor/platform/action-groups.md) nécessaires. Dans ce cas, un message d’erreur s’affiche sous forme de bannière dans la section **Alerte** du portail Operations Management Suite ou dans l’appel GET à l’API.

> [!IMPORTANT]
> Si les utilisateurs du portail OMS basé sur le cloud Azure Government n’effectuent pas les étapes de correction suivantes avant le 15 mars 2019, les alertes s’exécuteront dans Azure, mais elles ne déclencheront aucune action ni notification. Pour obtenir des notifications pour les alertes, vous devez modifier manuellement leurs règles d’alerte dans Azure et ajouter des [groupes d’actions](../../azure-monitor/platform/action-groups.md).

Voici les étapes de correction pour chaque erreur :
- **Erreur : Scope Lock is present at subscription/resource group level for write operations** (Le service Scope Lock est présent au niveau du groupe de ressources/de l’abonnement pour les opérations d’écriture) :   ![Capture d’écran de la page des paramètres d’alerte du portail Operations Management Suite, avec le message d’erreur Scope Lock sélectionné](media/alerts-extend-tool/ErrorScopeLock.png)

    Lorsque la fonctionnalité Scope Lock est activée, elle limite toute nouvelle modification sur l’abonnement ou le groupe de ressources qui contient l’espace de travail Log Analytics (dans Operations Management Suite). Le système ne peut pas étendre les alertes à Azure et créer les groupes d’actions nécessaires.
    
    Pour remédier à cela, supprimez le verrou *ReadOnly* sur votre abonnement ou groupe de ressources qui contient l’espace de travail. Vous pouvez effectuer cette opération à l’aide du portail Azure, de PowerShell, d’Azure CLI ou de l’API. Pour en savoir plus, consultez l’[utilisation du verrouillage des ressources](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    Lorsque vous corrigez cette erreur en procédant comme illustré dans l’article, Operations Management Suite étend vos alertes à Azure lors de l’exécution planifiée du jour suivant. Aucune autre action ou lancement de quoi que ce soit n’est nécessaire.

- **Erreur : Policy is present at subscription/resource group level** (Le service Policy est présent au niveau du groupe de ressources/de l’abonnement) :   ![Capture d’écran de la page des paramètres d’alerte du portail Operations Management Suite, avec le message d’erreur Policy sélectionné](media/alerts-extend-tool/ErrorPolicy.png)

    Lorsque [Azure Policy](../../governance/policy/overview.md) est appliqué, il restreint toute nouvelle ressource dans un abonnement ou un groupe de ressources qui contient l’espace de travail Log Analytics (dans Operations Management Suite). Le système ne peut pas étendre les alertes à Azure et créer les groupes d’actions nécessaires.
    
    Pour remédier à cela, modifiez la stratégie à l’origine de l’erreur *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*, qui empêche la création de nouvelles ressources sur votre abonnement ou groupe de ressources contenant l’espace de travail. Vous pouvez effectuer cette opération à l’aide du portail Azure, de PowerShell, d’Azure CLI ou de l’API. Vous pouvez auditer des actions pour trouver la stratégie en rapport, qui provoque l’échec. Pour plus d’informations, consultez [l’affichage des journaux d’activité pour auditer les actions](../../azure-resource-manager/resource-group-audit.md). 
    
    Lorsque vous corrigez cette erreur en procédant comme illustré dans l’article, Operations Management Suite étend vos alertes à Azure lors de l’exécution planifiée du jour suivant. Aucune autre action ou lancement de quoi que ce soit n’est nécessaire.


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la nouvelle [expérience des alertes Azure](../../azure-monitor/platform/alerts-overview.md).
* En savoir plus sur les [alertes de journal dans Azure Alerts](alerts-unified-log.md).

