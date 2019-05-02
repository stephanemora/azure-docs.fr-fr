---
title: Utiliser une alerte pour déclencher un runbook Azure Automation
description: Découvrez comment déclencher un runbook à exécuter quand une alerte Azure est déclenchée.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 892906089ae3538b3427d97165173fd82621f58a
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919980"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Utiliser une alerte pour déclencher un runbook Azure Automation

Vous pouvez utiliser [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) pour surveiller des métriques de base et des journaux d’activité pour la plupart des services Azure. Vous pouvez appeler les runbooks Azure Automation à l’aide de [groupes d’actions](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou à partir d’alertes classiques pour automatiser des tâches basées sur les alertes. Cet article explique comment configurer et exécuter un runbook en utilisant des alertes.

## <a name="alert-types"></a>Types d’alertes

Vous pouvez utiliser des runbooks automation avec quatre types d’alerte :

* Alertes courantes
* Alertes de journal d’activité
* Alerte de métrique quasiment en temps réel

> [!NOTE]
> Le schéma d’alerte commun standardise l’expérience de consommation pour les notifications d’alerte dans Azure dès aujourd'hui. Historiquement, les trois types d’alerte dans Azure dès aujourd'hui (mesures, journaux et journal d’activité) ont leurs propres modèles de courrier électronique, les schémas de webhook, etc. Pour plus d’informations, consultez [schéma commun de l’alerte](../azure-monitor/platform/alerts-common-schema.md)

Quand une alerte appelle un runbook, l’appel réel est une demande HTTP POST vers le Webhook. Le corps de la demande POST contient un objet au format JSON qui contient les propriétés utiles relatives à l’alerte. Le tableau suivant contient des liens vers le schéma de la charge utile pour chaque type d’alerte :

|Alerte  |Description|Schéma de la charge utile  |
|---------|---------|---------|
|[Alerte courantes](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Le schéma alerte commun qui normalise l’expérience de consommation pour les notifications d’alerte dans Azure dès aujourd'hui.|[Schéma de charge utile et alerte commun](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[Alerte du journal d’activité](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envoie une notification lorsqu’un nouvel événement du journal d’activité remplit des conditions spécifiques. Par exemple, lorsqu’une opération `Delete VM` est effectuée dans **myProductionResourceGroup** ou lorsqu’un nouvel événement Azure Service Health avec un statut **Actif** apparaît.| [Schéma de la charge utile et alerte de journal d’activité](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alerte de métrique quasiment en temps réel](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envoie une notification plus rapidement que des alertes de métrique lorsqu’une ou plusieurs mesures au niveau de la plateforme remplissent des conditions spécifiques. Par exemple, lorsque la valeur **CPU %** d’une machine virtuelle est supérieure à **90**, et que la valeur **Network In** est supérieure à **500 Mo** lors des 5 dernières minutes.| [Schéma de la charge utile et alerte de métrique quasiment en temps réel](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Les données fournies par chaque type d’alerte étant différentes, chaque type d’alerte doit être gérée différemment. Dans la section suivante, vous allez apprendre à créer un runbook pour gérer différents types d’alertes.

## <a name="create-a-runbook-to-handle-alerts"></a>Créer un runbook pour gérer les alertes

Pour utiliser Automation avec des alertes, vous avez besoin d’un runbook qui intègre une logique permettant de gérer la charge utile JSON des alertes qui est passée au runbook. L’exemple de runbook suivant doit être appelé à partir d’une alerte Azure.

Comme décrit dans la section précédente, chaque type d’alerte a un schéma différent. Le script recueille les données du wekbhook dans le paramètre d’entrée du runbook `WebhookData` à partir d’une alerte. Il évalue ensuite la charge utile JSON pour déterminer le type d’alerte utilisé.

Dans cet exemple, il s’agit d’une alerte depuis une machine virtuelle. Il récupère les données de la machine virtuelle à partir de la charge utile, puis utilise ces informations pour arrêter la machine virtuelle. La connexion doit être configurée dans le compte Automation sur lequel le runbook est exécuté. Lorsque vous utilisez des alertes pour déclencher des runbooks, il est important de vérifier l’état de l’alerte dans le runbook déclenché. Le runbook se déclenche chaque fois que l’alerte change d’état. Les alertes ont plusieurs états, les deux plus courants étant `Activated` et `Resolved`. Recherchez cet état dans la logique de runbook pour vous assurer que le runbook ne s’exécute pas plusieurs fois. L’exemple de cet article montre uniquement comment rechercher des alertes `Activated`.

Le runbook utilise le [compte d’identification](automation-create-runas-account.md) **AzureRunAsConnection** pour s’authentifier auprès d’Azure pour effectuer l’action de gestion sur la machine virtuelle.

Utilisez cet exemple pour créer un runbook appelé **Stop-AzureVmInResponsetoVMAlert**. Vous pouvez modifier le script PowerShell et l’utiliser avec de nombreuses ressources différentes.

1. Allez sur votre compte Azure Automation.
2. Sous **automatisation des processus**, sélectionnez **Runbooks**.
3. En haut de la liste des procédures opérationnelles, sélectionnez **+ créer un runbook**.
4. Sur le **ajouter des runbooks** , entrez **Stop-AzureVmInResponsetoVMAlert** pour le nom du runbook. Pour le type de runbook, sélectionnez **PowerShell**. Sélectionnez ensuite **Create** (Créer).  
5. Copiez l’exemple PowerShell suivant dans le **modifier** page.

    ```powershell-interactive
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Sélectionnez **Publier** pour enregistrer et publier le runbook.

## <a name="create-the-alert"></a>Créer l’alerte

Alertes utilisent les groupes d’actions, qui sont des collections d’actions qui sont déclenchées par l’alerte. Un runbook n’est qu’une des nombreuses actions que vous pouvez utiliser avec les groupes d’actions.

1. Dans votre compte Automation, sélectionnez **alertes** sous **surveillance**.
1. Sélectionnez **+ nouvelle règle d’alerte**.
1. Cliquez sur **sélectionnez** sous **ressources**. Sur le **sélectionner une ressource** page, sélectionnez votre machine virtuelle à l’alerte issu de, puis cliquez sur **fait**.
1. Cliquez sur **ajouter une condition** sous **Condition**. Sélectionnez le signal que vous souhaitez utiliser, par exemple **pourcentage d’UC** et cliquez sur **fait**.
1. Sur le **logique du signal configurer** page, entrez votre **valeur de seuil** sous **logique d’alerte**, puis cliquez sur **fait**.
1. Sous **Groupes d’action**, sélectionnez **Créer**.
1. Sur le **ajouter un groupe d’action** page, donnez à votre groupe d’actions, un nom et un nom court.
1. Donnez un nom à l’action. Pour le type d’action, sélectionnez **Runbook Automation**.
1. Sélectionnez **modifier les détails**. Dans la page **Configurer un runbook**, sélectionnez **Utilisateur** sous **Source du runbook**.  
1. Sélectionnez votre **Abonnement** et votre **Compte Automation**, puis le runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Sélectionnez **Oui** pour **activer le schéma commun alerte**.
1. Sélectionnez **OK** pour créer le groupe d’actions.

    ![Page d’ajout d’un groupe d’actions](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Vous pouvez utiliser ce groupe d’actions dans les [alertes du journal d’activité](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) et les [alertes quasiment en temps réel](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) que vous avez créées.

1. Sous **détails d’une alerte**, ajoutez un nom de règle d’alerte et une description, puis cliquez sur **créer une règle d’alerte**.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le démarrage d’un runbook Automation avec un Webhook, consultez [Démarrer un runbook depuis un Webhook](automation-webhooks.md).
* Pour plus d’informations sur les différentes façons de démarrer un Runbook, voir [Démarrage d’un Runbook](automation-starting-a-runbook.md).
* Pour découvrir comment créer une alerte de journal d’activité, consultez [Créer des alertes de journal d’activité](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Pour apprendre à créer une alerte quasiment en temps réel, consultez [Créer une règle d’alerte avec le portail Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).