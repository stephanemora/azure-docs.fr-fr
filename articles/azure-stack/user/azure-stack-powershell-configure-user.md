---
title: Configurer l’environnement PowerShell de l’utilisateur Azure Stack | Microsoft Docs
description: Configurer l’environnement PowerShell de l’utilisateur Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: d8b245666989552208f8cbcf0dddfdfc310f65e0
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "41946502"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configurer l’environnement PowerShell de l’utilisateur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

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
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Connexion à AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId
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