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
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 4d6a7150c854ba89c3cd8eacd6b553c4b8e97343
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963347"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configurer des stratégies de durée de vie des jetons (préversion)
Vous pouvez spécifier la durée de vie d’un jeton SAML, d’accès ou d’ID émis par la plateforme d’identités Microsoft. Vous pouvez définir les durées de vie des jetons pour toutes les applications de votre organisation, pour une application mutualisée (plusieurs organisations) ou pour un principal de service spécifique de votre organisation. Pour plus d’informations, consultez [Durées de vie des jetons configurables](active-directory-configurable-token-lifetimes.md).

Dans cette section, nous allons étudier un scénario de stratégie courant qui peut vous aider à imposer de nouvelles règles pour la durée de vie des jetons. Dans l’exemple, vous apprenez à créer une stratégie qui nécessite que les utilisateurs s’authentifient plus fréquemment dans votre application web.

## <a name="get-started"></a>Bien démarrer
Pour commencer, suivez les étapes ci-dessous :

1. Téléchargez la dernière [version préliminaire publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Exécutez la commande `Connect` pour vous connecter à votre compte Administrateur Azure AD. Exécutez cette commande chaque fois que vous démarrez une nouvelle session.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Pour afficher toutes les stratégies qui ont été créées dans votre organisation, exécutez la cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true).  Tout résultat ayant une valeur de propriété définie qui diffère des valeurs par défaut indiquées ci-dessus relève du champ d’application de la mise hors service.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. Pour déterminer les applications et les principaux de service liés à une stratégie spécifique, vous avez identifié la cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) en remplaçant **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** par l’un de vos ID de stratégie. Vous pouvez ensuite décider s’il faut configurer la fréquence de connexion de l’accès conditionnel ou conserver les valeurs Azure AD par défaut.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Si votre locataire a des stratégies qui définissent des valeurs personnalisées pour les propriétés de configuration des jetons d’actualisation et de session, Microsoft vous recommande de mettre à jour ces stratégies avec des valeurs qui reflètent les valeurs par défaut décrites ci-dessus. Si aucune modification n’est apportée, Azure AD honorera automatiquement les valeurs par défaut.

## <a name="create-a-policy-for-web-sign-in"></a>Créer une stratégie de connexion web

Dans cet exemple, vous créez une stratégie qui nécessite que vos utilisateurs s’authentifient plus fréquemment dans votre application web. Cette stratégie définit la durée de vie des jetons d’accès/ID et l’âge maximal d’un jeton de session multifacteur pour le principal de service de votre application web.

1. Créez une stratégie de durée de vie des jetons.

    Cette stratégie de connexion web définit la durée de vie des jetons d’accès/ID et l’âge maximal de jeton de session à facteur unique sur 2 heures.

    1. Pour créer la stratégie, exécutez la cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Pour voir votre nouvelle stratégie et obtenir son **ObjectId**, exécutez la cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Affectez la stratégie au principal de service. Vous devez également obtenir **l’ID d’objet** de votre principal de service.

    1. Utilisez la cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) pour afficher tous les principaux de service de votre organisation ou un seul principal de service.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Lorsque vous avez le principal du service, exécutez la cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Créer des stratégies de durée de vie des jetons pour les jetons d’actualisation et de session
> [!IMPORTANT]
> Depuis mai 2020, les nouveaux locataires ne peuvent pas configurer les durées de vie des jetons d’actualisation et de session.  Les locataires avec une configuration existante peuvent modifier les stratégies des jetons d’actualisation et de session jusqu’au 30 janvier 2021.  Azure Active Directory cessera d’honorer la configuration actuelle des jetons d’actualisation et de session dans les stratégies après le 30 janvier 2021. Vous pourrez toujours configurer la durée de vie des jetons d’accès, SAML et d’ID après la mise hors service.
>
> Si vous devez continuer à définir la période de temps avant qu’un utilisateur soit invité à se connecter à nouveau, configurez la fréquence de connexion dans Accès conditionnel. Pour en savoir plus sur l’accès conditionnel, consultez [Configurer la gestion de session d’authentification avec l’accès conditionnel](/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).
>
> Si vous ne souhaitez pas utiliser l’accès conditionnel après la date de mise hors service, vos jetons d’actualisation et de session seront définis sur la [configuration par défaut](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement) à cette date, et vous ne pourrez plus modifier leur durée de vie.

### <a name="manage-an-organizations-default-policy"></a>Gérer la stratégie par défaut d’une organisation
Dans cet exemple, vous allez créer une stratégie qui permet à vos utilisateurs de se connecter moins fréquemment dans votre organisation entière. Pour ce faire, créez une stratégie de durée de vie des jetons pour les jetons d’actualisation à facteur unique, qui est appliquée dans toute votre organisation. Cette stratégie est appliquée à toutes les applications de votre organisation et à chaque principal de service pour lequel aucune stratégie n’est déjà définie.

1. Créez une stratégie de durée de vie des jetons.

    1. Définissez le jeton d’actualisation à facteur unique sur « Jusqu’à révocation ». Le jeton n’expire pas tant que l’accès n’est pas révoqué. Créez la définition de stratégie suivante :

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Pour créer la stratégie, exécutez la cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Pour supprimer un espace blanc, exécutez la cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Pour afficher votre nouvelle stratégie et obtenir son **ID d’objet**, exécutez la commande ci-après :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Mettez à jour la stratégie.

    Vous pouvez décider que la première stratégie que vous définissez dans cet exemple n’est pas aussi stricte que ce que votre service requiert. Pour définir votre jeton d’actualisation à facteur unique de façon à ce qu’il expire dans deux jours, exécutez la commande suivante :

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Créer une stratégie pour une application native qui appelle une API web
Dans cet exemple, vous créez une stratégie qui nécessite que vos utilisateurs s’authentifient moins fréquemment. La stratégie augmente également la durée pendant laquelle un utilisateur peut être inactif avant d’avoir à s’authentifier de nouveau. La stratégie est appliquée à l’API web. Lorsque l’application native demande l’API web en tant que ressource, cette stratégie est appliquée.

1. Créez une stratégie de durée de vie des jetons.

    1. Pour créer une stratégie stricte pour une API web, exécutez la cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Pour afficher votre nouvelle stratégie, exécutez la commande suivante :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Affectez la stratégie à votre API web. Vous devez également obtenir **l’ID d’objet** de votre application. Utilisez la cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) pour trouver l’**ObjectId** de votre application, ou utilisez le [portail Azure](https://portal.azure.com/).

    Obtenez l’**ObjectId** de votre application et assignez la stratégie :

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Gérer une stratégie avancée
Dans cet exemple, vous créez quelques stratégies, pour savoir comment fonctionne le système de priorité. Vous apprenez également à gérer plusieurs stratégies qui sont appliquées à divers objets.

1. Créez une stratégie de durée de vie des jetons.

    1. Pour créer une stratégie par défaut d’organisation qui définit la durée de vie des jetons d’actualisation à facteur unique sur 30 jours, exécutez la cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Pour voir votre nouvelle stratégie, exécutez la cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Affectez la stratégie à un principal de service.

    À présent, vous avez une stratégie qui s’applique à toute l’organisation. Vous souhaitez peut-être conserver cette stratégie de 30 jours pour un principal de service spécifique, mais changer la stratégie par défaut d’organisation pour qu’elle soit la limite supérieure du paramètre « Jusqu’à révocation ».

    1. Pour afficher tous les principaux de service de votre organisation, utilisez la cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

    1. Lorsque vous avez le principal du service, exécutez la cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Définissez l’indicateur `IsOrganizationDefault` sur false :

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Créez une stratégie par défaut d’organisation :

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    À présent, la stratégie d’origine est liée à votre principal de service, et la nouvelle stratégie est définie comme stratégie par défaut de votre organisation. Il est important de se rappeler que les stratégies appliquées aux principaux de service ont priorité sur les stratégies par défaut d’organisation.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [capacités de gestion des sessions d’authentification](../conditional-access/howto-conditional-access-session-lifetime.md) dans l’accès conditionnel Azure AD.
