---
title: Envoyer un e-mail à partir d’un runbook Azure Automation
description: Découvrez comment utiliser SendGrid pour envoyer un e-mail à partir d’un runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 4d825dee469497cbb56a91c913ff3ac51963058b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855693"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Tutoriel : Envoyer un e-mail à partir d’un runbook Azure Automation

Vous pouvez envoyer un e-mail à partir d’un runbook avec [SendGrid](https://sendgrid.com/solutions) à l’aide de PowerShell. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créez un coffre de clés Azure.
> * Stockez votre clé API `SendGrid` dans le coffre de clés.
> * Créez un runbook réutilisable qui récupère votre clé API et envoie un e-mail à l’aide d’une clé API stockée dans [Azure Key Vault](/azure/key-vault/).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure : Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Créer un compte SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Compte Automation](automation-offering-get-started.md) avec modules **Az** et [connexion Exécuter en tant que](automation-create-runas-account.md) pour stocker et exécuter le runbook.

## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Vous pouvez créer un coffre de clés Azure à l’aide du script PowerShell suivant. Remplacez les valeurs des variables par les valeurs propres à votre environnement. Utilisez l’interface intégrée Azure Cloud Shell via le bouton **Essayer** en haut à droite du bloc de code. Vous pouvez également copier et exécuter le code localement si le [module Azure PowerShell](/powershell/azure/install-az-ps) est installé sur votre ordinateur local.

> [!NOTE]
> Pour récupérer votre clé API, suivez les étapes décrites dans [Rechercher votre clé API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Pour découvrir d’autres méthodes de création d’un coffre de clés Azure et de stockage d’un secret, consultez [Démarrages rapides Key Vault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importer les modules requis dans votre compte Automation

Pour utiliser Azure Key Vault dans un runbook, votre compte Automation a besoin des modules suivants :

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Cliquez sur **Déployer sur Azure Automation** sous l’onglet Azure Automation sous **Options d’installation**. Cette action ouvre le portail Azure. Dans la page Importer, sélectionnez votre compte Automation et cliquez sur **OK**.

Pour découvrir d’autres méthodes d’ajout des modules requis, consultez [Importer des modules](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Créer le runbook pour envoyer un e-mail

Une fois que vous avez créé un coffre de clés et stocké votre clé API `SendGrid`, il est temps de créer le runbook qui récupère la clé API et envoie un e-mail.

Ce runbook utilise `AzureRunAsConnection` comme [compte d’identification](automation-create-runas-account.md) pour s’authentifier auprès d’Azure afin de récupérer le secret d’Azure Key Vault.

Utilisez cet exemple pour créer un runbook nommé **Send-GridMailMessage**. Vous pouvez modifier le script PowerShell et le réutiliser pour différents scénarios.

1. Allez sur votre compte Azure Automation.
2. Sous **Automatisation de processus**, sélectionnez **Runbooks**.
3. En haut de la liste des runbooks, sélectionnez **+ Créer un runbook**.
4. Dans la page **Ajouter un runbook**, entrez **Send- GridMailMessage** comme nom de runbook. Pour le type de runbook, sélectionnez **PowerShell**. Sélectionnez ensuite **Create** (Créer).
   ![Créer un runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Le runbook est créé et la page **Modifier le runbook PowerShell** s’ouvre.
   ![Modifier le runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copiez l’exemple PowerShell suivant dans la page **Modifier**. Vérifiez que `$VaultName` est le nom que vous avez spécifié quand vous avez créé votre coffre de clés.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Sélectionnez **Publier** pour enregistrer et publier le runbook.

Pour vérifier que le runbook s’exécute correctement, vous pouvez suivre les étapes décrites dans [Tester un runbook](manage-runbooks.md#test-a-runbook) ou [Démarrer un runbook](start-runbooks.md).
Si vous ne voyez pas votre e-mail de test, vérifiez vos dossiers de **courrier indésirable** et de **spam**.

## <a name="clean-up"></a>Nettoyer

Lorsqu’il n’est plus nécessaire, supprimez le runbook. Pour le faire, sélectionnez le runbook dans la liste de runbooks, puis cliquez sur **Supprimer**.

Supprimez le coffre de clés à l’aide de l’applet de commande [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

* Si vous rencontrez des problèmes lors de création ou du démarrage de votre runbook, consultez [Résoudre les erreurs avec les runbooks](./troubleshoot/runbooks.md).
* Pour mettre à jour les modules de votre compte Automation, consultez [Guide pratique pour mettre à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md)].
* Pour superviser l’exécution de runbook, consultez [Transférer l’état d’un travail et des flux de travail d’Automation vers des journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Pour déclencher un runbook à l’aide d’une alerte, consultez [Utiliser une alerte pour déclencher un runbook Azure Automation](automation-create-alert-triggered-runbook.md).
