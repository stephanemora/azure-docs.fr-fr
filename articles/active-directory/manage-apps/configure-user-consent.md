---
title: Configurer le consentement de l’utilisateur final pour une application à l’aide d’Azure AD
description: Apprenez à gérer comment et quand les utilisateurs peuvent donner leur consentement pour des applications qui auront accès aux données de votre organisation.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519620"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurer le consentement de l’utilisateur final pour une application

Des applications peuvent s’intégrer à la Plateforme d’identités Microsoft pour permettre aux utilisateurs de se connecter à l’aide de leur compte professionnel ou scolaire dans Azure Active Directory (Azure AD) et d’accéder aux données de votre organisation afin de fournir des expériences riches pilotées par les données. Des autorisations différentes permettent à l’application d’obtenir au niveau d’accès différent aux données de votre organisation.

Par défaut, les utilisateurs peuvent donner leur consentement pour des applications qui accèdent aux données de votre organisation, mais pour certaines autorisations uniquement. Par exemple, par défaut, un utilisateur peut donner son consentement afin d’autoriser une application à accéder à sa propre boîte aux lettres ou aux conversations « Teams » d’une équipe dont l’utilisateur est le propriétaire, mais il ne peut pas donner son consentement afin d’autoriser une application à accéder en lecture et en écriture à tous les sites SharePoint de votre organisation. Ce consentement, qui permet aux utilisateurs d’acquérir facilement des applications utiles qui s’intègrent à Microsoft 365, à Azure et à d’autres services, peut cependant représenter un risque s’il n’est pas utilisé et surveillé avec précaution.

Microsoft vous recommande de désactiver toutes les opérations futures de consentement d’utilisateurs pour aider à réduire votre surface d’exposition et à atténuer ce risque. Si le consentement de l’utilisateur est désactivé, les autorisations de consentement préalables sont toujours respectées, mais toutes les opérations de consentement futures doivent être effectuées par un administrateur. Le consentement de l’administrateur au niveau du locataire peut être demandé par les utilisateurs via un workflow de demande d’autorisation d’administrateur [intégré](configure-admin-consent-workflow.md) ou par le biais de vos propres processus de support. Consultez [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md) pour plus de détails.

## <a name="configure-user-consent-to-applications"></a>Configurer le consentement de l’utilisateur pour une application
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Désactiver ou activer le consentement de l’utilisateur à partir du portail Azure

Vous pouvez utiliser le portail Azure pour désactiver ou activer la capacité des utilisateurs à donner leur consentement pour des applications qui accèdent aux données de votre organisation :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’[Administrateur général](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Sélectionnez **Azure Active Directory**, **Applications d’entreprise**, puis **Paramètres utilisateur**.
3. Activez ou désactivez le consentement de l’utilisateur avec le contrôle nommé **Les utilisateurs peuvent autoriser les applications à accéder aux données de l’entreprise en leur nom**.
4. (Facultatif) Configurez un [workflow de demande de consentement administrateur](configure-admin-consent-workflow.md) pour vous assurer que les utilisateurs qui ne sont pas autorisés à donner leur consentement pour une application ne puissent pas demander une approbation.

> [!TIP]
> Pour autoriser les utilisateurs à demander à un administrateur la révision d’une application pour laquelle ils ne sont pas autorisés à donner leur consentement (par exemple, parce que le consentement de l’utilisateur a été désactivé ou parce que l’application demande des autorisations que l’utilisateur n’est pas autorisé à accorder), envisagez de [configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Désactiver ou activer le consentement de l’utilisateur à l’aide de PowerShell

Vous pouvez utiliser le Module Azure AD PowerShell v1 ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) pour activer ou désactiver la capacité des utilisateurs à donner leur consentement pour des applications qui accèdent aux données de votre organisation.

1. Connectez-vous à votre organisation en exécutant la cmdlet suivante :

    ```powershell
    Connect-MsolService
    ```

2. Vérifiez si le consentement de l’utilisateur est activé en exécutant la cmdlet suivante :

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Activez ou désactivez le consentement de l’utilisateur. Par exemple, pour désactiver le consentement de l’utilisateur, exécutez la cmdlet suivante :

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Configurer le consentement du propriétaire du groupe pour des applications qui accèdent aux données de groupe

> [!IMPORTANT]
> Les informations suivantes sont liées à une fonctionnalité à venir qui permettra aux propriétaires de groupes d’accorder à des applications l’accès aux données de leurs groupes. Lorsque cette fonctionnalité sera disponible, elle sera activée par défaut. Bien que cette fonctionnalité ne soit pas encore publiée, vous pouvez utiliser ces instructions pour désactiver la capacité avant sa publication.

Les propriétaires de groupes peuvent autoriser des applications (par exemple, des applications publiées par des fournisseurs tiers) à accéder aux données de votre organisation associées à un groupe. Par exemple, un propriétaire d’équipe (qui est le propriétaire du groupe Office 365 de l’équipe) peut autoriser une application à lire tous les messages « Teams » de l’équipe ou à répertorier le profil de base des membres d’un groupe.

> [!NOTE]
> Indépendamment de ce paramètre, un propriétaire de groupe est toujours autorisé à ajouter d’autres utilisateurs ou applications directement en tant que propriétaires de groupes.

### <a name="configure-group-owner-consent-using-powershell"></a>Configurer le consentement du propriétaire du groupe à l’aide de PowerShell

Vous pouvez utiliser le Module Azure AD PowerShell Préversion ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) pour activer ou désactiver la capacité des propriétaires de groupes à donner leur consentement pour des applications qui accèdent aux données de votre organisation pour les groupes dont ils sont propriétaires.

1. Vérifiez que vous utilisez bien le Module [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (cette étape est importante si vous avez installé le Module [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) et le Module [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Connectez-vous à Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Récupérez la valeur actuelle des paramètres d’annuaire *Consent Policy Settings* (Paramètres de stratégie de consentement) dans votre locataire. Pour cela, vous devez vérifier si les paramètres d’annuaire de cette fonctionnalité ont été créés et, si ce n’est pas le cas, utiliser les valeurs du Modèle de paramètres d’annuaire correspondant.

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

4. Prenez connaissance des valeurs des paramètres. Deux valeurs de paramètres définissent les utilisateurs qui seraient en mesure d’autoriser une application à accéder aux données de leur groupe :

    | Paramètre       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  Indicateur spécifiant si les propriétaires de groupes sont autorisés à accorder des autorisations spécifiques de groupe. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Si _EnableGroupSpecificConsent_ a la valeur « True » et que cette valeur est définie sur l’ID d’objet d’un groupe, les membres du groupe identifié seront autorisés à accorder des autorisations spécifiques de groupe aux groupes dont ils sont propriétaires. |

5. Mettez à jour les valeurs des paramètres pour la configuration souhaitée :

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

6. Enregistrez les paramètres.

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

L’évolution du consentement en fonction des risques contribue à réduire l’exposition des utilisateurs aux applications malveillantes qui effectuent des [demandes de consentement illicites](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Si Microsoft détecte une demande de consentement de l’utilisateur final à risque, la demande nécessitera une « évolution » vers le consentement de l’administrateur à la place. Cette fonctionnalité est activée par défaut, mais elle entraînera un changement de comportement uniquement lorsque le consentement de l’utilisateur final sera activé.

Quand une demande de consentement à risque est détectée, l’invite de consentement affiche un message indiquant que l’approbation de l’administrateur est requise. Si le [flux de travail de demande de consentement de l’administrateur](configure-admin-consent-workflow.md) est activé, l’utilisateur peut envoyer la demande à un administrateur pour révision ultérieure, directement à partir de l’invite de consentement. S’il n’est pas activé, le message suivant s’affiche :

* **AADSTS90094 :** &lt;clientAppDisplayName&gt; a besoin d’une autorisation pour accéder aux ressources de votre organisation, et cet accès ne peut être autorisé que par un administrateur. Veuillez demander à un administrateur d’accorder une autorisation pour cette application avant de pouvoir l’utiliser.

Dans ce cas, un événement d’audit est également journalisé avec la catégorie « ApplicationManagement », le type d’activité « Consent to application » et le motif du statut « Risky application detected ».

> [!IMPORTANT]
> Les administrateurs doivent [évaluer toutes les demandes de consentement](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) soigneusement avant d’approuver, en particulier quand Microsoft a détecté un risque.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Désactiver ou réactiver l’évolution du consentement en fonction des risques à l’aide de PowerShell

Vous pouvez utiliser le module de préversion Azure AD PowerShell ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) pour désactiver l’évolution vers le consentement de l’administrateur, requis dans les cas où Microsoft détecte un risque, ou pour le réactiver s’il a été précédemment désactivé.

Pour ce faire, vous pouvez utiliser les mêmes étapes que celles indiquées ci-dessus pour [configurer le consentement du propriétaire du groupe à l’aide de PowerShell](#configure-group-owner-consent-using-powershell), mais en substituant une valeur de paramètres différente. Les étapes présentent trois différences : 

1. Prenez connaissance des valeurs des paramètres de l’évolution du consentement en fonction des risques :

    | Paramètre       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Indicateur qui spécifie si le consentement de l’utilisateur est bloqué lorsqu’une demande risquée est détectée. |

2. Substituez la valeur suivante dans l’étape 3 :

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. Substituez l’un des éléments suivants dans l’étape 5 :

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Étapes suivantes

[Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)

[Découvrez comment gérer le consentement pour les applications et évaluer les demandes de consentement](manage-consent-requests.md)

[Accorder le consentement de l’administrateur au niveau locataire à une application](grant-admin-consent.md)

[Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD sur StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
