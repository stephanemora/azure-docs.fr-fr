---
title: Tutoriel `:` Utiliser une identité managée de machine virtuelle Windows pour accéder à Azure AD Graph
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité managée de machine virtuelle Windows affectée par le système pour accéder à l’API Azure AD Graph.
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
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ef467adb8970d410404c151d0028ee4cda92b9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183023"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Didacticiel : Utiliser une identité managée de machine virtuelle Windows affectée par le système pour accéder à l’API Azure AD Graph

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité managée de machine virtuelle Windows affectée par le système pour accéder à l’API Azure AD Graph afin de récupérer ses appartenances aux groupes. Les identités managées pour ressources Azure sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code.  Dans le cadre de ce didacticiel, vous allez effectuer une requête sur l’appartenance de l’identité de votre machine virtuelle à des groupes Azure AD. Les informations de groupe sont souvent utilisées pour les décisions d’autorisation, par exemple. En coulisses, l’identité managée de votre machine virtuelle est représentée par un **principal de service** dans Azure AD. Avant de procéder à la requête relative au groupe, ajoutez le principal de service représentant l’identité de la machine virtuelle à un groupe dans Azure AD. Vous pouvez effectuer cette opération à l’aide d’Azure PowerShell, d’Azure AD PowerShell ou de l’interface de ligne de commande Azure.

> [!div class="checklist"]
> * Se connecter à Azure AD
> * Ajouter l’identité de votre machine virtuelle à un groupe dans Azure AD 
> * Accorder à l’identité de la machine virtuelle l’accès à Azure AD Graph 
> * Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour appeler Azure AD Graph

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Pour que vous puissiez accorder à l’identité de la machine virtuelle l’accès à Azure AD Graph, le rôle **Administrateur général** doit être assigné à votre compte dans Azure AD.
- Installez la dernière version d’[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) si ce n’est pas déjà fait. 

## <a name="connect-to-azure-ad"></a>Se connecter à Azure AD

Vous devez vous connecter à Azure AD pour assigner la machine virtuelle à un groupe et pour accorder à la machine virtuelle l’autorisation de récupérer ses appartenances aux groupes. Vous pouvez utiliser l’applet de commande Connect-AzureAD directement, ou avec le paramètre TenantId si vous avez plusieurs locataires.

```powershell
Connect-AzureAD
```
Ou
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Ajouter l’identité de votre machine virtuelle à un groupe dans Azure AD

Lorsque vous avez activé l’identité managée affectée par le système sur la machine virtuelle Windows, un principal de service a été créé dans Azure AD.  Vous devez maintenant ajouter la machine virtuelle à un groupe.  L’exemple suivant montre comment créer un groupe dans Azure AD et ajouter le principal de service de votre machine virtuelle à ce groupe :

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Accorder à votre machine virtuelle l’accès à l’API Azure AD Graph

À l’aide d’identités managées pour ressources Azure, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD. L’API Microsoft Azure AD Graph prend en charge l’authentification Azure AD. Dans cette étape, vous allez accorder au principal de service de l’identité de votre machine virtuelle l’accès à Azure AD Graph, de sorte qu’il puisse effectuer une requête d’appartenance à des groupes. Les principaux de service doivent bénéficier de **permissions d’application** pour pouvoir accéder à Microsoft ou Azure AD Graph. Le type de permission d’application que vous devez accorder dépend de l’entité à laquelle vous souhaitez accéder dans MS ou Azure AD Graph.

Dans le cadre de ce didacticiel, vous allez accorder à l’identité de votre machine virtuelle l’autorisation d’effectuer une requête sur les appartenances à des groupes à l’aide de la permission d’application ```Directory.Read.All```. Pour que vous puissiez accorder cette autorisation, le rôle Administrateur général doit être assigné à votre compte d’utilisateur dans Azure AD. La méthode la plus courante pour accorder une permission d’application consiste à accéder à l’inscription de votre application dans le Portail Azure et à ajouter l’autorisation. Toutefois, les identités managées pour les ressources Azure n’inscrivent pas les objets d’application dans Azure AD. Elles inscrivent uniquement les principaux de service. Pour inscrire la permission d’application, vous allez utiliser l’outil de ligne de commande Azure AD PowerShell. 

Azure AD Graph :
- AppID du principal de service (utilisé quand vous accordez la permission d’application) : 00000002-0000-0000-c000-000000000000
- ID de ressource (utilisé lors d’une demande de jeton d’accès à partir des identités managées pour les ressources Azure) : https://graph.windows.net
- Référence de l’étendue d’autorisation : [Référence des autorisations pour Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Accorder des permissions d’application à l’aide d’Azure AD PowerShell

Vous avez besoin d’Azure AD PowerShell pour utiliser cette option. Si vous ne l’avez pas installé, [téléchargez la dernière version](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) avant de continuer.

1. Ouvrez une fenêtre PowerShell et connectez-vous à Azure AD :

   ```powershell
   Connect-AzureAD
   ```
   Pour vous connecter à une instance d’Azure Active Directory en particulier, utilisez le paramètre _TenantId_, comme suit :

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Exécutez les commandes PowerShell suivantes pour assigner la permission d’application ``Directory.Read.All`` au principal de service qui représente l’identité de votre machine virtuelle.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   Le résultat de la commande finale doit se présenter comme suit et retourner l’ID de l’assignation :
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Si l’appel à `New-AzureAdServiceAppRoleAssignment` échoue avec l’erreur `bad request, one or more properties are invalid`, il se peut que la permission d’application soit déjà assignée au principal de service de l’identité de la machine virtuelle. Vous pouvez utiliser les commandes PowerShell suivantes pour vérifier si la permission d’application existe déjà entre l’identité de votre machine virtuelle et Azure AD Graph :

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   Vous pouvez utiliser les commandes PowerShell suivantes pour obtenir la liste complète des permissions d’application accordées à Azure AD Graph :

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   Vous pouvez utiliser les commandes PowerShell suivantes pour supprimer des permissions d’application accordées à l’identité de votre machine virtuelle pour Azure AD Graph :

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle pour appeler Azure AD Graph 

Pour utiliser l’identité managée de machine virtuelle affectée par le système pour l’authentification auprès d’Azure AD Graph, vous devez effectuer les requêtes à partir de la machine virtuelle.

1. Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows, puis, dans le panneau **Vue d’ensemble**, cliquez sur **Connecter**.  
2. Entrez votre **Nom d’utilisateur** et le **Mot de passe** que vous avez utilisés quand vous avez créé la machine virtuelle Windows.
3. Maintenant que vous avez créé une Connexion Bureau à distance avec la machine virtuelle, ouvrez PowerShell dans la session à distance.  
4. À l’aide de Invoke-WebRequest de Powershell, envoyez une requête aux identités managées locales pour le point de terminaison de ressources Azure afin d’obtenir un jeton d’accès pour Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Convertissez la réponse d’objet JSON en objet PowerShell.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrayez le jeton d’accès de la réponse.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. À l’aide de l’ID d’objet du principal de service de l’identité de votre machine virtuelle (vous pouvez récupérer cette valeur à partir de la variable déclarée dans les étapes précédentes : ``$ManagedIdentitiesServicePrincipal.ObjectId``), vous pouvez interroger l’API Azure AD Graph pour récupérer ses appartenances aux groupes. Remplacez `<OBJECT ID>` par l’ID d’objet de l’étape précédente et <`ACCESS-TOKEN>` par le jeton d’accès obtenu précédemment :

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   La réponse contient le `Object ID` du groupe auquel vous avez ajouté le principal de service de votre machine virtuelle lors des étapes précédentes.

   Réponse :

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser une identité managée de machine virtuelle Windows affectée par le système pour accéder à Azure AD Graph.  Pour en savoir plus sur Azure AD Graph, consultez :

>[!div class="nextstepaction"]
>[API Graph Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
