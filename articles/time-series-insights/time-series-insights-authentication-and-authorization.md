---
title: Authentification et autorisation pour l’API - Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment configurer l’authentification et l’autorisation pour une application personnalisée qui appelle l’API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/14/2019
ms.custom: seodec18
ms.openlocfilehash: d47f846f77d3552288dfea43b417d8c60856f41a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327887"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Authentification et autorisation pour l’API Insights Azure Time Series

Ce document explique comment inscrire une application dans Azure Active Directory à l'aide du nouveau panneau Azure Active Directory. Les applications inscrites dans Azure Active Directory permettent aux utilisateurs de s’authentifier et d'utiliser l’API Azure Time Series Insight associée à un environnement Time Series Insights.

## <a name="service-principal"></a>Principal du service

Les sections suivantes expliquent comment configurer une application pour accéder à l’API Insights Azure Time Series pour le compte d'une application. L’application peut ensuite interroger ou publier des données de référence dans l’environnement Time Series Insights avec ses propres informations d’identification par le biais d'Azure Active Directory.

## <a name="summary-and-best-practices"></a>Résumé et meilleures pratiques

Le flux d'inscription de l'application Azure Active Directory implique trois étapes principales.

1. [Inscrire une application](#azure-active-directory-app-registration) dans Azure Active Directory.
1. Autoriser l'application à accéder aux [données dans l’environnement Time Series Insights](#granting-data-access).
1. Utiliser l'**ID d’application** et la **clé secrète client** pour obtenir un jeton depuis `https://api.timeseries.azure.com/` dans votre [application cliente](#client-app-initialization). Le jeton permet ensuite d’appeler l’API Insights Azure Time Series.

Comme indiqué à l'**étape 3**, séparer les informations d'identification de l'utilisateur et de l'application permet ce qui suit :

* Attribuer à l’identité de l’application des autorisations différentes de vos propres autorisations. En règle générale, ces autorisations sont strictement limitées à ce que l’application nécessite. Par exemple, vous pouvez autoriser l’application à lire uniquement des données dans un environnement Time Series Insights particulier.
* Isoler la sécurité de l’application de la création des informations d'identification d'un utilisateur en utilisant une **clé secrète client** ou un certificat de sécurité. Ainsi, les informations d’identification de l’application ne dépendent pas des informations d’identification d’un utilisateur spécifique. Si le rôle de l’utilisateur évolue, l’application ne requiert pas nécessairement de nouvelles informations d’identification ou une configuration supplémentaire. Si l’utilisateur modifie son mot de passe, tous les accès à l’application ne requièrent pas de nouvelles informations d’identification ou clés.
* Exécuter un script sans assistance en utilisant une **clé secrète client** ou un certificat de sécurité plutôt que les informations d’identification d’un utilisateur spécifique (ce qui implique qu'il soit présent).
* Utiliser un certificat de sécurité plutôt qu’un mot de passe pour sécuriser l’accès à votre API Azure Time Series Insights.

> [!IMPORTANT]
> Suivez le principe de **séparation des problèmes** (décrit dans ce scénario ci-dessus) lorsque vous configurez votre stratégie de sécurité Azure Time Series Insights.

> [!NOTE]
> * L'article se concentre sur une application à client unique conçue pour s’exécuter au sein d’une seule organisation.
> * Les applications à client unique sont généralement utilisées pour des applications métier exécutées au sein de votre organisation.

## <a name="detailed-setup"></a>Configuration détaillée

### <a name="azure-active-directory-app-registration"></a>Inscription d'application Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Octroi d'un accès aux données

1. Pour l’environnement Time Series Insights, sélectionnez **Stratégies d’accès aux données**, puis **Ajouter**.

   [![Ajouter une nouvelle stratégie d’accès aux données à l’environnement Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Dans la boîte de dialogue **Sélectionner un utilisateur**, collez le **Nom de l'application** ou l'**ID de l'application** à partir de la section d'inscription de l'application Azure Active Directory.

   [![Rechercher une application dans la boîte de dialogue Sélectionner un utilisateur](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Sélectionnez le rôle. Sélectionnez **Lecteur** pour interroger des données ou **Contributeur** pour interroger des données et modifier les données de référence. Sélectionnez **OK**.

   [![Choisir Lecteur ou Contributeur dans la boîte de dialogue Sélectionner un rôle utilisateur](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Enregistrez la stratégie en sélectionnant **OK**.

   > [!TIP]
   > Pour plus d’informations sur les options avancées d’accès aux données, consultez [Octroi d’un accès aux données](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Initialisation de l'application cliente

1. Utilisez l'**ID de l'application** et la **clé secrète client** (clé de l'application) à partir de la section d'inscription de l'application Azure Active Directory afin d'obtenir le jeton pour le compte de l’application.

    Dans C#, le code suivant permet d'obtenir le jeton pour le compte de l’application. Pour obtenir un exemple complet, voir [Interroger des données à l’aide de C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. Le jeton peut ensuite être passé dans l’en-tête `Authorization` lorsque l’application appelle l’API Insights Azure Time Series.

## <a name="common-headers-and-parameters"></a>Paramètres et en-têtes communs

Cette section décrit les en-têtes de requête HTTP et les paramètres qui sont couramment utilisés pour exécuter des requêtes sur les API Time Series Insights en disponibilité générale et en préversion. Les exigences spécifiques aux API sont décrites plus en détail dans la [documentation de référence sur l’API REST Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Authentication

Pour exécuter des requêtes authentifiées sur les [API REST Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/), un jeton de porteur OAuth 2.0 valide doit être passé dans l’[en-tête d’autorisation](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) à l’aide du client REST de votre choix (Postman, JavaScript, C#). 

> [!IMPORTANT]
> Le jeton doit être émis exactement vers la ressource `https://api.timeseries.azure.com/` (également appelée « audience » du jeton).
> * Votre **AuthURL** [Postman](https://www.getpostman.com/) sera donc conforme à : `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Pour savoir comment s’authentifier par programmation auprès des API Time Series Insights à l’aide du [SDK JavaScript Client](https://github.com/microsoft/tsiclient/blob/master/docs/API.md), consultez l’[exemple de visualisation hébergé du SDK client](https://tsiclientsample.azurewebsites.net/) Azure Time Series Insights ainsi que des graphes et autres graphiques.

### <a name="http-headers"></a>En-têtes HTTP

En-têtes de requête nécessaires :

- `Authorization` pour l’authentification et l’autorisation. Un jeton de porteur OAuth 2.0 valide doit être passé dans l’en-tête d’autorisation. Le jeton doit être émis exactement vers la ressource `https://api.timeseries.azure.com/` (également appelée « audience » du jeton).

En-têtes de requête facultatifs :

- `Content-type` - Seul `application/json` est pris en charge.
- `x-ms-client-request-id` - ID de requête client. Le service enregistre cette valeur. Permet au service de suivre l’opération d’un service à l’autre.
- `x-ms-client-session-id` - ID de session du client. Le service enregistre cette valeur. Permet au service de suivre un groupe d’opérations connexes d’un service à l’autre.
- `x-ms-client-application-name` - Nom de l’application qui a généré cette requête. Le service enregistre cette valeur.

En-têtes de réponse :

- `Content-type` - Seul `application/json` est pris en charge.
- `x-ms-request-id` - ID de requête généré par le serveur. Peut être utilisé pour demander à Microsoft d’examiner une requête.

### <a name="http-parameters"></a>Paramètres HTTP

Paramètres de chaîne de requête d’URL obligatoires :

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Paramètres de chaîne de requête d’URL facultatifs :

- `timeout=<timeout>` - Délai d’attente côté serveur pour l’exécution des requêtes. S’applique uniquement aux API [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) et [Get Environment Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api). La valeur du délai d’attente doit être au format de durée ISO 8601, par exemple `"PT20S"`, et doit être comprise dans la plage `1-30 s`. La valeur par défaut est `30 s`.

## <a name="next-steps"></a>Étapes suivantes

- Pour un exemple de code qui appelle l’API Time Series Insights mise à la disposition générale, consultez [Interroger des données à l’aide de C#](./time-series-insights-query-data-csharp.md).

- Pour des exemples de code de l'API Time Series Insights en préversion, consultez [Interroger des données en préversion à l’aide de données C# ](./time-series-insights-update-query-data-csharp.md).

- Pour obtenir des informations de référence sur l’API, consultez le [document de référence sur l’API de requête](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Découvrez comment [créer un principal de service](../active-directory/develop/howto-create-service-principal-portal.md).