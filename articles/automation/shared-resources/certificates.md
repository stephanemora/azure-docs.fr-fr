---
title: Gérer les certificats dans Azure Automation
description: Les certificats sont stockés de manière sécurisée dans Azure Automation, et sont accessibles par des runbooks ou des configurations DSC pour s’authentifier auprès de ressources Azure et tierces. Cet article présente les certificats et leur utilisation dans la création textuelle et graphique.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732824"
---
# <a name="manage-certificates-in-azure-automation"></a>Gérer les certificats dans Azure Automation

Stockés de manière sécurisée dans Azure Automation, les certificats sont accessibles par des runbooks ou des configurations DSC avec l’activité [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) pour les ressources Azure Resource Manager. Le stockage sécurisé de certificats vous permet de créer des runbooks et des configurations DSC qui utilisent des certificats pour l’authentification, ou les ajoute aux ressources Azure ou tierces.

Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l’aide d’une clé unique générée pour chaque compte Automation. Cette clé est stockée dans un coffre de clés managé par le système. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource. Ce processus est managé par Azure Automation.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>Applets de commande Az Powershell

Pour Az, les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell. Elles font partie du [module Az.Automation](/powershell/azure/overview), utilisable dans les runbooks Automation et les configurations DSC.

|Applet de commande |Description|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Charge un certificat de service pour le service cloud spécifié.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Récupère des informations sur un certificat à utiliser dans un Runbook ou dans une configuration DSC. Vous pouvez uniquement récupérer le certificat lui-même à l’aide de l’activité `Get-AutomationCertificate`.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Crée un nouveau certificat dans Azure Automation.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Supprime un certificat dans Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Définit les propriétés d’un certificat existant, y compris le téléchargement du fichier de certificat et le mot de passe d’un fichier **.pfx**.|

## <a name="activities"></a>Activités

Les activités du tableau suivant sont utilisées pour accéder aux certificats dans un Runbook et dans des configurations DSC.

| Activités | Description |
|:---|:---|
|`Get-AutomationCertificate`|Obtient un certificat à utiliser dans un Runbook ou dans une configuration DSC. Retourne un objet [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Vous devez éviter d’utiliser des variables dans le paramètre `Name` de `Get-AutomationCertificate` dans un runbook ou une configuration DSC. L’utilisation de variables dans ce paramètre complique la découverte des dépendances entre les runbooks ou les configurations DSC et les variables Automation au moment de la conception.

## <a name="python-2-functions"></a>Fonctions Python 2

La fonction dans le tableau suivant est utilisée pour accéder aux certificats dans un runbook Python 2.

| Fonction | Description |
|:---|:---|
| `automationassets.get_automation_certificate` | Récupère des informations sur les ressources d’un certificat. |

> [!NOTE]
> Vous devez importer le module `automationassets` au début de votre runbook Python pour accéder aux fonctions des ressources.

## <a name="creating-a-new-certificate"></a>Création d’un certificat

Lorsque vous créez un certificat, vous téléchargez un fichier .cer ou .pfx dans Azure Automation. Si vous marquez le certificat comme exportable, vous pouvez également le transférer du magasin de certificats Azure Automation. S’il n’est pas exportable, il ne peut servir que pour la signature dans le runbook ou la configuration DSC. Azure Automation nécessite que le certificat provienne du **Fournisseur de services de chiffrement Microsoft Enhanced RSA et AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Créer un certificat avec le portail Azure

1. Dans votre compte Automation, cliquez sur **Ressources** pour ouvrir la page Ressources.
2. Sélectionnez **Certificats** pour ouvrir la page Certificats.
3. Cliquez sur **Ajouter un certificat** en haut de la page.
4. Tapez un nom pour le certificat dans le champ **Nom**.
5. Pour rechercher un fichier **.cer** ou **.pfx**, cliquez sur **Sélectionner un fichier** sous **Charger** un fichier de certificat. Si vous sélectionnez un fichier **.pfx**, spécifiez un mot de passe et indiquez s’il est exportable.
6. Cliquez sur **Créer** pour enregistrer la nouvelle ressource de certificat.

### <a name="create-a-new-certificate-with-powershell"></a>Créer un certificat avec PowerShell

L’exemple suivant démontre la création d’un certificat Automation et sont marquage comme exportable. Cet exemple importe un fichier **.pfx** existant.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Créer un certificat avec un modèle Resource Manager

L’exemple suivant montre comment déployer un certificat sur son compte Automation à l’aide d’un modèle Resource Manager au moyen de PowerShell :

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Utilisation d’un certificat

Pour utiliser un certificat, utilisez l’activité `Get-AutomationCertificate`. La cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) n’est pas utilisable, car elle retourne des informations sur la ressource de certificat, et non le certificat proprement dit.

### <a name="textual-runbook-example"></a>Exemple de runbook textuel

L’exemple suivant montre comment ajouter un certificat à un service cloud dans un Runbook. Dans cet exemple, le mot de passe est récupéré à partir d’une variable Automation chiffrée.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Exemple de runbook graphique

Ajoutez une activité `Get-AutomationCertificate` à un runbook graphique, cliquez avec le bouton droit sur le certificat dans le volet Bibliothèque et sélectionnez **Ajouter à la zone de dessin**.

![Ajouter un certificat à la zone de dessin](../media/certificates/automation-certificate-add-to-canvas.png)

L’image suivante montre un exemple d’utilisation d’un certificat dans un Runbook graphique. Il s’agit du même exemple que le précédent, qui montre comment ajouter un certificat à un service cloud à partir d’un runbook textuel.

![Exemple de création graphique](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exemple Python 2

L’exemple suivant montre comment accéder aux certificats dans les runbooks Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’utilisation des liens pour contrôler le flux logique d’activités que votre runbook effectue, consultez [Liens de création graphique](../automation-graphical-authoring-intro.md#links-and-workflow). 
