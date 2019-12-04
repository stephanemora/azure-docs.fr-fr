---
title: Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure AD
description: Comment sélectionner une application d’entreprise pour affecter un utilisateur ou un groupe dans Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a5135f97ffb7d29c9fd928382ca4344beaa654d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274736"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory

Pour attribuer un utilisateur ou un groupe à une application d’entreprise, vous devez avoir attribué l’un des rôles administrateur global, administrateur d’application, ou administrateur d’application cloud, ou être attribué comme propriétaire de l’application d’entreprise.  Dans le cas des applications Microsoft (par exemple, les applications Office 365), utilisez PowerShell pour affecter des utilisateurs à une application d’entreprise.

> [!NOTE]
> Pour les conditions de gestion des licences relatives aux composants traités dans le présent article, consultez la [page sur la tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Affecter un utilisateur à une application - portail

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
1. Sélectionnez **Tous les services**, entrez Azure Active Directory dans la zone de texte, puis sélectionnez **Entrée**.
1. Sélectionnez **Applications d’entreprise**.
1. Dans le volet **Applications d’entreprise - Toutes les applications**, vous voyez la liste des applications que vous pouvez gérer. Sélectionnez une application.
1. Dans le volet ***NomApplication*** (autrement dit, le volet portant le nom de l’application sélectionnée), choisissez **Utilisateurs et groupes**.
1. Dans le volet ***NomApplication***  **- Utilisateurs et groupes**, sélectionnez **Ajouter un utilisateur**.
1. Dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

   ![Affecter un utilisateur ou un groupe à l’application](./media/assign-user-or-group-access-portal/assign-users.png)

1. Dans le volet **Utilisateurs et groupes**, sélectionnez un ou plusieurs utilisateurs ou groupes dans la liste, puis cliquez sur le bouton **Sélectionner** en bas du volet.
1. Dans le volet **Ajouter une attribution**, sélectionnez **Rôle**. Ensuite, dans le volet **Sélectionner un rôle**, choisissez un rôle à appliquer aux utilisateurs ou groupes sélectionnés, puis cliquez sur **OK** en bas du volet.
1. Dans le volet **Ajouter une affectation**, sélectionnez le bouton **Affecter** en bas du volet. Les utilisateurs ou les groupes ont les autorisations définies par le rôle sélectionné pour cette application d’entreprise.

## <a name="allow-all-users-to-access-an-app---portal"></a>Autoriser tous les utilisateurs à accéder à une application - portail

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
1. Sélectionnez **Tous les services**, entrez Azure Active Directory dans la zone de texte, puis sélectionnez **Entrée**.
1. Sélectionnez **Applications d’entreprise**.
1. Dans le volet **Applications d’entreprise**, sélectionnez **Toutes les applications**. Cette action répertorie les applications que vous pouvez gérer.
1. Dans le volet **Applications d’entreprise - Toutes les applications**, sélectionnez une application.
1. Dans le volet ***NomApplication***, sélectionnez **Propriétés**.
1. Dans le volet ***NomApplication* - Propriétés**, définissez le paramètre **Affectation utilisateur requise ?** sur **Non**.

L’option **Affectation utilisateur requise ?**  :

- Si cette option est définie sur Oui, les utilisateurs doivent tout d’abord être affectés à cette application avant de pouvoir y accéder.
- Si cette option est définie sur Non, tous les utilisateurs qui naviguent directement vers l’URL de l’application ou vers l’URL de lien ciblé de l’application se voient accorder l’accès
- N’a pas d’impact sur l’apparition ou non d’une application dans le volet Accès à l’application. Pour montrer l’application dans le volet d’accès, vous devez affecter un utilisateur ou un groupe approprié à l’application.
- Fonctionne uniquement avec les applications cloud qui sont configurées pour l’authentification unique SAML, les applications de proxy d’application qui utilisent la pré-authentification Azure Active Directory, ou les applications créées directement sur la plateforme d’application Azure AD qui utilisent l’authentification OAuth 2.0 / OpenID Connect après qu’un utilisateur ou un administrateur a donné son consentement pour cette application. Consultez [Authentification unique pour les applications](what-is-single-sign-on.md). Consultez [Configurer le consentement de l’utilisateur final pour une application](configure-user-consent.md).
- Cette option n’a aucun effet quand une application est configurée pour les autres modes d’authentification unique.

## <a name="assign-a-user-to-an-app---powershell"></a>Affecter un utilisateur à une application - PowerShell

1. Ouvrez une invite de commandes Windows PowerShell avec des privilèges élevés.

   > [!NOTE]
   > Vous devez installer le module AzureAD (utilisez la commande `Install-Module -Name AzureAD`). Si vous y êtes invité, installez un module NuGet ou le nouveau module Azure Active Directory V2 PowerShell, tapez O et appuyez sur Entrée.

1. Exécutez `Connect-AzureAD` et connectez-vous avec un compte d’utilisateur Administrateur général.
1. Pour affecter un utilisateur et un rôle à une application, utilisez le script suivant :

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Pour plus d’informations sur la façon d’affecter un utilisateur à un rôle d’application, consultez la documentation de [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Pour assigner un groupe à une application d’entreprise, vous devez remplacer `Get-AzureADUser` par `Get-AzureADGroup`.

### <a name="example"></a>Exemples

Cet exemple affecte l’utilisateur Britta Simon à l’application [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) à l’aide de PowerShell.

1. Dans PowerShell, affectez les valeurs correspondantes aux variables $username, $app_name et $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Dans cet exemple, nous ignorons le nom exact du rôle d’application à attribuer à Britta Simon. Exécutez les commandes suivantes pour obtenir l’utilisateur ($user) et le principal du service ($sp) à l’aide de l’UPN de l’utilisateur et du nom complet du principal du service.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Exécutez la commande `$sp.AppRoles` afin d’afficher les rôles disponibles pour l’application Workplace Analytics. Dans cet exemple, vous souhaitez affecter à Britta Simon le rôle Analyst (Limited access).

   ![Présente les rôles disponibles pour un utilisateur à l’aide du rôle Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Affectez le nom de rôle à la variable `$app_role_name`.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Exécutez la commande suivante pour affecter l’utilisateur au rôle d’application :

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Voir tous mes groupes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](remove-user-or-group-access-portal.md)
- [Désactiver les connexions utilisateur pour une application d’entreprise](disable-user-sign-in-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](change-name-or-logo-portal.md)
