---
title: Étendre des alertes de Log Analytics à Azure
description: Cet article décrit les outils et les API qui permettent d’étendre des alertes à partir de Log Analytics vers Azure Alerts.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 0524e38f155049c630ace7f33805ef230c8dccca
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344242"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Étendre des alertes à partir de Log Analytics vers Azure Alerts
La fonctionnalité des alertes dans Azure Log Analytics est remplacée par Azure Alerts. Dans le cadre de cette transition, les alertes que vous avez configurées à l’origine dans Log Analytics vont être étendues à Azure. Si vous ne souhaitez pas attendre qu’elles soient déplacées automatiquement dans Azure, vous pouvez lancer le processus :

- Manuellement à partir du portail Operations Management Suite 
- Par programmation en utilisant l’API AlertsVersion  

> [!NOTE]
> Depuis le 14 mai 2018, Microsoft étend automatiquement à Azure Alerts les alertes créées dans des instances publiques de Log Analytics, selon une série répétée jusqu’à ce que la migration soit terminée. Si vous rencontrez des problèmes pour créer des [groupes d’actions](../../azure-monitor/platform/action-groups.md), utilisez [ces étapes correctives](alerts-extend-tool.md#troubleshooting) afin de créer automatiquement des groupes d’actions. Vous pouvez utiliser ces étapes jusqu’au 5 juillet 2018. *Non applicable aux utilisateurs du cloud souverain et d’Azure Government de Log Analytics*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Option 1 : Lancement à partir du portail Operations Management Suite
Les étapes suivantes expliquent comment étendre les alertes pour l’espace de travail à partir du portail Operations Management Suite.  

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
                            "serviceUri": "http://test.com"
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
                            "serviceUri": "http://test.com"
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


## <a name="option-3-use-a-custom-powershell-script"></a>Option 3 : Utilisation d’un script PowerShell personnalisé
 Si l’extension par Microsoft de vos alertes à Azure ne s’est pas déroulée correctement depuis le portail Operations Management Suite, vous pouvez procéder à cette opération manuellement jusqu’au 5 juillet 2018. Les deux options de l’extension manuelle sont décrites dans les deux sections précédentes.

Après le 5 juillet 2018, toutes les alertes du portail Operations Management Suite seront étendues à Azure. Les utilisateurs qui n’ont pas effectué [les étapes de correction nécessaires suggérées](#troubleshooting) verront leurs alertes s’exécuter sans déclencher d’actions ni de notifications, du fait de l’absence de [groupes d’actions](../../azure-monitor/platform/action-groups.md) associés. 

Pour créer manuellement des [groupes d’actions](../../azure-monitor/platform/action-groups.md) pour les alertes dans Log Analytics, utilisez l’exemple de script suivant :
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>À propos du script PowerShell personnalisé 
Voici quelques informations importantes sur l’utilisation du script :
- L’installation de [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l’appel de l’API Azure Resource Manager, est nécessaire.
- Pour exécuter le script, vous devez disposer d’un rôle de contributeur ou de propriétaire dans l’abonnement Azure.
- Vous devez fournir les paramètres suivants :
    - $subscriptionId : ID d’abonnement Azure associé à l’espace de travail Log Analytics dans Operations Management Suite.
    - $resourceGroup : Groupe de ressources Azure pour l’espace de travail Log Analytics dans Operations Management Suite.
    - $workspaceName : Nom de l’espace de travail Log Analytics dans Operations Management Suite.

### <a name="output-of-the-custom-powershell-script"></a>Sortie du script PowerShell personnalisé
Le script est détaillé et génère les étapes au fur et à mesure qu’il s’exécute : 
- Il affiche le résumé, qui contient les informations sur les alertes Operations Management Suite Log Analytics existantes de l’espace de travail. Le résumé contient également des informations sur les groupes d’actions Azure à créer pour les actions qui s’y rapportent. 
- Vous êtes invité à poursuivre l’extension ou à l’arrêter après avoir consulté le résumé.
- Si vous continuez ce processus d’extension, des groupes d’actions Azure sont créés, et toutes les alertes existantes leur sont associées. 
- Le script s’arrête en affichant le message « Extension terminée ! » En cas de défaillances intermédiaires, le script affiche les erreurs suivantes.

## <a name="troubleshooting"></a>Résolution de problèmes 
Pendant le processus d’extension des alertes, des problèmes peuvent empêcher le système de créer les [groupes d’actions](../../azure-monitor/platform/action-groups.md) nécessaires. Dans ce cas, un message d’erreur s’affiche sous forme de bannière dans la section **Alerte** du portail Operations Management Suite ou dans l’appel GET à l’API.

> [!IMPORTANT]
> Si les utilisateurs de Log Analytics basé sur le cloud public Azure n’effectuent pas les étapes de correction suivantes avant le 5 juillet 2018, les alertes s’exécuteront dans Azure, mais elles ne déclencheront aucune action ni notification. Pour obtenir des notifications d’alertes, vous devez modifier et ajouter manuellement des [groupes d’actions](../../azure-monitor/platform/action-groups.md), ou utiliser le précédent [script PowerShell personnalisé](#option-3---using-custom-powershell-script).

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
