---
title: Durées de vie des jetons configurables
titleSuffix: Microsoft identity platform
description: Découvrez comment définir des durées de vie pour des jetons d’accès, SAML et d’ID émis par la Plateforme d’identité Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363969"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Durées de vie des jetons configurables dans la plateforme d’identité Microsoft (préversion)

Vous pouvez spécifier la durée de vie d’un jeton d’accès, d’ID ou SAML émis par la plateforme d’identité Microsoft. Vous pouvez définir les durées de vie des jetons pour toutes les applications de votre organisation, pour une application mutualisée (plusieurs organisations) ou pour un principal de service spécifique de votre organisation. Cependant, nous ne prenons pas en charge actuellement la configuration des durées de vie des jetons pour les [principaux de service d’identité managée](../managed-identities-azure-resources/overview.md).

Dans Azure AD, un objet de stratégie représente un ensemble de règles appliquées sur des applications individuelles ou sur toutes les applications d’une organisation. Chaque type de stratégie comporte une structure unique avec un ensemble de propriétés qui sont ensuite appliquées aux objets auxquels elles sont affectées.

Vous pouvez désigner une stratégie comme stratégie par défaut pour votre organisation. La stratégie est appliquée à toutes les applications de l’organisation tant qu’elle n’est pas remplacée par une stratégie pourvue d’une priorité plus élevée. Vous pouvez également affecter une stratégie à des applications spécifiques. L’ordre de priorité varie par type de stratégie.

Pour obtenir des exemples, consultez des [exemples de configuration des durées de vie des jetons](configure-token-lifetimes.md).

> [!NOTE]
> La stratégie de durée de vie des jetons configurable s’applique seulement aux clients mobiles et de poste de travail qui accèdent aux ressources SharePoint Online et OneDrive Entreprise ; elle ne s’applique pas aux sessions de navigateur web.
> Pour gérer la durée de vie des sessions de navigateur web pour SharePoint Online et OneDrive Entreprise, utilisez la fonctionnalité [Durée de vie de la session d’accès conditionnel](../conditional-access/howto-conditional-access-session-lifetime.md). Reportez-vous au [blog SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) pour en savoir plus sur la configuration des délais d’expiration des sessions inactives.

## <a name="license-requirements"></a>Conditions de licence :

L'utilisation de cette fonctionnalité nécessite une licence Azure AD Premium P1. Pour trouver la licence appropriée à vos besoins, consultez [Comparaison des fonctionnalités mises à la disposition générale des éditions Gratuite et Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Les clients avec [des licences Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) ont également accès aux fonctionnalités d’accès conditionnel.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Stratégies de durée de vie des jetons d’accès, SAML et d’ID

Vous pouvez définir les stratégies de durée de vie des jetons d’accès, SAML et d’ID.

### <a name="access-tokens"></a>Jetons d’accès

Les clients utilisent des jetons d’accès pour accéder à une ressource protégée. Un jeton d’accès peut uniquement être utilisé pour une combinaison spécifique d’utilisateur, de client et de ressource. Les jetons d’accès ne peuvent pas être révoqués et sont valides jusqu’à leur expiration. Un acteur malveillant qui a obtenu un jeton d’accès peut l’utiliser pour prolonger sa durée de vie. L’ajustement de la durée de vie des jetons d’accès représente un compromis entre l’amélioration des performances du système et l’augmentation de la durée pendant laquelle le client conserve un accès une fois son compte désactivé. Les performances du système sont améliorées en réduisant le nombre de fois où un client doit acquérir un nouveau jeton d’accès.  La valeur par défaut varie en fonction de l’application cliente qui demande le jeton. Par exemple, les clients prenant en charge l’évaluation continue de l’accès (CAE) qui négocient des sessions prenant en charge CAE verront une durée de vie de jeton plus longue (jusqu’à 28 heures). Après l’expiration du jeton d’accès, le client doit utiliser le jeton d’actualisation pour acquérir (généralement en mode silencieux) un nouveau jeton d’actualisation et un nouveau jeton d’accès.

### <a name="saml-tokens"></a>Jetons SAML

Les jetons SAML sont utilisés par de nombreuses applications SaaS basées sur le Web, et sont obtenus à l’aide du point de terminaison du protocole SAML2 d’Azure Active Directory. Ils sont également consommés par les applications utilisant WS-Federation. La durée de vie par défaut du jeton est d’une heure. Du point de vue d’une application, la période de validité du jeton est spécifiée par la valeur NotOnOrAfter de l’élément `<conditions …>` dans le jeton. Au terme de la période de validité du jeton, le client doit initier une nouvelle requête d’authentification, qui est souvent satisfaite sans connexion interactive en raison du jeton de session d’authentification unique (SSO).

La valeur de NotOnOrAfter peut être modifiée à l’aide du paramètre `AccessTokenLifetime` dans un élément `TokenLifetimePolicy`. Elle sera définie sur la durée de vie configurée dans la stratégie si elle existe, à laquelle sera ajouté un facteur de décalage de l’horloge de cinq minutes.

La valeur NotOnOrAfter de la confirmation d’objet spécifiée dans l’élément `<SubjectConfirmationData>` n’est pas affectée par la configuration de la durée de vie du jeton. 

### <a name="id-tokens"></a>Jetons d’ID

Les jetons d’ID sont transmis aux sites web et clients natifs. Les jetons d’ID contiennent des informations de profil sur un utilisateur. Un jeton d’ID est lié à une combinaison spécifique d’utilisateur et de client. Les jetons d’ID sont considérés comme valides jusqu’à leur expiration. En règle générale, une application web fait correspondre la durée de vie de session d’un utilisateur de l’application à la durée de vie du jeton d’ID émis pour l’utilisateur. Vous pouvez ajuster la durée de vie des jetons d’ID pour contrôler la fréquence à laquelle l’application web arrête la session de l’application et demande à l’utilisateur de s’authentifier à nouveau auprès de la plateforme d’identité Microsoft (en mode silencieux ou interactif).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Stratégies de durée de vie des jetons d’actualisation et de session

Vous ne pouvez pas définir les stratégies de durée de vie des jetons d’actualisation et de session.

> [!IMPORTANT]
> Depuis le 30 janvier 2021, vous ne pouvez plus configurer la durée de vie des jetons d’actualisation et de session. Azure Active Directory n’honore plus la configuration des jetons d’actualisation et de session dans les stratégies existantes.  Les nouveaux jetons émis après l’expiration des jetons existants sont maintenant [configurés par défaut](#configurable-token-lifetime-properties). Vous pouvez toujours configurer la durée de vie des jetons d’accès, SAML et d’ID après la mise hors service de la configuration des jetons d’actualisation et de session.
>
> La durée de vie du jeton existant ne changera pas. Lorsqu’un jeton expire, un nouveau jeton est émis en fonction de la valeur par défaut.
>
> Si vous devez continuer à définir la période de temps avant qu’un utilisateur soit invité à se connecter à nouveau, configurez la fréquence de connexion dans Accès conditionnel. Pour en savoir plus sur l’accès conditionnel, lisez l’article [Configurer la gestion de session d’authentification avec l’accès conditionnel](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="configurable-token-lifetime-properties"></a>Propriétés des durées de vie des jetons configurables
Une stratégie de durée de vie des jetons est un type d’objet de stratégie qui contient des règles de durée de vie des jetons. Cette stratégie détermine la durée pendant laquelle les jetons d’accès, SAML et d’ID sont considérés comme valides. Les stratégies de durée de vie des jetons ne peuvent pas être définies pour les jetons d’actualisation et de session. Si aucune stratégie n’est définie, le système applique la valeur de durée de vie par défaut.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Propriétés de la stratégie de durée de vie des jetons d’accès, ID et SAML2

Réduire la propriété Durée de vie de jeton d’accès atténue le risque qu’un jeton d’accès ou jeton d’ID soit utilisé par un acteur malveillant pour une période prolongée. (Ces jetons ne peuvent pas être révoqués.) L’inconvénient est que les performances sont affectées, car les jetons sont remplacés plus souvent.

Pour obtenir un exemple, consultez [Créer une stratégie de connexion Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

La configuration des jetons d’accès, ID et SAML2 est affectée par les propriétés suivantes et leurs valeurs définies respectives :

- **Propriété** : durée de vie du jeton d’accès
- **Chaîne de propriété de stratégie** : AccessTokenLifetime
- **Affecte** : jetons d’accès, jetons d’ID, jetons SAML2
- **Par défaut** :
    - Jetons d’accès : varie en fonction de l’application cliente qui demande le jeton. Par exemple, les clients prenant en charge l’évaluation continue de l’accès (CAE) qui négocient des sessions prenant en charge CAE verront une durée de vie de jeton plus longue (jusqu’à 28 heures).
    - Jetons d’ID, jetons SAML2 : une heure
- **Minimum** : 10 minutes
- **Maximum** : 1 journée

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Propriétés des stratégies de durée de vie des jetons d’actualisation et de session

La configuration des jetons d’actualisation et de session est affectée par les propriétés suivantes et leurs valeurs définies respectives. Après la mise hors service de la configuration des jetons d’actualisation et de session le 30 janvier 2021, Azure AD honorera uniquement les valeurs par défaut décrites ci-dessous. Si vous décidez de ne pas utiliser l’[accès conditionnel](../conditional-access/howto-conditional-access-session-lifetime.md) pour gérer la fréquence de connexion, vos jetons d’actualisation et de session seront définis sur la configuration par défaut à cette date, et vous ne pourrez plus modifier leur durée de vie.  

|Propriété   |Chaîne de propriété de stratégie    |Éléments affectés |Default |
|----------|-----------|------------|------------|
|Délai d’inactivité maximale de jeton d’actualisation |MaxInactiveTime  |Jetons d’actualisation |90 jours  |
|Âge maximal de jeton d’actualisation à facteur unique  |MaxAgeSingleFactor  |Jetons d’actualisation (pour tous les utilisateurs)  |Jusqu’à révocation  |
|Âge maximal de jeton d’actualisation multifacteur  |MaxAgeMultiFactor  |Jetons d’actualisation (pour tous les utilisateurs) |Jusqu’à révocation  |
|Âge maximal de jeton de session à facteur unique  |MaxAgeSessionSingleFactor |Jetons de session (persistants et non persistants)  |Jusqu’à révocation |
|Âge maximal de jeton de session multifacteur  |MaxAgeSessionMultiFactor  |Jetons de session (persistants et non persistants)  |Jusqu’à révocation |

Vous pouvez utiliser PowerShell pour rechercher les stratégies qui seront affectées par la mise hors service.  Utilisez les [applets de commande PowerShell](configure-token-lifetimes.md#get-started) pour voir toutes les stratégies créées dans votre organisation, ou pour rechercher les applications et les principaux de service liés à une stratégie spécifique.

## <a name="policy-evaluation-and-prioritization"></a>Définition des priorités et évaluation de la stratégie
Vous pouvez créer, puis affecter une stratégie de durée de vie à une application spécifique, à votre organisation et à vos principaux de service. Plusieurs stratégies peuvent s’appliquer à une application spécifique. La stratégie de durée de vie du jeton appliquée suit les règles ci-dessous :

* Si une stratégie est explicitement affectée au principal de service, elle est appliquée.
* Si aucune stratégie n’est explicitement affectée au principal de service, une stratégie explicitement affectée à l’organisation parente du principal de service est appliquée.
* Si aucune stratégie n’est explicitement affectée au principal de service ou à l’organisation, la stratégie affectée à l’application est appliquée.
* Si aucune stratégie n’a été affectée au principal du service, à l’organisation ou à l’objet d’application, les valeurs par défaut sont appliquées. (Consultez le tableau dans la section [Propriétés des durées de vie des jetons configurables](#configurable-token-lifetime-properties).)

Pour plus d’informations sur la relation existant entre les objets de principal de service et d’application, consultez [Objets application et principal du service dans Azure Active Directory](app-objects-and-service-principals.md).

La validité d’un jeton est évaluée lors de son utilisation. C’est la stratégie pourvue de la priorité la plus élevée sur l’application ouverte qui est appliquée.

Tous les intervalles de temps utilisés ici sont mis en forme selon C# [TimeSpan](/dotnet/api/system.timespan) object - D.HH:MM:SS.  Par conséquent, 80 jours et 30 minutes s’affichent sous la forme `80.00:30:00`.  La première valeur D peut être supprimée si elle est égale à zéro ; 90 minutes deviennent alors `00:90:00`.  

## <a name="cmdlet-reference"></a>Référence des applets de commande

Il s’agit d’applets de commande du [module Azure Active Directory PowerShell pour Graph (préversion)](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals).

### <a name="manage-policies"></a>Gérer les stratégies

Vous pouvez utiliser les applets de commande suivantes pour gérer les stratégies.

| Applet de commande | Description | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Permet de créer une stratégie. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Permet d’obtenir toutes les stratégies d’Azure AD ou une stratégie spécifiée. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Permet d’obtenir toutes les applications et tous les principaux de service liés à une stratégie. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Met à jour une stratégie existante. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Supprime la stratégie spécifiée. |

### <a name="application-policies"></a>Stratégies d’application
Vous pouvez utiliser les applets de commande suivantes pour les stratégies d’application.</br></br>

| Applet de commande | Description | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lie la stratégie spécifiée à une application. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Permet d’obtenir la stratégie affectée à une application. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Supprime une stratégie d’une application. |

### <a name="service-principal-policies"></a>Stratégies de principal du service
Vous pouvez utiliser les applets de commande suivantes pour les stratégies de principal de service.

| Applet de commande | Description | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lie la stratégie spécifiée à un principal de service. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Permet d’obtenir une stratégie liée au principal de service spécifié.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Supprime la stratégie du principal de service spécifié.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez des [exemples de configuration des durées de vie des jetons](configure-token-lifetimes.md).
