---
title: Utiliser Azure AD dans Azure Automation pour l’authentification auprès d’Azure
description: Découvrez comment utiliser Azure AD dans Azure Automation en tant que fournisseur pour l’authentification auprès d’Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548335"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Utiliser Azure AD dans Azure Automation pour l’authentification auprès d’Azure

Le service [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) active un certain nombre de tâches administratives, telles que la gestion des utilisateurs, la gestion des domaines et la configuration de l’authentification unique. Cet article explique comment utiliser Azure AD dans Azure Automation en tant que fournisseur pour l’authentification auprès d’Azure. 

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Installation des modules Azure AD

Vous pouvez activer Azure AD par le biais des modules PowerShell suivants :

* Azure Active Directory PowerShell pour Graph (modules AzureRM et Az). Azure Automation est fourni avec le module AzureRM et sa mise à niveau récente, le module Az. La fonctionnalité offre une authentification non interactive auprès d’Azure à l’aide de l’authentification basée sur les informations d’identification de l’utilisateur Azure AD (OrgId). Voir [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory pour Windows PowerShell (module MSOnline). Ce module permet les interactions avec Microsoft Online, notamment Office 365.

>[!NOTE]
>PowerShell Core ne prend pas en charge le module MSOnline. Pour utiliser les cmdlets du module, vous devez les exécuter à partir de Windows PowerShell. Vous êtes encouragé à utiliser la version la plus récente d’Azure Active Directory PowerShell pour les modules Graph au lieu du module MSOnline. 

### <a name="preinstallation"></a>Préinstallation

Avant d’installer les modules Azure AD sur votre ordinateur :

* Désinstallez toutes les versions précédentes du module AzureRM/Az et du module MSOnline. 

* Désinstallez l’Assistant de connexion Microsoft Online Services pour garantir le bon fonctionnement des nouveaux modules PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Installer les modules AzureRM et Az

>[!NOTE]
>Pour utiliser ces modules, vous devez utiliser PowerShell version 5.1 ou ultérieure avec une version 64 bits de Windows. 

1. Installez Windows Management Framework (WMF) 5.1. Voir [Installer et configurer WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installez AzureRM ou Az à l’aide des instructions de l’article [Installer Azure PowerShell sur Windows avec PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Installer le module MSOnline

>[!NOTE]
>Pour installer le module MSOnline, vous devez avoir un rôle Administrateur Office 365. Voir [À propos des rôles Administrateur](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Assurez-vous que la fonctionnalité Microsoft .NET Framework 3.5.x est activée sur votre ordinateur. Il est probable que votre ordinateur dispose d’une version plus récente, mais la compatibilité descendante avec les versions antérieures du .NET Framework peut être activée ou désactivée. 

2. Installez la version 64 bits de l’[Assistant de connexion Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Exécutez Windows PowerShell en tant qu’administrateur pour créer une invite de commandes Windows PowerShell avec élévation de privilèges.

4. Déployez Azure Active Directory à partir de [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Si vous êtes invité à installer le fournisseur NuGet, saisissez O, puis appuyez sur Entrée.

6. Si vous êtes invité à installer le module à partir de [PSGallery](https://www.powershellgallery.com/), saisissez O, puis appuyez sur Entrée.

### <a name="install-support-for-pscredential"></a>Installer la prise en charge de PSCredential

Azure Automation utilise la classe [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) pour représenter une ressource d’informations d’identification. Vos scripts récupèrent des objets `PSCredential` à l’aide de la cmdlet `Get-AutomationPSCredential`. Pour plus d’informations, consultez [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Attribution d’un administrateur d’abonnement

Vous devez désigner un administrateur pour l’abonnement Azure. Cette personne a le rôle Propriétaire pour l’étendue de l’abonnement. Voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Modification du mot de passe de l’utilisateur Azure AD

Pour modifier le mot de passe de l’utilisateur Azure AD :

1. Déconnectez-vous d’Azure.

2. Demander à l’administrateur de se connecter à Azure en tant que nouvel utilisateur Azure AD, en utilisant le nom d’utilisateur complet (y compris le domaine) et un mot de passe temporaire. 

3. Demandez à l’administrateur de modifier le mot de passe lorsqu’il y est invité.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Configuration d’Azure Automation pour gérer l’abonnement Azure à l’aide de l’utilisateur Azure AD

Pour qu’Azure Automation communique avec Azure AD, vous devez récupérer les informations d’identification associées à la connexion Azure à Azure AD. Il s’agit par exemple de l’ID de locataire, de l’ID d’abonnement, etc. Pour plus d’informations sur la connexion entre Azure et Azure AD, consultez [Connecter votre organisation à Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Création d’une ressource d’informations d’identification

À présent que les informations d’identification Azure pour Azure AD sont disponibles, il est temps de créer une ressource d’informations d’identification Azure Automation pour stocker en toute sécurité les informations d’identification d’Azure AD afin que les runbooks et les scripts Desire State Configuration (DSC) puissent y accéder. Vous pouvez effectuer cette opération dans le Portail Azure ou à l’aide de cmdlets PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Créer la ressource d’informations d’identification dans le Portail Azure

Vous pouvez utiliser le Portail Azure pour créer la ressource d’informations d’identification. Effectuez cette opération à partir de votre compte Automation en accédant à **Informations d’identification** sous **Ressources partagées**. Voir [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Créer la ressource d’informations d’identification avec Windows PowerShell

Pour préparer une nouvelle ressource d’informations d’identification dans Windows PowerShell, votre script crée d’abord un objet `PSCredential` à l’aide du nom d’utilisateur et du mot de passe attribués. Le script utilise ensuite cet objet pour créer la ressource par le biais d’un appel à la cmdlet [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0). Le script peut également appeler la cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) pour inviter l’utilisateur à saisir un nom et un mot de passe. Voir [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Gestion des ressources Azure à partir d’un runbook Azure Automation

Vous pouvez gérer les ressources Azure à partir de runbooks Azure Automation en utilisant la ressource d’informations d’identification. Vous trouverez ci-dessous un exemple de runbook PowerShell qui collecte la ressource d’informations d’identification à utiliser pour l’arrêt et le démarrage des machines virtuelles dans un abonnement Azure. Ce runbook utilise tout d’abord `Get-AutomationPSCredential` pour récupérer les informations d’identification à utiliser pour l’authentification auprès d’Azure. Il appelle ensuite la cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) pour se connecter à Azure à l’aide des informations d’identification. Le script utilise la cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) pour choisir l’abonnement à utiliser. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Étapes suivantes

* Vous trouverez des informations sur les ressources d’informations d’identification Automation dans [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md).
* Consultez [Gérer les modules dans Azure Automation](shared-resources/modules.md) pour savoir comment utiliser les modules Automation.
* Pour en savoir plus sur les méthodes qui peuvent être utilisées pour démarrer un runbook dans Azure Automation, consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md).
* Pour plus d’informations sur PowerShell, notamment des informations de référence sur le langage et des modules de formation, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).