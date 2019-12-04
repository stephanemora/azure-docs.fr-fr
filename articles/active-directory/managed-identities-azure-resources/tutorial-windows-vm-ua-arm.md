---
title: Tutoriel `:` Utiliser une identité managée pour accéder à Azure Resource Manager - Windows - Azure AD
description: Ce didacticiel explique pas à pas comment utiliser une identité managée attribuée par l’utilisateur sur une machine virtuelle Windows pour accéder à Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0999492f0d9c7d28da3ac896792fb2d7b898fd18
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224215"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Didacticiel : Utiliser une identité managée attribuée par l’utilisateur sur une machine virtuelle Windows pour accéder à Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce tutoriel explique comment créer une identité attribuée par l’utilisateur, comment l’attribuer à une machine virtuelle Windows, puis l’utiliser pour accéder à l’API d’Azure Resource Manager. Les identités MSI sont gérées automatiquement par Azure. Elles permettent l’authentification auprès de services prenant en charge Azure AD authentication, sans devoir nécessairement incorporer des informations d’identification à votre code. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une identité managée attribuée par l’utilisateur
> * Attribuer votre identité attribuée par l’utilisateur à votre machine virtuelle Windows
> * Accorder à l’identité attribuée par l’utilisateur l’accès à un groupe de ressources dans Azure Resource Manager 
> * Obtenir un jeton d’accès par l’identité attribuée par l’utilisateur et l’utiliser pour appeler Azure Resource Manager 
> * Lire les propriétés d’un groupe de ressources

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Connectez-vous au Portail Azure](https://portal.azure.com).

- [Créez une machine virtuelle Windows](/azure/virtual-machines/windows/quick-create-portal).

- Pour effectuer les étapes de création de ressources et de gestion de rôles nécessaires dans ce tutoriel, votre compte doit bénéficier des autorisations « Propriétaire » avec l’étendue appropriée (votre abonnement ou groupe de ressources). Si vous avez besoin d’aide concernant l’attribution de rôle, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources d’un abonnement Azure](/azure/role-based-access-control/role-assignments-portal).
- [Installez la dernière version du module Azure PowerShell](/powershell/azure/install-az-ps). 
- Exécutez `Connect-AzAccount` pour créer une connexion avec Azure.
- Installez la [dernière version de PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
- Exécutez `Install-Module -Name PowerShellGet -AllowPrerelease` pour obtenir la préversion du module `PowerShellGet` (vous devrez peut-être utiliser la commande `Exit` pour quitter la session PowerShell actuelle après avoir exécuté cette commande pour installer le module `Az.ManagedServiceIdentity`).
- Exécutez `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` pour installer la préversion du module `Az.ManagedServiceIdentity` afin d’effectuer les opérations d’identité attribuée par l’utilisateur dans cet article.

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée par l’utilisateur

Une identité attribuée par l’utilisateur est créée en tant que ressource Azure autonome. À l’aide de [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity), Azure crée dans votre locataire Azure AD une identité qui peut être attribuée à une ou plusieurs instances de service Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

La réponse contient les détails de l’identité attribuée par l’utilisateur qui a été créée, comme dans l’exemple suivant. Notez les valeurs `Id` et `ClientId` de votre identité attribuée par l’utilisateur, car elles seront utilisées dans les étapes suivantes :

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Attribuer l’identité attribuée par l’utilisateur à une machine virtuelle Windows

Une identité attribuée par l’utilisateur peut être utilisée par les clients sur plusieurs ressources Azure. Utilisez les commandes suivantes pour attribuer l’identité attribuée par l’utilisateur à une seule machine virtuelle. Utilisez la propriété `Id` retournée à l’étape précédente pour le paramètre `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Accorder à votre identité attribuée par l’utilisateur l’accès à un groupe de ressources dans Azure Resource Manager 

Les identités managées pour ressources Azure fournissent des identités que votre code peut utiliser pour demander des jetons d’accès afin de s’authentifier auprès d’API de ressources qui prennent en charge l’authentification Azure AD. Dans ce tutoriel, votre code accède à l’API Azure Resource Manager. 

Avant que ce code puisse accéder à l’API, vous devez accorder à l’identité l’accès à une ressource dans Azure Resource Manager. Dans le cas présent, le groupe de ressources dans lequel la machine virtuelle est contenue. Mettez à jour la valeur de `<SUBSCRIPTION ID>` en fonction de votre environnement.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

La réponse contient les détails de l’affectation de rôle qui a été créée, comme dans l’exemple suivant :

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour appeler Gestionnaire des ressources 

Pour la suite de ce tutoriel, nous allons utiliser la machine virtuelle que nous avons créée précédemment.

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

2. Dans le portail, accédez à **Machines virtuelles** et accédez à la machine virtuelle Windows. Ensuite, dans **Vue d’ensemble**, cliquez sur **Connecter**.

3. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez utilisés quand vous avez créé la machine virtuelle Windows.

4. Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez **PowerShell** dans la session à distance.

5. À l’aide de `Invoke-WebRequest` de Powershell, adressez une requête au point de terminaison des identités managées locales pour les ressources Azure pour obtenir un jeton d’accès pour Azure Resource Manager.  La valeur `client_id` est la valeur qui a été renvoyée lorsque vous avez [créé l’identité managée attribuée par l’utilisateur](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Lire les propriétés d’un groupe de ressources

Utilisez le jeton d’accès récupéré à l’étape précédente pour accéder à Azure Resource Manager et lisez les propriétés du groupe de ressources dont vous avez accordé l’accès à votre identité attribuée par l’utilisateur. Remplacez `<SUBSCRIPTION ID>` par l’ID d’abonnement de votre environnement.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
La réponse contient les informations particulières du groupe de ressources, comme dans l’exemple suivant :

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment créer une identité attribuée par l’utilisateur, puis l’attacher à une machine virtuelle Azure pour accéder à l’API Azure Resource Manager.  Pour en savoir plus sur Azure Resource Manager, consultez :

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
