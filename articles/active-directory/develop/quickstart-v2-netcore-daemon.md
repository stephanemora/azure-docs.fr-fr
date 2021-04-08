---
title: 'Démarrage rapide : Obtenir un jeton et appeler Microsoft Graph dans une application console | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez apprendre comment un exemple d’application .NET Core peut utiliser le workflow d’informations d’identification du client pour obtenir un jeton et appeler Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022923"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Démarrage rapide : acquérir un jeton et appeler l’API Microsoft Graph à partir d’une de l’identité de l’application d’une console

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application console .NET Core peut obtenir un jeton d’accès pour appeler l’API Microsoft Graph et afficher une [liste des utilisateurs](/graph/api/user-list) dans l’annuaire. L’exemple de code montre également comment un travail ou un service Windows peut s’exécuter avec l’identité d’une application, au lieu de l’identité d’un utilisateur. L’exemple d’application de console dans ce guide de démarrage rapide est également une application démon. Il s’agit donc d’une application cliente confidentielle.

> [!div renderon="docs"]
> Le diagramme suivant illustre le fonctionnement de l’exemple d’application :
>
> ![Diagramme montrant le fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide nécessite [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core), mais fonctionne également avec .NET Core 5.0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Inscrire et télécharger l’application

> [!div renderon="docs" class="sxs-lookup"]
>
> Vous avez deux options pour commencer à créer votre application : configuration automatique ou configuration manuelle.
>
> ### <a name="automatic-configuration"></a>Configuration automatique
>
> Si vous souhaitez inscrire et configurer automatiquement votre application, puis télécharger l’exemple de code, procédez comme suit :
>
> 1. Accédez à la <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">page du portail Azure permettant d’inscrire des applications</a>.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.
>
> ### <a name="manual-configuration"></a>Configuration manuelle
>
> Si vous souhaitez configurer manuellement votre application et votre exemple de code, utilisez les procédures suivantes.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</span></a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Annuaire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire l’application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Pour **Nom**, entrez un nom pour votre application. Entrez par exemple **Daemon-console**. Les utilisateurs de votre application verront ce nom, que vous pourrez changer ultérieurement.
> 1. Sélectionnez **Inscrire** pour créer l’application.
> 1. Sous **Gérer**, sélectionnez **Certificats et secrets**.
> 1. Sous **Secrets du client**, sélectionnez **Nouveau secret client**, entrez un nom, puis sélectionnez **Ajouter**. Enregistrez la valeur secrète dans un emplacement sûr pour l’utiliser dans une étape ultérieure.
> 1. Sous **Gérer**, sélectionnez **Autorisations de l’API** > **Ajouter une autorisation**. Sélectionnez **Microsoft Graph**.
> 1. Sélectionnez **Autorisations de l’application**.
> 1. Sous le nœud **Utilisateur**, sélectionnez **User.Read.All**, puis sélectionnez **Ajouter des autorisations**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Télécharger et configurer votre application de démarrage rapide
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, créez un secret client et ajoutez l’autorisation d’application **User.Read.All** de l’API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-your-visual-studio-project"></a>Étape 2 : Télécharger votre projet Visual Studio

> [!div renderon="docs"]
> [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Vous pouvez exécuter le projet fourni dans Visual Studio ou Visual Studio pour Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Exécutez le projet avec Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Étape 3 : Configurer votre projet Visual Studio
>
> 1. Extrayez le fichier .zip dans un dossier local proche de la racine du disque. Par exemple, extrayez-le dans *C:\Azure-Samples*.
>
>    Nous vous recommandons d’extraire l’archive dans un répertoire près de la racine de votre lecteur pour éviter les erreurs dues à des limitations de longueur de chemin sur Windows.
>
> 1. Ouvrez la solution dans Visual Studio : *1-Call-MSGraph\daemon-console.sln* (facultatif).
> 1. Dans *appsettings.js*, remplacez les valeurs de `Tenant`, `ClientId` et `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    Dans ce code :
>    - `Enter_the_Application_Id_Here` est la valeur ID d’application (client) pour l’application que vous avez inscrite.
        Pour connaître les valeurs de l’ID d’application (client) et de l’ID de l’annuaire (locataire), consultez la page **Vue d’ensemble** de l’application dans le Portail Azure.
>    - Remplacez `Enter_the_Tenant_Id_Here` par l’ID de locataire ou le nom de locataire (par exemple, `contoso.microsoft.com`).
>    - Remplacez `Enter_the_Client_Secret_Here` par la clé secrète client que vous avez créée à l’étape 1.
    Pour générer une nouvelle clé, accédez à la page **Certificats et secrets**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Étape 3 : Consentement de l’administrateur

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Étape 4 : Consentement de l’administrateur

Si vous essayez d’exécuter l’application à ce stade, vous recevrez une erreur *HTTP 403 - Interdit* : « privilèges insuffisants pour terminer l’opération ». Cette erreur se produit parce que les autorisations d’application uniquement nécessitent un administrateur général de votre annuaire pour donner son consentement à votre application. Sélectionnez l’une des options suivantes en fonction de votre rôle.

##### <a name="global-tenant-administrator"></a>Administrateur de locataires général

> [!div renderon="docs"]
> Si vous êtes un administrateur client global, accédez à **Applications d’entreprise** sur le portail Azure. Sélectionnez l’inscription de votre application, puis sélectionnez **Autorisations** dans la section **Sécurité** du volet gauche. Sélectionnez ensuite le grand bouton intitulé **Accorder un consentement d’administrateur pour {nom du locataire}** (où **{nom du locataire}** est le nom de votre annuaire).

> [!div renderon="portal" class="sxs-lookup"]
> Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement administrateur pour Entrer_le_nom_du_locataire_ici**.
> > [!div id="apipermissionspage"]
> > [Accéder à la page Autorisations de l’API]()

##### <a name="standard-user"></a>Utilisateur standard

Si vous êtes un utilisateur standard de votre locataire, demandez à un administrateur général de vous accorder le consentement administrateur pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Dans cette URL :
> * Remplacez `Enter_the_Tenant_Id_Here` par l’ID de locataire ou le nom de locataire (par exemple, `contoso.microsoft.com`).
> * `Enter_the_Application_Id_Here` est la valeur ID d’application (client) pour l’application que vous avez inscrite.

L’erreur « AADSTS50011 : aucune adresse de réponse n’est inscrite pour l’application » peut s’afficher après avoir l’octroi du consentement pour l’application à l’aide de l’URL précédente. Cette erreur se produit parce que cette application et l’URL n’ont pas d’URI de redirection. Vous pouvez l’ignorer.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Étape 5 : Exécution de l'application

Si vous utilisez Visual Studio ou Visual Studio pour Mac, appuyez sur **F5** pour exécuter l’application. Dans le cas contraire, exécutez l’application via l’invite de commandes, la console ou le terminal :

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Dans ce code :
* `{ProjectFolder}` est le dossier où vous avez extrait le fichier zip. par exemple `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Une liste des utilisateurs dans Azure Active Directory s’affiche normalement.

Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. La clé secrète client est ajoutée sous la forme d’un fichier de texte brut à vos fichiers projet. Pour des raisons de sécurité, nous vous recommandons d’utiliser un certificat plutôt qu’une clé secrète client avant de considérer l’application comme une application de production. Pour plus d’informations sur la façon d’utiliser un certificat, consultez [ces instructions](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) dans le dépôt GitHub pour cet exemple.

## <a name="more-information"></a>Informations complémentaires
Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, quels sont les principaux arguments et comment ajouter une connexion à une application de console .NET Core existante.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
>
> ![Diagramme montrant le fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

La bibliothèque d’authentification Microsoft (MSAL, dans le package [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons pour accéder à une API protégée par la plateforme d’identité Microsoft. Ce guide de démarrage rapide demande des jetons à l’aide de l’identité de l’application au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le terme de [flux d’informations d’identification du client OAuth](v2-oauth2-client-creds-grant-flow.md). Pour plus d’informations sur l’utilisation de MSAL.NET avec un flux d’informations d’identification du client, consultez [cet article](https://aka.ms/msal-net-client-credentials).

 Vous pouvez installer MSAL.NET en exécutant la commande suivante dans la console du gestionnaire de package de Visual Studio :

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```csharp
using Microsoft.Identity.Client;
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Élément | Description |
 |---------|---------|
 | `config.ClientSecret` | La clé secrète client créée pour l’application dans le portail Azure. |
 | `config.ClientId` | L’ID d’application (client) de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
 | `config.Authority`    | (Facultatif) Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Il s’agit généralement de `https://login.microsoftonline.com/{tenant}` pour le cloud public, où `{tenant}` est le nom ou l’ID de votre locataire.|

Pour plus d’informations, consultez la [documentation de référence pour `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Demande de jetons

Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `AcquireTokenForClient` :

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Élément| Description |
|---------|---------|
| `scopes` | Contient les étendues demandées. Pour les clients confidentiels, cette valeur doit utiliser un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies statiquement dans l’objet d’application défini sur le Portail Azure. Pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com` . Pour les API web personnalisées, `{Application ID URI}` est défini sur le portail Azure, sous **Inscription d’application (préversion)**  > **Exposer une API**. |

Pour plus d’informations, consultez la [documentation de référence pour `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les applications démon, consultez la vue d’ensemble du scénario :

> [!div class="nextstepaction"]
> [Application démon qui appelle des API web](scenario-daemon-overview.md)
