---
title: Utiliser une identité MSI attribuée à l’utilisateur sur une machine virtuelle Windows pour accéder à Azure Resource Manager
description: Ce tutoriel explique pas à pas comment utiliser une identité MSI (Managed Service Identity) attribuée à l’utilisateur sur une machine virtuelle Windows pour accéder à Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: 57455c5abf8c566f3935ece73d0b7470863936f8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699147"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutoriel : utiliser une identité MSI (Managed Service Identity) attribuée à l’utilisateur sur une machine virtuelle Windows pour accéder à Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce tutoriel explique comment créer une identité attribuée à l’utilisateur, comment l’attribuer à une machine virtuelle Windows, puis l’utiliser pour accéder à l’API d’Azure Resource Manager. Les identités MSI sont gérées automatiquement par Azure. Elles permettent l’authentification auprès de services prenant en charge Azure AD authentication, sans devoir nécessairement incorporer des informations d’identification à votre code. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows 
> * Créer une identité attribuée à l’utilisateur
> * Attribuer votre identité attribuée à l’utilisateur à votre machine virtuelle Windows
> * Accorder à l’identité attribuée à l’utilisateur l’accès à un groupe de ressources dans Azure Resource Manager 
> * Obtenir un jeton d’accès par l’identité attribuée à l’utilisateur et l’utiliser pour appeler Azure Resource Manager 
> * Lire les propriétés d’un groupe de ressources

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les étapes de création de ressources et de gestion de rôles nécessaires dans ce tutoriel, votre compte doit bénéficier des autorisations « Propriétaire » avec l’étendue appropriée (votre abonnement ou groupe de ressources). Si vous avez besoin d’aide concernant l’attribution de rôle, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources d’un abonnement Azure](/azure/role-based-access-control/role-assignments-portal).

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.7 ou ultérieure pour les besoins de ce tutoriel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-resource-group"></a>Créer un groupe de ressources

Dans l’exemple suivant, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Une fois le groupe de ressources créé, créez une machine virtuelle Windows.

Définissez le nom d’utilisateur et le mot de passe pour le compte d’administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```
Créez la machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée à l’utilisateur

Une identité attribuée à l’utilisateur est créée en tant que ressource Azure autonome. À l’aide de [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), Azure crée dans votre locataire Azure AD une identité qui peut être attribuée à une ou plusieurs instances de service Azure.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

La réponse contient les détails de l’identité attribuée à l’utilisateur qui a été créée, comme dans l’exemple suivant. Notez la valeur `Id` pour votre identité attribuée à l’utilisateur, car elle est utilisée à l’étape suivante :

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

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Attribuer l’identité attribuée à l’utilisateur à une machine virtuelle Windows

Une identité attribuée à l’utilisateur peut être utilisée par les clients sur plusieurs ressources Azure. Utilisez les commandes suivantes pour attribuer l’identité attribuée à l’utilisateur à une seule machine virtuelle. Utilisez la propriété `Id` retournée à l’étape précédente pour le paramètre `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Accorder à votre identité MSI attribuée à l’utilisateur l’accès à un groupe de ressources dans Azure Resource Manager 

Managed Service Identity (MSI) fournit des identités que votre code peut utiliser pour demander des jetons d’accès afin de s’authentifier auprès d’API de ressources qui prennent en charge l’authentification Azure AD. Dans ce tutoriel, votre code accède à l’API Azure Resource Manager. 

Avant que ce code puisse accéder à l’API, vous devez accorder à l’identité l’accès à une ressource dans Azure Resource Manager. Dans le cas présent, le groupe de ressources dans lequel la machine virtuelle est contenue. Mettez à jour la valeur de `<SUBSCRIPTION ID>` en fonction de votre environnement.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
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

5. À l’aide de l’applet de commande `Invoke-WebRequest` de Powershell, adressez une requête au point de terminaison MSI local pour obtenir un jeton d’accès pour Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Lire les propriétés d’un groupe de ressources

Utilisez le jeton d’accès récupéré à l’étape précédente pour accéder à Azure Resource Manager et lisez les propriétés du groupe de ressources dont vous avez accordé l’accès à votre identité attribuée à l’utilisateur. Remplacez <SUBSCRIPTION ID> par l’ID d’abonnement de votre environnement.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
La réponse contient les informations particulières du groupe de ressources, comme dans l’exemple suivant :

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert comment créer une identité assignée à l’utilisateur, puis l’attacher à une machine virtuelle Azure pour accéder à l’API Azure Resource Manager.  Pour en savoir plus sur Azure Resource Manager, consultez :

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)