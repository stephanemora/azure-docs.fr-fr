---
title: Guide pratique pour authentifier et autoriser par API dans Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment configurer l’authentification et l’autorisation pour une application personnalisée qui appelle l’API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472575"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Authentification et autorisation pour l’API Insights Azure Time Series

Cet article explique comment configurer l’authentification et l’autorisation utilisées dans une application personnalisée qui appelle l’API Azure Time Series Insights.

> [!TIP]
> En savoir plus sur [lui accordant l’accès aux données](./time-series-insights-data-access.md) à votre environnement Time Series Insights dans Azure Active Directory.

## <a name="service-principal"></a>Principal du service

Cela et les sections suivantes décrivent comment configurer une application pour accéder à l’API Time Series Insights pour le compte de l’application. L’application peut ensuite interroger ou publier des données de référence dans l’environnement Time Series Insights avec les informations d’identification de l’application plutôt que des informations d’identification de l’utilisateur.

## <a name="best-practices"></a>Bonnes pratiques

Quand vous avez une application qui doit accéder à Time Series Insights :

1. Configurer une application Azure Active Directory.
1. [Affecter des stratégies d’accès de données](./time-series-insights-data-access.md) dans l’environnement Time Series Insights.

À l’aide d’application plutôt que de vos informations d’identification de l’utilisateur est souhaitable depuis :

* Vous pouvez affecter des autorisations à l’identité d’application sont distincts à partir de vos propres autorisations. En règle générale, ces autorisations sont strictement limitées à ce que l’application nécessite. Par exemple, vous pouvez autoriser l’application à lire des données uniquement dans un environnement Time Series Insights particulier.
* Vous n’êtes pas obligé de modifier les informations d’identification de l’application, si vos responsabilités évoluent.
* Vous pouvez utiliser un certificat ou une clé d’application pour automatiser l’authentification lorsque vous exécutez un script sans assistance.

Les sections suivantes expliquent comment effectuer ces étapes via le portail Azure. L’article se concentre sur une application à locataire unique où l’application doit s’exécuter dans une seule organisation. Vous utiliserez généralement des applications à locataire unique pour les applications line of business qui s’exécutent dans votre organisation.

## <a name="set-up-summary"></a>Configurer le résumé

Le flux d’installation se compose de trois étapes :

1. Créer une application dans Azure Active Directory.
1. Autoriser cette application à accéder à l’environnement Time Series Insights.
1. Utiliser l’ID d’application et la clé pour acquérir un jeton à partir de `https://api.timeseries.azure.com/`. Le jeton permet ensuite d’appeler l’API Insights Azure Time Series.

## <a name="detailed-setup"></a>Programme d’installation détaillée

1. Dans le portail Azure, sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription d’application**.

   [![Nouvelle inscription d’application dans Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Donnez un nom à l’application, sélectionnez le type **Application web ou API**, sélectionnez un URI valide pour l’**URL de connexion**, puis cliquez sur **Créer**.

   [![Créer l’application dans Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Sélectionnez votre application nouvellement créée, puis copiez son ID d’application dans votre éditeur de texte favori.

   [![Copiez l’ID d’application](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Sélectionnez **Clés**, entrez le nom de clé, sélectionnez la date d’expiration, puis cliquez sur **Enregistrer**.

   [![Sélectionnez les clés de l’application](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Entrez le nom de la clé et d’expiration, puis cliquez sur Enregistrer](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Copiez la clé dans votre éditeur de texte.

   [![Copiez la clé d’application](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Pour l’environnement Time Series Insights, sélectionnez **Stratégies d’accès aux données**, puis cliquez sur **Ajouter**.

   [![Ajouter la nouvelle stratégie d’accès de données à l’environnement Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Dans le **sélectionner un utilisateur** boîte de dialogue, collez le nom de l’application (à partir de **étape 2**) ou ID d’application (à partir de **étape 3**).

   [![Rechercher une application dans la boîte de dialogue Sélectionner un utilisateur](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Sélectionnez le rôle (**lecteur** pour interroger des données, **contributeur** pour interroger des données et la modification des données de référence) et cliquez sur **OK**.

   [![Choisir lecteur ou contributeur dans la boîte de dialogue Sélectionner un rôle](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Enregistrer la stratégie en cliquant sur **OK**.

1. Utiliser l’ID d’application (à partir de **étape 3**) et clé d’application (à partir de **étape 5**) pour acquérir le jeton pour le compte de l’application. Le jeton peut ensuite être passé dans l’en-tête `Authorization` lorsque l’application appelle l’API Insights Azure Time Series.

    Si vous utilisez C#, vous pouvez utiliser le code suivant pour acquérir le jeton pour le compte de l’application. Pour obtenir un exemple complet, voir [Interroger des données à l’aide de C#](time-series-insights-query-data-csharp.md).

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

Utilisez le **ID d’Application** et **clé** dans votre application auprès d’Azure Time Series Insights.

## <a name="next-steps"></a>Étapes suivantes

- Pour un exemple de code qui appelle l’API Time Series Insights, voir [Interroger des données à l’aide de C#](time-series-insights-query-data-csharp.md).

- Pour obtenir des informations de référence sur l’API, consultez le [document de référence sur l’API de requête](/rest/api/time-series-insights/ga-query-api).

- Découvrez comment [créer un principal de service](../active-directory/develop/howto-create-service-principal-portal.md).
