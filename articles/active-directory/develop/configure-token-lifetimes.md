---
title: Définir les durées de vie de jetons
titleSuffix: Microsoft identity platform
description: Découvrez comment définir des durées de vie pour des jetons émis par la Plateforme d’identité Microsoft. Apprenez à gérer la stratégie par défaut d’une organisation, à créer une stratégie de connexion web, à créer une stratégie pour une application native qui appelle une API web et à gérer une stratégie avancée.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363884"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configurer des stratégies de durée de vie des jetons (préversion)
Vous pouvez spécifier la durée de vie d’un jeton SAML, d’accès ou d’ID émis par la plateforme d’identités Microsoft. Vous pouvez définir les durées de vie des jetons pour toutes les applications de votre organisation, pour une application mutualisée (plusieurs organisations) ou pour un principal de service spécifique de votre organisation. Pour plus d’informations, consultez [Durées de vie des jetons configurables](active-directory-configurable-token-lifetimes.md).

Dans cette section, nous allons étudier un scénario de stratégie courant qui peut vous aider à imposer de nouvelles règles pour la durée de vie des jetons. Dans l’exemple, vous apprenez à créer une stratégie qui nécessite que les utilisateurs s’authentifient plus fréquemment dans votre application web.

## <a name="get-started"></a>Bien démarrer

Pour commencer, téléchargez la dernière [préversion publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Ensuite, exécutez la commande `Connect` pour vous connecter à votre compte Administrateur Azure AD. Exécutez cette commande chaque fois que vous démarrez une nouvelle session.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Créer une stratégie de connexion web

Dans cet exemple, vous créez une stratégie qui nécessite que vos utilisateurs s’authentifient plus fréquemment dans votre application web. Cette stratégie définit la durée de vie des jetons d’ID/d’accès au principal du service de votre application web.

1. Créez une stratégie de durée de vie des jetons.

    Cette stratégie, pour la connexion web, définit la durée de vie du jeton d’accès/ID sur deux heures.

    Pour créer la stratégie, exécutez la cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Pour voir votre nouvelle stratégie et obtenir son **ObjectId**, exécutez la cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Affectez la stratégie au principal de service. Vous devez également obtenir **l’ID d’objet** de votre principal de service.

    Utilisez la cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) pour afficher tous les principaux de service de votre organisation ou un seul principal de service.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Lorsque vous avez le principal du service, exécutez la cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Afficher les stratégies existantes dans un locataire

Pour afficher toutes les stratégies qui ont été créées dans votre organisation, exécutez la cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true).  Tout résultat ayant une valeur de propriété définie qui diffère des valeurs par défaut indiquées ci-dessus relève du champ d’application de la mise hors service.

```powershell
Get-AzureADPolicy -All $true
```

Pour déterminer les applications et les principaux de service liés à une stratégie spécifique, vous avez identifié la cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) en remplaçant **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** par l’un de vos ID de stratégie. Vous pouvez ensuite décider s’il faut configurer la fréquence de connexion de l’accès conditionnel ou conserver les valeurs Azure AD par défaut.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Si votre locataire a des stratégies qui définissent des valeurs personnalisées pour les propriétés de configuration des jetons d’actualisation et de session, Microsoft vous recommande de mettre à jour ces stratégies avec des valeurs qui reflètent les valeurs par défaut décrites ci-dessus. Si aucune modification n’est apportée, Azure AD honorera automatiquement les valeurs par défaut.

### <a name="troubleshooting"></a>Dépannage
Certains utilisateurs ont signalé une erreur `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` après l’exécution de l’applet de commande `Get-AzureADPolicy`. Pour résoudre ce problème, exécutez la commande suivante pour désinstaller/réinstaller le module AzureAD, puis installer le module AzureADPreview :

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [capacités de gestion des sessions d’authentification](../conditional-access/howto-conditional-access-session-lifetime.md) dans l’accès conditionnel Azure AD.
