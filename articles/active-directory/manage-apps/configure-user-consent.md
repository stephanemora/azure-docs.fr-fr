---
title: Configurer le consentement de l’utilisateur final pour une application à l’aide d’Azure AD
description: Apprenez à gérer comment et quand les utilisateurs peuvent donner leur consentement pour des applications qui auront accès aux données de votre organisation.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 433ff5498baeb4c31473e43fc4a5d24f4ba9fd1c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605156"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurer le consentement de l’utilisateur final pour une application

Vous pouvez intégrer vos applications avec la plateforme d’identités Microsoft pour permettre à des utilisateurs de se connecter à l’aide de leur compte professionnel ou scolaire, et d’accéder aux données de votre organisation afin d’offrir des expériences riches pilotées par les données.

Pour qu’une application puisse accéder aux données de votre organisation, un utilisateur doit lui accorder les autorisations nécessaires. Les différentes autorisations ont trait à des niveaux d’accès différents. Par défaut, tous les utilisateurs peuvent accorder à des applications des autorisations qui ne nécessitent pas de consentement de l’administrateur. Par exemple, par défaut, un utilisateur peut autoriser une application à accéder à sa boîte aux lettres, mais ne peut pas l’autoriser à accéder en lecture et en écriture à tous les fichiers de votre organisation.

Si les utilisateurs peuvent autoriser des applications à accéder à des données, il peuvent facilement acquérir des applications utiles et être productifs. Toutefois, dans certains cas, une telle configuration peut constituer un risque si elle n’est pas analysée et contrôlée avec soin.

## <a name="user-consent-settings"></a>Paramètres de consentement de l’utilisateur

Pour contrôler les cas où les utilisateurs peuvent autoriser des applications, choisissez la stratégie de consentement applicable à tous les utilisateurs. Voici les trois options de stratégie de consentement :

* **Désactiver le consentement de l’utilisateur** : les utilisateurs ne peuvent pas accorder d’autorisations aux applications. Les utilisateurs peuvent continuer à se connecter aux applications qu’ils ont précédemment autorisées ou que des administrateurs ont autorisées en leur nom, mais ils ne sont pas autorisés à accorder de nouvelles autorisations ou à autoriser de nouvelles applications. Seuls des utilisateurs auxquels est dévolu un rôle d’annuaire comprenant l’autorisation de donner un consentement sont en mesure de donner leur consentement à de nouvelles autorisations ou applications.

* **Les utilisateurs peuvent accorder des autorisations à des applications d’éditeurs vérifiés, mais uniquement les autorisations que vous sélectionnez (préversion)**  : les utilisateurs ne peuvent accorder des autorisations qu’à des applications publiées par un [serveur de publication vérifié](../develop/publisher-verification-overview.md) et inscrites dans votre locataire. Les utilisateurs ne peuvent accorder que des autorisations classifiées comme ayant un « Faible impact », ou « Faible risque ». Ce qui est considéré comme un faible risque pour une organisation, par exemple une application affichant l’adresse e-mail d’un utilisateur, peut être considéré comme un risque élevé pour une autre organisation. Pour cette raison, les autorisations de « faible risque » sont définies par l’administrateur du locataire.

  Veillez à [classifier les autorisations](#configure-permission-classifications-preview) pour sélectionner les autorisations que les utilisateurs peuvent accorder.

* **Les utilisateurs peuvent accorder des autorisations à toutes les applications** : cette option permet à tout utilisateur d’accorder à toute application toute autorisation ne nécessitant pas de consentement de l’administrateur. 

   Pour réduire le risque d’exposition à des applications malveillantes tentant d’amener les utilisateurs à leur accorder l’accès aux données de votre organisation, nous vous recommandons de permettre à l’utilisateur d’autoriser uniquement des applications publiées par un [serveur de publication vérifié](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Configurer les paramètres de consentement de l’utilisateur à partir du portail Azure

Pour configurer les paramètres de consentement de l’utilisateur via le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’[Administrateur général](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Consentement et autorisations** > **Paramètres de consentement de l’utilisateur**.
1. Sous **Consentement de l’utilisateur pour les applications**, sélectionnez le paramètre de consentement que vous souhaitez configurer pour tous les utilisateurs.
1. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Paramètres de consentement de l’utilisateur":::

> [!TIP]
> Songez à [activer le flux de travail de consentement administrateur](configure-admin-consent-workflow.md) pour autoriser les utilisateurs à demander à un administrateur la révision et l’approbation d’une application pour laquelle ils ne sont pas autorisés à donner leur consentement, par exemple, si le consentement de l’utilisateur a été désactivé ou si l’application demande des autorisations que l’utilisateur n’est pas autorisé à accorder.

### <a name="configure-user-consent-settings-using-powershell"></a>Configurer les paramètres de consentement de l’utilisateur à l’aide de PowerShell

Vous pouvez utiliser le dernier module Azure AD PowerShell en préversion, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), pour choisir la stratégie de consentement régissant le consentement de l’utilisateur pour les applications.

* **Désactiver le consentement de l’utilisateur** : pour désactiver le consentement de l’utilisateur, définissez les stratégies de consentement qui régissent le consentement de l’utilisateur de façon à ce qu’elles soient vides :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Autoriser le consentement de l’utilisateur à des applications de serveurs de publication vérifiés, pour des autorisations sélectionnées (préversion)**  : pour limiter le consentement de l’utilisateur aux applications de serveurs de publication vérifiés et inscrites dans votre locataire, et uniquement pour des autorisations que vous classifiez comme ayant un « Faible impact », configurez la stratégie de consentement intégrée nommée `microsoft-user-default-low` :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   N’oubliez pas de [classifier les autorisations](#configure-permission-classifications-preview) pour sélectionner les autorisations que les utilisateurs peuvent accorder.

* **Autoriser le consentement de l’utilisateur à toutes les applications** : pour autoriser le consentement de l’utilisateur à toutes les applications :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Cette option permet à tout utilisateur d’accorder à toute application toute autorisation ne nécessitant pas de consentement de l’administrateur. Nous vous recommandons de permettre aux utilisateurs d’autoriser uniquement des applications de serveurs de publication vérifiés.

## <a name="configure-permission-classifications-preview"></a>Configurer des classifications d’autorisations (préversion)

Les classifications d’autorisations vous permettent d’identifier l’impact des différentes autorisations en fonction des stratégies et des évaluations des risques de votre organisation. Par exemple, vous pouvez utiliser des classifications d’autorisations dans des stratégies de consentement afin d’identifier les autorisations que les utilisateurs peuvent accorder.

> [!NOTE]
> Actuellement, seule la classification d’autorisation ayant un « Faible impact » est prise en charge. Seules des autorisations déléguées qui ne nécessitent pas de consentement de l’administrateur peuvent être classifiées comme ayant un « Faible impact ».

### <a name="classify-permissions-using-the-azure-portal"></a>Classifier les autorisations à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’[Administrateur général](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Consentement et autorisations** > **Classifications d’autorisations**.
1. Choisissez **Ajouter des autorisations** pour classifier une autorisation autrement que comme ayant un « Faible impact ». 
1. Sélectionnez l’API, puis les autorisations déléguées.

Dans cet exemple, nous avons classifié l’ensemble minimal d’autorisations requises pour l’authentification unique :

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Paramètres de consentement de l’utilisateur":::

> [!TIP]
> Pour l’API Microsoft Graph, les autorisations minimales requises pour une authentification unique de base sont `openid`, `profile`, `User.Read` et `offline_access`. Avec ces autorisations, une application peut lire les détails du profil de l’utilisateur connecté, et maintenir cet accès même quand l’utilisateur n’utilise plus l’application.

### <a name="classify-permissions-using-powershell"></a>Classifier les autorisations à l’aide de PowerShell

Vous pouvez utiliser le dernier module Azure AD PowerShell en préversion, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), pour classifier les autorisations. Les classifications des autorisations sont configurées sur l’objet **ServicePrincipal** de l’API qui publie les autorisations.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Pour lire les classifications d’autorisations actuelles pour une API :

1. Récupérez l’objet **ServicePrincipal** pour l’API. Ici, nous récupérons l’objet ServicePrincipal pour l’API Microsoft Graph :

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Lisez les classifications d’autorisations déléguées pour l’API :

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Pour classifier une autorisation comme ayant un « Faible impact » :

1. Récupérez l’objet **ServicePrincipal** pour l’API. Ici, nous récupérons l’objet ServicePrincipal pour l’API Microsoft Graph :

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Recherchez l’autorisation déléguée que vous souhaitez classifier :

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Définissez la classification d’autorisation à l’aide du nom et de l’ID de l’autorisation :

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Pour supprimer une classification d’autorisation déléguée :

1. Récupérez l’objet **ServicePrincipal** pour l’API. Ici, nous récupérons l’objet ServicePrincipal pour l’API Microsoft Graph :

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Recherchez la classification d’autorisation déléguée à supprimer :

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Supprimez la classification d’autorisation :

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurer le consentement du propriétaire du groupe pour des applications qui accèdent aux données de groupe

Les propriétaires de groupes peuvent autoriser des applications telles que des applications publiées par des fournisseurs tiers à accéder aux données de votre organisation associées à un groupe. Par exemple, un propriétaire d’équipe dans Microsoft Teams peut autoriser une application à lire tous les messages Teams de l’équipe ou à consulter le profil de base des membres d’un groupe.

Vous pouvez configurer les utilisateurs pouvant autoriser des applications à accéder aux données de leurs groupes, ou désactiver cette fonctionnalité.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Configurer le consentement du propriétaire du groupe à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’[Administrateur général](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Sélectionnez **Azure Active Directory** > **Applications d’entreprise** > **Consentement et autorisations** > **Paramètres de consentement de l’utilisateur**.
3. Sous **Consentement du propriétaire du groupe pour les applications qui accèdent aux données**, sélectionnez l’option que vous souhaitez activer.
4. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.

Dans cet exemple, tous les propriétaires de groupes peuvent autoriser des applications qui accèdent aux données de leurs groupes :

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Paramètres de consentement de l’utilisateur":::

### <a name="configure-group-owner-consent-using-powershell"></a>Configurer le consentement du propriétaire du groupe à l’aide de PowerShell

Vous pouvez utiliser le Module Azure AD PowerShell Préversion, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), pour activer ou désactiver la capacité des propriétaires de groupes à autoriser des applications qui accèdent aux données de votre organisation pour les groupes qui leur appartiennent.

1. Vérifiez que vous utilisez le module [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true). Cette étape est importante si vous avez installé les modules [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) et [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Connectez-vous à Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Récupérez la valeur actuelle des paramètres d’annuaire **Consent Policy Settings** (Paramètres de stratégie de consentement) dans votre locataire. Pour cela, vous devez vérifier si les paramètres d’annuaire de cette fonctionnalité ont été créés et, si ce n’est pas le cas, utiliser les valeurs du Modèle de paramètres d’annuaire correspondant.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Prenez connaissance des valeurs des paramètres. Deux valeurs de paramètres définissent les utilisateurs qui seraient en mesure d’autoriser une application à accéder aux données de leur groupe :

    | Paramètre       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean | Indicateur spécifiant si les propriétaires de groupes sont autorisés à accorder des autorisations spécifiques de groupe. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Si _EnableGroupSpecificConsent_ a la valeur « True » et que cette valeur est définie sur l’ID d’objet d’un groupe, les membres du groupe identifié sont autorisés à accorder des autorisations spécifiques de groupes aux groupes dont ils sont propriétaires. |

1. Mettez à jour les valeurs des paramètres pour la configuration souhaitée :

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Enregistrez vos paramètres.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Configurer l’évolution du consentement en fonction des risques

La réaffectation du consentement en fonction des risques contribue à réduire l’exposition des utilisateurs aux applications malveillantes qui font des [demandes de consentement illicites](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Si Microsoft détecte une demande de consentement de l’utilisateur final à risque, la demande nécessitera une « évolution » vers le consentement de l’administrateur à la place. Cette fonctionnalité est activée par défaut, mais elle entraînera un changement de comportement uniquement lorsque le consentement de l’utilisateur final sera activé.

Quand une demande de consentement à risque est détectée, l’invite de consentement affiche un message indiquant que l’approbation de l’administrateur est requise. Si le [flux de travail de demande de consentement de l’administrateur](configure-admin-consent-workflow.md) est activé, l’utilisateur peut envoyer la demande à un administrateur pour révision ultérieure, directement à partir de l’invite de consentement. S’il n’est pas activé, le message suivant s’affiche :

* **AADSTS90094 :** &lt;clientAppDisplayName&gt; a besoin d’une autorisation pour accéder aux ressources de votre organisation, et cet accès ne peut être autorisé que par un administrateur. Veuillez demander à un administrateur d’accorder une autorisation pour cette application avant de pouvoir l’utiliser.

Dans ce cas, un événement d’audit est également journalisé avec la catégorie « ApplicationManagement », le type d’activité « Consent to application » et le motif de statut « Risky application detected ».

> [!IMPORTANT]
> Les administrateurs doivent [évaluer toutes les demandes de consentement](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) soigneusement avant d’approuver une demande, en particulier quand Microsoft a détecté un risque.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Désactiver ou réactiver l’évolution du consentement en fonction des risques à l’aide de PowerShell

Vous pouvez utiliser le module Azure AD PowerShell en préversion, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), pour désactiver la réaffectation à l’administrateur du consentement requis quand Microsoft détecte un risque, ou pour la réactiver si elle a été précédemment désactivée.

Pour ce faire, vous pouvez procéder de la manière décrite ci-dessus pour [configurer le consentement du propriétaire du groupe à l’aide de PowerShell](#configure-group-owner-consent-using-powershell), mais en modifiant la valeur des paramètres. Les étapes présentent trois différences : 

1. Prenez connaissance des valeurs des paramètres de l’évolution du consentement en fonction des risques :

    | Paramètre       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Indicateur qui spécifie si le consentement de l’utilisateur est bloqué lorsqu’une demande risquée est détectée. |

1. Substituez la valeur suivante dans l’étape 3 :

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Substituez l’un des éléments suivants dans l’étape 5 :

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

* [Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)
* [Découvrez comment gérer le consentement pour les applications et évaluer les demandes de consentement](manage-consent-requests.md)
* [Accorder le consentement de l’administrateur au niveau locataire à une application](grant-admin-consent.md)
* [Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/active-directory-v2-scopes.md)

Pour obtenir de l’aide ou trouver des réponses à vos questions :
* [Azure AD sur StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
