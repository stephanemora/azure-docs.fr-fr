---
title: Azure Service Bus – Mettre à jour automatiquement les unités de messagerie
description: Cet article vous explique de quelle façon vous pouvez utiliser un runbook Azure Automation pour mettre à jour automatiquement les unités de messagerie d’un espace de noms Service Bus.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341490"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Mettre à jour automatiquement les unités de messagerie d’un espace de noms Azure Service Bus 
Cet article vous explique de quelle façon vous pouvez mettre à jour automatiquement les [unités de messagerie](service-bus-premium-messaging.md) d’un espace de noms Service Bus en fonction de l’utilisation des ressources (UC ou mémoire). 

L’exemple de cet article montre comment augmenter les unités de messagerie pour un espace de noms Service Bus lorsque ce dernier utilise l’UC au-delà de 75 %. Les étapes principales sont les suivantes :

1. Créez un runbook Azure Automation avec un script PowerShell qui effectue un scale-up (augmente) des unités de messagerie pour un espace de noms Service Bus. 
2. Créez une alerte d’utilisation de l’UC sur l’espace de noms Service Bus, qui appelle le script PowerShell lorsque l’espace de noms utilise l’UC au-delà de 75 %. 

> [!IMPORTANT]
> Cet article s’applique uniquement au niveau de service **Premium** d’Azure Service Bus. 


## <a name="create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus
Créez un espace de noms Service Bus de niveau Premium. Suivez les étapes de l’article [Créer un espace de noms dans le portail Azure](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) pour créer l’espace de noms. 

## <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation
Créez un compte Azure Automation en suivant les instructions de l’article [Créer un compte Azure Automation](../automation/automation-quickstart-create-account.md). 

## <a name="import-azservice-module-from-gallery"></a>Importer le module Az.Service à partir de la galerie
Importez les modules `Az.Accounts` et `Az.ServiceBus` de la galerie dans le compte Automation. Le module `Az.ServiceBus` dépend du module `Az.Accounts`, ce dernier doit donc être installé en premier. 

Pour obtenir des instructions pas à pas, consultez [Importer un module à partir de la galerie des modules](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Créer et publier un runbook PowerShell

1. Créez un runbook PowerShell en suivant les instructions de l’article [Créer un runbook PowerShell](../automation/automation-quickstart-create-runbook.md). 

    Voici un exemple de script PowerShell que vous pouvez utiliser pour augmenter les unités de messagerie d’un espace de noms Service Bus. Ce script PowerShell dans un runbook Automation augmente les unités de messagerie de 1 à 2, 2 à 4 ou 4 à 8. Les valeurs autorisées pour cette propriété sont les suivantes : 1, 2, 4 et 8. Vous pouvez créer un autre runbook pour réduire le nombre d’unités de messagerie.

    Les paramètres **namespaceName** et **resourceGroupName** sont utilisés pour tester le script séparément du scénario d’alerte. 
    
    Le paramètre **WebHookData** permet à l’alerte de transmettre des informations telles que le nom du groupe de ressources, le nom de la ressource, etc. au runtime. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Testez le runbook](../automation/manage-runbooks.md#test-a-runbook) en spécifiant des valeurs pour les paramètres **namespaceName** et **resourceGroupName**. Vérifiez que les unités de messagerie de l’espace de noms sont mises à jour. 
3. Une fois le test terminé, [publiez le runbook](..//automation/manage-runbooks.md#publish-a-runbook) pour qu’il puisse être ajouté ultérieurement comme action pour une alerte sur l’espace de noms. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Créer une alerte sur l’espace de noms pour déclencher le runbook
Pour configurer une alerte sur votre espace de noms Service Bus afin de déclencher le runbook Automation que vous avez créé, consultez l’article [Utiliser une alerte pour déclencher un runbook Azure Automation](../automation/automation-create-alert-triggered-runbook.md). Par exemple, vous pouvez créer une alerte sur la métrique **Utilisation du processeur par espace de noms** ou **Utilisation de la taille mémoire par espace de noms** et ajouter une action pour déclencher le runbook Automation que vous avez créé. Pour plus d’informations sur ces métriques, consultez [Métriques d’utilisation des ressources](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

La procédure suivante montre comment créer une alerte qui déclenche le runbook Automation lorsque l’**utilisation de l’UC** de l’espace de noms dépasse **75 %** .

1. Sur la page **Espace de noms Service Bus** de votre espace de noms, sélectionnez **Alertes** dans le menu de gauche, puis sélectionnez **+ Nouvelle règle d’alerte** dans la barre d’outils. 
    
    ![Page Alertes : bouton Nouvelle règle d’alerte](./media/automate-update-messaging-units/alerts-page.png)
2. Dans la page **Créer une règle d’alerte**, cliquez sur **Sélectionner une condition**. 

    ![Page Créer une règle d’alerte : sélectionner une condition](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Sur la page **Configurer la logique du signal**, sélectionnez **UC** pour le signal. 

    ![Configurer la logique du signal : sélectionner UC](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Entrez une **valeur de seuil** (dans cet exemple, il s’agit de **75 %** ), puis sélectionnez **Terminé**. 

    ![Configurer le signal de l’UC](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. À présent, dans la page **Créer une règle d’alerte**, cliquez sur **Sélectionner un groupe d’actions**.
    
    ![Sélectionner un groupe d’actions](./media/automate-update-messaging-units/select-action-group-button.png)
6. Sélectionnez le bouton **Créer un groupe d’actions** dans la barre d’outils. 

    ![Bouton Créer un groupe d’actions](./media/automate-update-messaging-units/create-action-group-button.png)
7. Dans la page **Ajouter un groupe d’actions**, effectuez les étapes suivantes :
    1. Entrez le **nom** du groupe d’actions. 
    2. Entrez le **nom court** du groupe d’actions.
    3. Sélectionnez l’**abonnement** dans lequel vous souhaitez créer ce groupe d’actions.
    4. Sélectionnez le **groupe de ressources**. 
    5. Dans la section **Actions**, entrez le **nom de l’action**, puis sélectionnez **Runbook Automation** pour **Type d’action**. 

        ![Page d’ajout d’un groupe d’actions](./media/automate-update-messaging-units/add-action-group-page.png)
8. Dans la page **Configurer le runbook**, effectuez les étapes suivantes :
    1. Pour **Source du Runbook**, sélectionnez **Utilisateur**. 
    2. Pour **Abonnement**, sélectionnez votre **abonnement** Azure qui contient le compte Automation. 
    3. Pour **Compte Automation**, sélectionnez votre **compte Automation**.
    4. Pour **Runbook**, sélectionnez votre runbook. 
    5. Sélectionnez **OK** dans la page **Configurer le runbook**. 
        ![Configure runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. Sélectionnez **OK** dans la page **Ajouter un groupe d’actions**. 
5. Maintenant, dans la page **Créer une règle d’alerte**, entrez le **nom de la règle**, puis sélectionnez **Créer une règle d’alerte**. 
    ![Create alert rule](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Désormais, lorsque l’espace de noms utilise l’UC au-delà de 75 %, l’alerte déclenche le runbook Automation, ce qui augmente les unités de messagerie de l’espace de noms Service Bus. De même, vous pouvez créer une alerte pour un autre runbook Automation qui réduit le nombre d’unités de messagerie si l’espace de noms utilise l’UC en deçà de 25 %. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les unités de messagerie, consultez l’article relatif à la [messagerie Premium](service-bus-premium-messaging.md).
