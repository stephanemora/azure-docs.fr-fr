---
title: Se connecter à Azure Stack en tant qu’utilisateur avec PowerShell | Microsoft Docs
description: Étapes pour vous connecter à l’instance Azure Stack de l’utilisateur.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 65071df1dff6e25683510956c2d82de5252b67ac
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107240"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Se connecter à Azure Stack en tant qu’utilisateur avec PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article vous fournit les étapes pour vous connecter à votre instance Azure Stack. Vous devez vous connecter pour gérer les ressources Azure Stack avec PowerShell. Par exemple, vous pouvez utiliser PowerShell pour vous abonner à des offres, créer des machines virtuelles et déployer des modèles Azure Resource Manager. afin d’exécuter les applets de commande PowerShell.

Pour l’installation :
  - Vérifiez que vous disposez des éléments requis.
  - Connexion à Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS). 
  - Inscrivez des fournisseurs de ressources.
  - Testez votre connectivité.

## <a name="prerequisites"></a>Prérequis

Vous pouvez configurer les prérequis suivants à partir du [kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).
* Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).

Veillez à remplacer les variables de script suivantes par des valeurs de votre configuration Azure Stack :

- **Nom du locataire Azure AD**  
  Le nom de votre client Azure AD utilisé pour gérer Azure Stack, par exemple, yourdirectory.onmicrosoft.com.
- **Point de terminaison Azure Resource Manager**  
  Pour le kit de développement Azure Stack, cette valeur est définie sur https://management.local.azurestack.external. Pour obtenir cette valeur pour les systèmes Azure Stack intégrés, contactez votre fournisseur de services.

## <a name="connect-with-azure-ad"></a>Connexion à Azure AD

```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Connexion à AD FS

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Inscrire des fournisseurs de ressources

Les fournisseurs de ressources ne sont pas automatiquement inscrits pour les nouveaux abonnements utilisateur qui n’ont aucune ressource déployée par le biais du portail. Vous pouvez inscrire explicitement un fournisseur de ressources en exécutant le script suivant :

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Tester la connectivité

Une fois que tout est configuré, testez la connectivité à l’aide de PowerShell pour créer des ressources dans Azure Stack. À des fins de test, créez un groupe de ressources pour une application et ajoutez une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « MyResourceGroup » :

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes

- [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)
- [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
- Si vous souhaitez configurer PowerShell pour l’environnement d’opérateur cloud, consultez l’article [Configurer l’environnement PowerShell de l’opérateur Azure Stack](../azure-stack-powershell-configure-admin.md).
