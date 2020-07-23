---
title: Gérer les autorisations des utilisateurs et des administrateurs - Azure Active Directory | Microsoft Docs
description: Apprenez à examiner et gérer les autorisations relatives à une application sur Azure AD. Par exemple, si vous souhaitez révoquer toutes les autorisations octroyées à une application.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277596"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Mesures relatives aux applications surprivilégiées ou suspectes dans Azure Active Directory

Apprenez à examiner et gérer les autorisations relatives à une application. Selon le scénario, cet article propose différentes mesures pour sécuriser votre application. Cela s'applique à toutes les applications qui ont été ajoutées à votre locataire Azure Active Directory (Azure AD), avec consentement utilisateur ou administrateur.

Pour plus d’informations sur le consentement des applications, consultez [Infrastructure de consentement d’Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prérequis

Pour pouvoir appliquer les mesures décrites ci-dessous, vous devez vous connecter en tant qu'administrateur général, administrateur d'application ou administrateur d'application cloud.

Pour restreindre l’accès aux applications, vous devez demander l’affectation d’utilisateurs, puis affecter des utilisateurs ou des groupes à l’application.  Pour plus d'informations, voir [Méthodes d'affectation d'utilisateurs et de groupes](methods-for-assigning-users-and-groups.md).

Vous pouvez accéder au portail Azure AD pour obtenir des scripts PowerShell contextuels permettant d'appliquer les mesures.
 
1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
2. Sélectionnez **Azure Active Directory (Azure Active Directory)**  > **Enterprise applications (Applications d’entreprise)** .
3. Sélectionnez l'application dont vous souhaitez restreindre l'accès.
4. Sélectionnez **Autorisations**. Sur la barre de commandes, sélectionnez **Passer en revue les autorisations**.

![Passer en revue les autorisations](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>Je souhaite contrôler l'accès à une application

Nous vous recommandons de restreindre l'accès à cette application en activant le paramètre Affectation d'utilisateurs.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
2. Sélectionnez **Azure Active Directory (Azure Active Directory)**  > **Enterprise applications (Applications d’entreprise)** .
3. Sélectionnez l'application dont vous souhaitez restreindre l'accès.
4. Sélectionnez **Propriétés**, puis définissez le paramètre Droits d'accès requis pour l'utilisateur sur Oui.
5. Sélectionnez **Utilisateur et groupes**, puis supprimez les utilisateurs indésirables affectés à l'application.
6. Affectez des utilisateurs et des groupes à l'application.

Facultatif : vous pouvez supprimer tous les utilisateurs affectés à l'application à l'aide de PowerShell.

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>Je souhaite révoquer toutes les autorisations relatives à une application

L'utilisation de PowerShell révoque toutes les autorisations octroyées à cette application.

> [!NOTE]
> La révocation de l'autorisation actuellement octroyée n'empêchera pas les utilisateurs de consentir à nouveau aux applications. Si vous souhaitez empêcher les utilisateurs de consentir à l'application, consultez [Configurer la manière dont les utilisateurs finaux consentent aux applications](configure-user-consent.md).

Facultatif : vous pouvez désactiver l'application pour empêcher les utilisateurs d'y accéder et pour empêcher l'application d'accéder à vos données.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
2. Sélectionnez **Azure Active Directory (Azure Active Directory)**  > **Enterprise applications (Applications d’entreprise)** .
3. Sélectionnez l'application dont vous souhaitez restreindre l'accès.
4. Sélectionnez **Propriétés**, puis définissez « Activé pour que les utilisateurs se connectent ? » sur Non.

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>L'application est suspecte et je souhaite en savoir plus

Nous vous recommandons de restreindre l'accès à cette application en activant le paramètre Affectation d'utilisateurs et vérifiant les autorisations que l'utilisateur et les administrateurs ont octroyées à l'application.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
3. Sélectionnez **Azure Active Directory (Azure Active Directory)**  > **Enterprise applications (Applications d’entreprise)** .
5. Sélectionnez l'application dont vous souhaitez restreindre l'accès.
6. Sélectionnez **Propriétés**, puis définissez le paramètre Droits d'accès requis pour l'utilisateur sur Oui.
7. Sélectionnez **Autorisations** et vérifiez les autorisations consenties par l'administrateur et l'utilisateur.

Facultatif :

- À l'aide de PowerShell, vous pouvez supprimer tous les utilisateurs affectés pour les empêcher de se connecter à l'application.
- À l'aide de PowerShell, vous pouvez invalider les jetons d'actualisation des utilisateurs qui ont accès à l'application.
- À l'aide de PowerShell, vous pouvez révoquer toutes les autorisations relatives à cette application.
- Vous pouvez désactiver l'application pour bloquer l'accès des utilisateurs et empêcher celle-ci d'accéder à vos données.


## <a name="application-is-malicious-and-im-compromised"></a>L'application est malveillante et je suis compromis

Nous vous recommandons de désactiver l'application pour empêcher les utilisateurs d'y accéder et pour empêcher l'application d'accéder à vos données. Si vous supprimez l'application, les utilisateurs finaux pourront de nouveau consentir à celle-ci et octroyer l'accès à vos données.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
2. Sélectionnez **Azure Active Directory (Azure Active Directory)**  > **Enterprise applications (Applications d’entreprise)** .
3. Sélectionnez l'application dont vous souhaitez restreindre l'accès.
4. Sélectionnez **Propriétés**, puis copiez l'ID d'objet.

### <a name="powershell-commands"></a>Commandes PowerShell


Récupérez l'ID d'objet du principal de service.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
2. Sélectionnez **Azure Active Directory (Azure Active Directory)**  > **Enterprise applications (Applications d’entreprise)** .
3. Sélectionnez l'application dont vous souhaitez restreindre l'accès.
4. Sélectionnez **Propriétés**, puis copiez l'ID d'objet.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Supprimez tous les utilisateurs affectés à l'application.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Révoquez les autorisations octroyées à l'application.

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Invalidez les jetons d'actualisation.
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Étapes suivantes
- [Gérer le consentement pour les applications et évaluer les demandes de consentement](manage-consent-requests.md)
- [Configurer le consentement de l'utilisateur](configure-user-consent.md)
- [Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)
