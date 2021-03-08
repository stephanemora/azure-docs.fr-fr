---
title: Authentification et autorisation pour l’API - Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment configurer l’authentification et l’autorisation pour une application personnalisée qui appelle l’API Azure Time Series Insights.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 02d9edd555566f86fd8bb09cf4acef4956ae53e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041210"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Authentification et autorisation pour l’API Insights Azure Time Series

Selon les besoins de votre entreprise, votre solution peut inclure une ou plusieurs applications clientes que vous utilisez pour interagir avec les [API](/rest/api/time-series-insights/reference-data-access-overview) de votre environnement Azure Time Series Insights. Azure Time Series Insights effectue l’authentification à l’aide de [jetons de sécurité Azure AD basés sur OAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Pour authentifier vos clients, vous devez obtenir un jeton du porteur avec les autorisations appropriées et le transmettre avec vos appels d’API. Ce document décrit plusieurs méthodes d’obtention d’informations d’identification que vous pouvez utiliser pour obtenir un jeton du porteur et vous authentifier.


  Ce document explique comment inscrire une application dans Azure Active Directory à l’aide du nouveau panneau Azure Active Directory. Les applications inscrites dans Azure Active Directory permettent aux utilisateurs de s'authentifier et d'utiliser l'API Azure Time Series Insight associée à un environnement Azure Time Series Insights.

## <a name="managed-identities"></a>Identités managées

Les sections suivantes décrivent comment utiliser une identité managée d’Azure Active Directory (Azure AD) pour accéder à l’API Azure Time Series Insights. Sur Azure, les identités managées évitent aux développeurs d’avoir à gérer les informations d’identification en fournissant une identité pour la ressource Azure dans Azure AD et en l’utilisant pour obtenir des jetons Azure Active Directory (Azure AD). Voici quelques-uns des avantages de l’utilisation des identités managées :

- Vous n’avez pas besoin de gérer les informations d’identification. Vous n’avez même pas accès à ces dernières.
- Vous pouvez utiliser des identités managées pour vous authentifier auprès d’un service Azure qui prend en charge l’authentification Azure AD, notamment Azure Key Vault.
- Les identités managées peuvent être utilisées sans coût supplémentaire.

Pour en savoir plus sur les deux types d’identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

Vous pouvez utiliser des identités gérées à partir de vos :

- Machines virtuelles Azure
- Azure App Services
- Azure Functions
- Instances de conteneur Azure
- Et bien plus...

Pour la liste complète, consultez [Services Azure qui prennent en charge les identités gérées pour les ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).

## <a name="azure-active-directory-app-registration"></a>Inscription d'application Azure Active Directory

Nous vous recommandons d’utiliser des identités managées autant que possible afin de ne pas avoir à gérer les informations d’identification. Si votre application cliente n’est pas hébergée sur un service Azure qui prend en charge les identités managées, vous pouvez inscrire votre application auprès d’un locataire Azure AD. Lorsque vous inscrivez votre application auprès d’Azure AD, vous créez une configuration d’identité pour votre application, ce qui lui permet de s’intégrer avec Azure AD. Quand vous inscrivez une application dans le [portail Azure](https://portal.azure.com/), vous choisissez s’il s’agit d’une application monolocataire (accessible uniquement dans votre locataire) ou multilocataire (accessible dans d’autres locataires). De plus, vous pouvez éventuellement définir un URI de redirection (auquel le jeton d’accès est envoyé).

Une fois l’inscription de l’application terminée, vous disposez d’une instance globale unique de l’application (objet application) qui réside dans votre locataire ou annuaire de base. Vous disposez également d’un ID global unique pour votre application (l’ID de l’application ou du client). Dans le portail, vous pouvez ensuite ajouter des secrets ou des certificats, ainsi que des étendues pour que votre application fonctionne, personnaliser votre application dans la boîte de dialogue de connexion, et bien plus.

Si vous inscrivez une application dans le portail, un objet application et un objet principal de service sont automatiquement créés dans votre locataire de base. Si vous inscrivez/créez une application à l’aide des API Microsoft Graph, la création de l’objet principal de service est une étape distincte. Un objet principal de service est requis pour demander des jetons.

Veillez à consulter la liste de vérification [Sécurité](../active-directory/develop/identity-platform-integration-checklist.md#security) pour votre application. En guise de meilleure pratique, il est recommandé d’utiliser des [informations d’identification de certificat](../active-directory/develop/active-directory-certificate-credentials.md), pas des informations d’identification de mot de passe (clés secrètes client).

Pour plus d’informations, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Étape 1 : créer votre identité managée ou inscription d’application

Une fois que vous avez déterminé si vous allez utiliser une identité managée ou une inscription d’application, l’étape suivante consiste à en approvisionner une.

### <a name="managed-identity"></a>Identité managée

Les étapes à suivre pour créer une identité managée varient en fonction de l’emplacement de votre code et de la création ou non d’une identité attribuée par le système ou par l’utilisateur. Pour comprendre la différence, consultez [Types d’identités managées](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Une fois que vous avez sélectionné votre type d’identité, recherchez et suivez le tutoriel approprié dans la [documentation](../active-directory/managed-identities-azure-resources/index.yml) sur les identités managées par Azure AD. Vous y trouverez des instructions sur la configuration des identités managées pour :

- [Machines virtuelles Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service et Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- Et bien plus...

### <a name="application-registration"></a>Inscription de l’application

Suivez les étapes décrites dans [Inscrire une application](../active-directory/develop/quickstart-register-app.md#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Étape 2 : accorder l’accès

Lorsque votre environnement Azure Time Series Insights reçoit une demande, le jeton du porteur de l’appelant est d’abord validé. Si la validation réussit, l’appelant a été authentifié. Ensuite, une autre vérification est effectuée pour s’assurer que l’appelant est autorisé à effectuer l’action demandée. Pour autoriser un utilisateur ou un principal de service, vous devez commencer par lui accorder un accès à l’environnement en lui attribuant le rôle Lecteur ou Contributeur.

- Pour accorder l’accès via l’interface utilisateur du [portail Azure](https://portal.azure.com/), suivez les instructions fournies dans l’article [Accorder l’accès aux données dans un environnement](concepts-access-policies.md) . Lors de la sélection de l’utilisateur, vous pouvez rechercher l’identité managée ou l’inscription d’application par son nom ou par son ID.

- Pour accorder l’accès à l’aide d’Azure CLI, exécutez la commande suivante. Pour obtenir la liste complète des commandes disponibles pour gérer l’accès, consultez la documentation [ici](/cli/azure/ext/timeseriesinsights/tsi/access-policy).

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> L’extension timeseriesinsights pour Azure CLI nécessite la version 2.11.0 ou une version ultérieure. L’extension est automatiquement installée la première fois que vous exécutez une commande az tsi access-policy. [Apprenez-en davantage](/cli/azure/azure-cli-extensions-overview) sur les extensions.

## <a name="step-3-requesting-tokens"></a>Étape 3 : demande de jetons

Une fois que votre identité managée ou votre inscription d’application ont été approvisionnées et qu’un rôle leur a été attribué, vous êtes prêt à commencer à les utiliser pour demander des jetons du porteur OAuth 2.0. La méthode que vous utilisez pour obtenir un jeton varie selon l’emplacement d’hébergement de votre code et le langage de votre choix. Lorsque vous spécifiez la ressource (également appelée « audience » du jeton), vous pouvez identifier Azure Time Series Insights par son URL ou son GUID :

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Si vous utilisez l’URL en tant qu’ID de ressource, le jeton doit être émis exactement à `https://api.timeseries.azure.com/`. La barre oblique de fin est obligatoire.

> * Si vous utilisez [Postman](https://www.getpostman.com/), votre **AuthURL** sera donc : `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` est valide, mais `https://api.timeseries.azure.com` ne l’est pas.

### <a name="managed-identities"></a>Identités managées

Lors de l’accès à partir de Azure App Service ou de Functions, suivez les instructions fournies dans [Obtenir des jetons pour les ressources Azure](../app-service/overview-managed-identity.md).

> [!TIP]
> Pour les fonctions et applications .NET, la façon la plus simple d’utiliser une identité managée consiste à recourir à la [Bibliothèque de client Azure Identity](/dotnet/api/overview/azure/identity-readme) pour .NET. 

Pour les applications et les fonctions .NET, la façon la plus simple pour utiliser une identité managée consiste à passer par le package Microsoft.Azure.Services.AppAuthentication. Ce package est populaire en raison de sa simplicité et de ses avantages en matière de sécurité. Les développeurs peuvent écrire du code une seule fois et laisser la bibliothèque de client déterminer comment s’authentifier en fonction de l’environnement d’application, selon qu’il s’agit d’une station de travail de développeur utilisant un compte de développeur ou déployée dans Azure à l’aide d’une identité de service managée. Pour obtenir des conseils sur la migration à partir de la bibliothèque AppAuthentication prédécesseur, lisez [Guide de migration AppAuthentication vers Azure.Identity](/dotnet/api/overview/azure/app-auth-migration).

Demandez un jeton pour Azure Time Series Insights en utilisant le langage C# et la bibliothèque de client d’identité Azure pour .NET :

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Inscriptions des applications

* Les développeurs peuvent utiliser la [Bibliothèque d’authentification Microsoft](../active-directory/develop/msal-overview.md) (MSAL) afin d’obtenir des jetons pour les inscriptions d’applications.

La MSAL est utilisable dans de nombreux scénarios d’application, dont les suivants :

* [Applications monopages (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Application web connectant un utilisateur et appelant une API web pour le compte de l’utilisateur](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [API web appelant une autre API web en aval pour le compte de l’utilisateur connecté](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Application de bureau appelant une API web pour le compte de l’utilisateur connecté](../active-directory/develop/scenario-desktop-overview.md)
* [Application mobile appelant une API web au nom de l’utilisateur qui s’est connecté de manière interactive](../active-directory/develop/scenario-mobile-overview.md)
* [Application démon de bureau/service appelant une API web pour son propre compte](../active-directory/develop/scenario-daemon-overview.md)

Pour obtenir un exemple de code C# montrant comment obtenir un jeton en tant qu’inscription d’application, et interroger des données à partir d’un environnement Gen2, consultez l’exemple d’application sur [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs).

> [!IMPORTANT]
> Si vous utilisez la [Bibliothèque d'authentification Active Directory (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) consultez la section [Migration vers MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Paramètres et en-têtes communs

Cette section décrit les en-têtes de requête HTTP et les paramètres qui sont couramment utilisés pour exécuter des requêtes sur les API Azure Time Series Insights Gen1 et Gen2. Les exigences spécifiques aux API sont décrites plus en détail dans la [documentation de référence sur l’API REST Azure Time Series Insights](/rest/api/time-series-insights/).

> [!TIP]
> Lisez les [Informations de référence sur l’API REST Azure](/rest/api/azure/) pour en savoir plus sur l’utilisation des API REST, la création de requêtes HTTP et la gestion des réponses HTTP.

### <a name="http-headers"></a>En-têtes HTTP

Les en-têtes de requête obligatoires sont décrits ci-dessous.

| En-tête de requête obligatoire | Description |
| --- | --- |
| Autorisation | Pour l’authentification auprès d’Azure Time Series Insights, un jeton du porteur OAuth 2.0 valide doit être passé dans l’[en-tête d’autorisation](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Pour savoir comment s’authentifier par programmation auprès des API Azure Time Series Insights à l’aide du [Kit de développement logiciel (SDK) JavaScript Client](https://github.com/microsoft/tsiclient/blob/master/docs/API.md), lisez l’[exemple de visualisation hébergé du Kit de développement logiciel (SDK) client](https://tsiclientsample.azurewebsites.net/) Azure Time Series Insights ainsi que des graphes et autres graphiques.

Les en-têtes de requête facultatifs sont décrits ci-dessous.

| En-tête de demande facultatif. | Description |
| --- | --- |
| Content-Type | Seul `application/json` est pris en charge. |
| x-ms-client-request-id | ID de requête client. Le service enregistre cette valeur. Permet au service de suivre l’opération d’un service à l’autre. |
| x-ms-client-session-id | ID de session du client. Le service enregistre cette valeur. Permet au service de suivre un groupe d’opérations connexes d’un service à l’autre. |
| x-ms-client-application-name | Nom de l’application qui a généré cette requête. Le service enregistre cette valeur. |

Les en-têtes de réponse facultatifs mais recommandés sont décrits ci-dessous.

| En-tête de réponse | Description |
| --- | --- |
| Content-Type | Seul `application/json` est pris en charge. |
| x-ms-request-id | ID de requête généré par le serveur. Peut être utilisé pour demander à Microsoft d’examiner une requête. |
| x-ms-property-not-found-behavior | En-tête de réponse facultatif de l’API GA. Les valeurs possibles sont `ThrowError` (par défaut) ou `UseNull`. |

### <a name="http-parameters"></a>Paramètres HTTP

> [!TIP]
> Pour plus d’informations sur les informations de requête obligatoires et facultatives, consultez la [documentation de référence](/rest/api/time-series-insights/).

Les paramètres de chaîne de requête d’URL obligatoires dépendent de la version de l’API.

| Libérer | Valeurs de version d’API |
| --- |  --- |
| Première génération | `api-version=2016-12-12`|
| Deuxième génération | `api-version=2020-07-31`|

Les paramètres de chaîne de requête d’URL facultatifs incluent la définition d’un délai d’expiration pour les durées d’exécution des requêtes HTTP.

| Paramètre de requête facultatif | Description | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Délai d’attente côté serveur pour l’exécution des requêtes HTTP. S’applique uniquement aux API [Get Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) et [Get Environment Aggregates](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api). La valeur du délai d’attente doit être au format de durée ISO 8601, par exemple `"PT20S"`, et doit être comprise dans la plage `1-30 s`. La valeur par défaut est `30 s`. | Première génération |
| `storeType=<storeType>` | Pour les environnements Gen2 avec le magasin chaud activé, la requête peut être exécutée sur `WarmStore` ou `ColdStore`. Ce paramètre dans la requête définit le magasin sur lequel la requête doit être exécutée. S’il n’est pas défini, la requête est exécutée sur le magasin Cold. Pour interroger le magasin Warm, **storeType** doit être défini sur `WarmStore`. S’il n’est pas défini, la requête est exécutée par rapport au magasin Cold. | Deuxième génération |

## <a name="next-steps"></a>Étapes suivantes

* Pour un exemple de code qui appelle l’API Azure Time Series Insights Gen1 mise à la disposition générale, lisez [Interroger des données Gen1 à l’aide de C#](./time-series-insights-query-data-csharp.md).

* Pour un exemple de code qui appelle l’API Azure Time Series Insights Gen2, lisez [Interroger des données Gen2 à l’aide de C#](./time-series-insights-update-query-data-csharp.md).

* Pour obtenir des informations de référence sur l’API, lisez la documentation de [référence sur l’API de requête](/rest/api/time-series-insights/reference-query-apis).
