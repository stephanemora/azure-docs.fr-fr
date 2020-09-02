---
title: Restriction des autorisations d’accès des utilisateurs invités – Azure Active Directory | Microsoft Docs
description: Limitez les autorisations d’accès des utilisateurs invités avec le Portail Azure, PowerShell ou Microsoft Graph dans Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 08/20/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28125ff55fe6ab3e68d56dc26a074d0498c2b413
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798429"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Restriction des autorisations d’accès invité (préversion) dans Azure Active Directory

Azure Active Directory (Azure AD) offre la possibilité de limiter ce que peuvent voir les utilisateurs invités externes au sein de leur organisation dans Azure AD. Les utilisateurs invités sont définis par défaut sur un niveau d’autorisation limité dans Azure AD, tandis que les utilisateurs membres disposent de l’ensemble complet d’autorisations utilisateur par défaut. Cette préversion d’un nouveau niveau d’autorisation des utilisateurs invités dans les paramètres de collaboration externe de l’organisation Azure AD offre un accès encore plus restreint. Les choix d’accès invité possibles sont maintenant les suivants :

Niveau d’autorisation         | Niveau d’accès
----------------         | ------------
Identique aux utilisateurs membres     | Les invités ont le même accès aux ressources Azure AD que les utilisateurs membres
Accès limité (par défaut) | Les invités peuvent voir l’appartenance de tous les groupes non masqués
**Accès restreint (nouveau)**  | **Les invités ne peuvent voir l’appartenance d’aucun groupe**

Lorsque l’accès invité est restreint, les invités ne peuvent afficher que leur propre profil utilisateur. L’autorisation de voir d’autres utilisateurs n’est pas accordée, même si l’invité effectue une recherche par nom d’utilisateur principal ou par objectId. L’accès restreint empêche également les utilisateurs invités de consulter l’appartenance des groupes dont ils sont membres. Pour plus d’informations sur les autorisations utilisateur globales par défaut, consultez [Quelles sont les autorisations utilisateur par défaut dans Azure Active Directory ?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Autorisations et licences

Vous devez disposer du rôle Administrateur général pour configurer les paramètres de collaboration externe. Il n’existe aucune exigence de licence supplémentaire nécessaire pour restreindre l’accès invité.

## <a name="update-in-the-azure-portal"></a>Mise à jour sur le Portail Azure

Nous avons apporté des modifications aux contrôles existants du Portail Azure concernant les autorisations des utilisateurs invités.

> [!IMPORTANT]
> Pendant une brève période, les nouveaux contrôles du Portail concernant les autorisations des utilisateurs invités ne seront visibles qu’à l’aide de l’URL [https://aka.ms/AADRestrictedGuestAccess](https://aka.ms/AADRestrictedGuestAccess). Il reste possible d’utiliser PowerShell et Microsoft Graph pour définir les contrôles, auquel cas les modifications seront prises en compte par le Portail.

1. Connectez-vous au [Centre d’administration Azure AD](https://aka.ms/AADRestrictedGuestAccess) avec des autorisations d’administrateur général.
1. Sur la page de présentation **Azure Active Directory** de votre organisation, sélectionnez **Paramètres utilisateur**.
1. Sous **Utilisateurs externes**, sélectionnez **Gérer les paramètres de collaboration externe**.
1. Sur la page **Paramètres de collaboration externe** , sélectionnez l’option **L’accès utilisateur invité est limité aux propriétés et appartenances de leurs propres objets annuaire**.

    ![Page Paramètres de collaboration externe Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Sélectionnez **Enregistrer**. La prise en compte des modifications pour les utilisateurs invités peut prendre jusqu’à 15 minutes.

## <a name="update-with-the-microsoft-graph-api"></a>Mise à jour avec l’API Microsoft Graph

Nous avons ajouté une nouvelle API Microsoft Graph servant à configurer les autorisations invité dans une organisation Azure AD. Les appels d’API suivants permettent d’assigner n’importe quel niveau d’autorisation. La valeur de guestUserRoleId utilisée ici permet d’illustrer le paramètre d’utilisateur invité le plus restreint. Pour savoir comment définir des autorisations invité avec Microsoft Graph, consultez [Type de ressource authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Configuration initiale

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

La réponse doit être Réussite 204.

### <a name="updating-the-existing-value"></a>Mise à jour de la valeur existante

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

La réponse doit être Réussite 204.

### <a name="view-the-current-value"></a>Affichage de la valeur actuelle

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Exemple de réponse :

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Mise à jour avec des cmdlets PowerShell

Avec cette fonctionnalité, nous avons ajouté la possibilité de configurer les autorisations restreintes par le biais des cmdlets PowerShell v2. Les cmdlets PowerShell Get et Set ont été publiées dans la version 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Commande Get : Get-AzureADMSAuthorizationPolicy

Exemple :

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Commande Set : Set-AzureADMSAuthorizationPolicy

Exemple :

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Vous devez entrer authorizationPolicy comme ID lorsqu’il vous est demandé.

## <a name="supported-microsoft-365-services"></a>Services Microsoft 365 pris en charge

### <a name="supported-services"></a>Services pris en charge

« Pris en charge » signifie que l’expérience est conforme aux attentes ou, plus précisément, identique à l’expérience invité actuelle.

- Teams
- Outlook (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>Services non pris en charge actuellement

Le service non pris en charge actuellement risquent de présenter des problèmes de compatibilité avec le nouveau paramètre de restriction invité.

- Formulaires
- Planificateur dans Teams
- Application Planificateur
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)

Question | Réponse
-------- | ------
Où ces autorisations s’appliquent-elles ? | Ces autorisations au niveau des répertoires sont appliquées dans les services et les portails Azure AD, notamment Microsoft Graph, PowerShell v2, le Portail Azure et le portail Mes applications. Les services Microsoft 365 qui utilisent des groupes Office 365 pour les scénarios de collaboration (en particulier Outlook, Microsoft Teams et SharePoint) sont également affectés.
Quelles sont les parties du portail Mes applications affectées par cette fonctionnalité ? | La fonctionnalité de groupes du portail Mes applications tient compte de ces nouvelles autorisations. Cela comprend tous les chemins pour afficher la liste des groupes et les appartenances aux groupes dans Mes applications. Aucune modification n’a été apportée à la disponibilité des vignettes de groupe. La disponibilité des vignettes de groupe est toujours contrôlée par le paramètre de groupe existant sur le portail d’administration Azure.
Ces autorisations remplacent-elles les paramètres invités SharePoint ou Microsoft Teams ? | Non. Ces paramètres existants contrôlent toujours l’expérience et l’accès dans ces applications. Si, par exemple, vous constatez des problèmes dans SharePoint, vérifiez vos paramètres de partage externe.
Quels sont les problèmes de compatibilité connus dans le Planificateur et Yammer ? | <li>Si les autorisations sont définies sur « restreint », les invités qui sont connectés à l’application Planificateur ou accèdent au Planificateur dans Microsoft Teams n’ont pas accès à leurs plans ni à aucune tâche.<li>Lorsque les autorisations sont définies sur « restreint », les invités connectés à Yammer ne peuvent pas sortir du groupe.
Mes autorisations invité existantes seront-elles modifiées dans mon locataire ? | Aucune modification n’a été apportée à vos paramètres actuels. Nous maintenons une compatibilité descendante avec vos paramètres existants. Vous décidez quand vous souhaitez apporter des modifications.
Ces autorisations seront-elles définies par défaut ? | Non. Les autorisations par défaut existantes restent inchangées. Vous pouvez si vous le souhaitez les définir de manière plus restrictive.
Cette fonctionnalité est-elle soumise à des conditions de licence ? | Non, il n’existe aucune nouvelle exigence de licence associée à cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les autorisations d’invité existantes dans Azure AD, consultez [Quelles sont les autorisations utilisateur par défaut dans Azure Active Directory ?](../fundamentals/users-default-permissions.md).
- Pour connaître les méthodes de l’API Microsoft Graph permettant de restreindre l’accès invité, consultez [Type de ressource authorizationPolicy](/graph/api/resources/authorizationpolicy).
- Pour révoquer tous les accès d’un utilisateur, consultez [Révocation de l’accès utilisateur dans Azure AD](users-revoke-access.md).