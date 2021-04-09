---
title: Affecter une identité managée à un rôle d’application avec PowerShell – Azure AD
description: Instructions détaillées pour affecter à une identité managée l’accès au rôle d’une autre application, à l’aide de PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732063"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Affecter à une identité managée l’accès à un rôle d’application avec PowerShell

Les identités managées pour les ressources Azure fournissent aux services Azure une identité dans Azure Active Directory. Elles fonctionnent sans avoir besoin d’informations d’identification dans votre code. Les services Azure utilisent cette identité pour s’authentifier auprès des services prenant en charge l’authentification Azure AD. Les rôles d’application fournissent une forme de contrôle d’accès en fonction du rôle et permettent à un service d’implémenter des règles d’autorisation.

Dans cet article, vous allez apprendre à affecter une identité managée à un rôle d’application exposé par une autre application avec Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour exécuter les exemples de scripts, vous avez deux options :
    - Utiliser [Azure Cloud Shell](../../cloud-shell/overview.md), que vous pouvez ouvrir en utilisant le bouton **Essayer** dans le coin supérieur droit des blocs de code.
    - Exécuter les scripts localement en installant la dernière version d’[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Affecter à une identité managée l’accès au rôle d’application d’une autre application

1. Activez l’identité managée sur une ressource Azure, telle qu’une [machine virtuelle Azure](qs-configure-powershell-windows-vm.md).

1. Recherchez l’ID d’objet du principal de service de l’identité managée.

   **Pour une identité managée affectée par le système**, vous pouvez rechercher l’ID d’objet dans le portail Azure, dans la page **Identité** de la ressource. Vous pouvez également utiliser le script PowerShell suivant pour rechercher l’ID d’objet. Vous aurez besoin de l’ID de ressource de la ressource que vous avez créée à l’étape 1, qui est disponible dans le portail Azure, dans la page **Propriétés** de la ressource.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Pour une identité managée affectée par l’utilisateur**, vous pouvez rechercher l’ID d’objet de l’identité managée dans le portail Azure, dans la page **Vue d’ensemble** de la ressource. Vous pouvez également utiliser le script PowerShell suivant pour rechercher l’ID d’objet. Vous aurez besoin de l’ID de ressource de l’identité managée affectée par l’utilisateur.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Créez une nouvelle inscription d’application pour représenter le service auquel votre identité managée enverra une demande. Si l’API ou le service qui expose l’octroi de rôle d’application à l’identité managée a déjà un principal de service dans votre locataire Azure AD, ignorez cette étape. Par exemple, si vous souhaitez octroyer à l’identité managée l’accès à l’API Microsoft Graph, vous pouvez ignorer cette étape.

1. Recherchez l’ID d’objet du principal de service de l’application de service. Vous le trouverez en utilisant le portail Azure. Accédez à Azure Active Directory et ouvrez la page **Applications d’entreprise**, puis recherchez l’application et recherchez l’**ID d’objet**. Vous pouvez également rechercher l’ID d’objet du principal de service par son nom d’affichage à l’aide du script PowerShell suivant :

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Les noms d’affichage des applications ne sont pas uniques et vous devez vérifier que vous obtenez le principal de service de l’application appropriée.

1. Ajoutez un [rôle d’application](../develop/howto-add-app-roles-in-azure-ad-apps.md) à l’application que vous avez créée à l’étape 3. Vous pouvez créer ce rôle à l’aide du portail Azure ou de Microsoft Graph. Par exemple, vous pouvez ajouter un rôle d’application comme suit :

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Affectez le rôle d’application à l’identité managée. Vous aurez besoin des informations suivantes pour affecter le rôle d’application :
    * `managedIdentityObjectId` : ID d’objet du principal de service de l’identité managée, que vous avez trouvé à l’étape 2.
    * `serverServicePrincipalObjectId` : ID d’objet du principal de service de l’application serveur, que vous avez trouvé à l’étape 4.
    * `appRoleId` : ID du rôle d’application exposé par l’application serveur, que vous avez généré à l’étape 5 – dans cet exemple, l’ID du rôle d’application est `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6`.
   
   Exécutez le script PowerShell suivant pour ajouter l’attribution de rôle :

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Terminer le script

Cet exemple de script montre comment affecter une identité managée d’une application web Azure à un rôle d’application.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité managée pour les ressources Azure](overview.md)
- Pour activer l’identité managée sur une machine virtuelle Azure, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure à l’aide de PowerShell](qs-configure-powershell-windows-vm.md).