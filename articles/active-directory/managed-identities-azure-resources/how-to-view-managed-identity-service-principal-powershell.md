---
title: Guide pratique pour afficher le principal du service d’une identité managée à l’aide de PowerShell
description: Instructions pas à pas pour afficher le principal du service d’une identité managée à l’aide de PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0cf1915e4013451dbb09f2c4af3df2bad6166475
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438947"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Afficher le principal du service d’une identité managée à l’aide de PowerShell

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à afficher le principal du service d’une identité managée à l’aide de PowerShell.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous pour créer un compte gratuit](https://azure.microsoft.com/free/).
- Activez [l’identité affectée par le système sur une machine virtuelle](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou une [application](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Si vous choisissez d’installer et d’utiliser PowerShell en local, ce didacticiel nécessite le module Azure PowerShell version 5.7.0 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). 
- Si vous exécutez PowerShell en local, vous devez également effectuer les opérations suivantes : 
    - Exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.
    - Installez la [dernière version de PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Exécutez `Install-Module -Name PowerShellGet -AllowPrerelease` pour obtenir la préversion du module `PowerShellGet` (vous devrez peut-être utiliser la commande `Exit` pour quitter la session PowerShell actuelle après avoir exécuté cette commande pour installer le module `AzureRM.ManagedServiceIdentity`).
    - Exécutez `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` pour installer la version préliminaire du module `AzureRM.ManagedServiceIdentity` afin d’effectuer les opérations d’identité managée affectée par l’utilisateur décrites dans cet article.

## <a name="view-the-service-principal"></a>Afficher le principal du service

La commande suivante indique comment afficher le principal du service d’une machine virtuelle ou d’une application sur laquelle est activée une identité affectée par le système. Remplacez `<VM or application name>` par vos propres valeurs.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’affichage des principaux du service Azure AD à l’aide de PowerShell, consultez [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


