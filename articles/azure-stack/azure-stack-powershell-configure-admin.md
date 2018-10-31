---
title: Connexion à Azure Stack en tant qu’opérateur à l’aide de PowerShell | Microsoft Docs
description: Apprenez à vous connecter à Azure Stack en tant qu’opérateur à l’aide de PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 10/24/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 63fd864ebda1cb766792b99a2725fb069d5b5f2a
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024067"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Connectez-vous à Azure Stack en tant qu’opérateur à l’aide de PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez configurer Azure Stack pour utiliser PowerShell en vue de gérer les ressources Azure Stack, par exemple créer des offres, des plans, des quotas et des alertes. Cette rubrique vous permet de configurer l’environnement de l’opérateur.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes prérequises suivantes à partir du [Kit de développement](.\asdk\asdk-connect.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté à l’ASDK par le biais d’un VPN](.\asdk\asdk-connect.md#connect-to-azure-stack-with-vpn). 

 - Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
 - Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurer l’environnement de l’opérateur et se connecter à Azure Stack

Configurez l’environnement de l’opérateur Azure Stack avec PowerShell. Exécutez l’un des scripts suivants : remplacez les valeurs tenantName Azure AD, le point de terminaison GraphAudience et la valeur ArmEndpoint par la configuration de votre propre environnement.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Tester la connectivité

Vous avez terminé l’installation. Utilisez PowerShell pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé **myResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes

 - [Développer des modèles pour Azure Stack](user/azure-stack-develop-templates.md)
 - [Déployer des modèles avec PowerShell](user/azure-stack-deploy-template-powershell.md)