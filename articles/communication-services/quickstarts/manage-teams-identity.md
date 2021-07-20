---
title: Créer et configurer des jetons d’accès Teams
titleSuffix: An Azure Communication Services quickstart
description: Création d’un service fournissant des jetons d’accès Teams
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e9e58659cfaa5b459a28278362aac002a1a87db5
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223857"
---
# <a name="quickstart-set-up-and-manage-teams-access-tokens"></a>Démarrage rapide : Configurer et gérer des jetons d’accès Teams

> [!IMPORTANT]
> Pour activer/désactiver l’expérience de point de terminaison personnalisé Teams, complétez [ce formulaire](https://forms.office.com/r/B8p5KqCH19).

Dans ce démarrage rapide, nous allons créer une application de console .NET pour authentifier un jeton d’utilisateur AAD à l’aide de la bibliothèque MSAL. Nous échangerons ensuite ce jeton contre un jeton d’accès Teams avec le kit de développement logiciel (SDK) Azure Communication Services Identity. Le jeton d’accès Teams peut ensuite être utilisé par le kit de développement logiciel (SDK) Azure Communication Services Calling pour créer un point de terminaison personnalisé Teams.

> [!NOTE]
> Au sein des environnements de production, nous vous recommandons d’implémenter ce mécanisme d’échange dans les services principaux, car les demandes d’échange sont signées à l’aide d’un secret.


## <a name="prerequisites"></a>Prérequis
- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](./create-communication-resource.md).
- Activer l’expérience de point de terminaison personnalisé Teams via [ce formulaire](https://forms.office.com/r/B8p5KqCH19)
- Disposer d’Azure Active Directory avec des utilisateurs dotés d’une licence Teams

## <a name="introduction"></a>Introduction

Les identités Teams sont liées aux locataires dans Azure Active Directory. Votre application peut être utilisée par les utilisateurs du même client ou de n’importe quel locataire. Dans ce démarrage rapide, nous allons utiliser un cas d’usage mutualisé avec plusieurs acteurs : utilisateurs, développeurs et administrateurs des entreprises fictives Contoso et Fabrikam. Dans ce cas d’usage, Contoso est une entreprise qui crée une solution SaaS pour Fabrikam. 

Les sections suivantes vous guident tout au long des étapes destinées aux administrateurs, développeurs et utilisateurs. Les diagrammes illustrent le cas d’usage mutualisé. Si vous utilisez un seul locataire, exécutez toutes les étapes de Contoso et de Fabrikam dans un seul locataire.

## <a name="admin-actions"></a>Actions d’administration

Le rôle Administrateur dispose d’autorisations étendues dans AAD. Les membres de ce rôle peuvent approvisionner des ressources et lire des informations à partir du portail Azure. Dans le diagramme suivant, vous pouvez voir toutes les actions qui doivent être exécutées par les administrateurs.

![Actions des administrateurs pour activer l’expérience de point de terminaison personnalisé Teams](./media/teams-identities/teams-identity-admin-overview.png)

1. L’administrateur de Contoso crée ou sélectionne une *application* existante dans Azure Active Directory. La propriété *Types de comptes pris en charge* définit si les utilisateurs de différents locataires peuvent s’authentifier auprès de l'*application*. La propriété *URI de redirection* redirige la demande d’authentification réussie vers le *serveur* de Contoso.
1. L’administrateur de Contoso étend le manifeste de l'*application* avec l’autorisation VoIP Azure Communication Services. 
1. L’administrateur de Contoso autorise le flux client public pour l'*application*
1. L’administrateur de Contoso peut procéder à la mise à jour
1. L’administrateur de Contoso active l’expérience via [ce formulaire](https://forms.office.com/r/B8p5KqCH19)
1. L’administrateur de Contoso crée ou sélectionne une instance Communication Services existante qui sera utilisée pour l’authentification des demandes d’échange. Les jetons utilisateur AAD sont échangés contre des jetons d’accès Teams. De plus amples informations sur la création de [nouvelles ressources Azure Communication Services sont disponibles ici](./create-communication-resource.md).
1. L’administrateur de Fabrikam configure un nouveau principal de service pour Azure Communication Services dans le locataire de Fabrikam.
1. L’administrateur de Fabrikam accorde l’autorisation VoIP Azure Communication Services à l'*application* de Contoso. Cette étape est uniquement requise si l'*application* de Contoso n’est pas vérifiée.

### <a name="1-create-aad-application-registration-or-select-aad-application"></a>1. Créer une inscription d’application AAD ou sélectionner une application AAD 

Les utilisateurs doivent être authentifiés auprès des applications AAD avec l’autorisation `VoIP` Azure Communication Services. Si vous ne disposez pas d’une application que vous souhaitez utiliser pour ce démarrage rapide, vous pouvez créer une nouvelle inscription d’application. 

Les paramètres d’application suivants influent sur l’expérience :
- La propriété *Types de comptes pris en charge* définit si l'*application* correspond à un locataire unique (« comptes au sein de cet annuaire organisationnel uniquement ») ou à plusieurs locataires (« comptes au sein de n’importe quel annuaire organisationnel »). Pour ce scénario, vous pouvez utiliser plusieurs locataires.
- *URI de redirection* définit l’URI où la demande d’authentification est redirigée après authentification. Pour ce scénario, vous pouvez utiliser « Client public/natir (mobile et bureau) » et remplir « http://localhost » en tant qu’URI.

[La documentation détaillée est disponible ici.](/azure/active-directory/develop/quickstart-register-app#register-an-application) 

Lorsque l'*application* est inscrite, un identificateur s’affiche dans la vue d’ensemble. Cet identificateur est utilisé lors des étapes suivantes : **ID d’application (client)** .

### <a name="2-allow-public-client-flows"></a>2. Autoriser les flux clients publics

Dans le volet *Authentification* de votre *application*, vous pouvez voir la plateforme configurée pour le *Client public/natif (mobile et bureau)* avec l’URI de redirection pointant vers *localhost*. En bas de l’écran, vous pouvez noter la présence du bouton bascule *Autoriser les flux clients publics*, qui, pour ce démarrage rapide, est défini sur **Oui**.

### <a name="3-update-publisher-domain-optional"></a>3. Mettre à jour le domaine de l’éditeur (facultatif)
Dans le volet *Personnalisation*, vous pouvez mettre à jour votre domaine d’éditeur pour l'*application*. Cela est utile dans le cas d’une application mutualisée, où l’application est marquée comme vérifiée par Azure. Vous trouverez plus d’informations sur la vérification de l’éditeur ainsi que sur la mise à jour du domaine de votre application [ici](/azure/active-directory/develop/howto-configure-publisher-domain).

### <a name="4-define-azure-communication-services-voip-permission-in-application"></a>4. Définir l’autorisation VoIP Azure Communication Services dans l’application

Accédez aux détails de l'*application* et sélectionnez le volet « Manifeste ». Dans le manifeste, recherchez la propriété appelée : *« requiredResourceAccess »* . Il s’agit d’un tableau d’objets qui définit les autorisations de l’*application*. Étendez le manifeste avec les autorisations VoIP pour l’instance Azure Communication Services de l’application principale. Ajoutez l’objet suivant au tableau.

> [!NOTE] 
> Ne modifiez pas les GUID dans l’extrait de code, car ils identifient de manière unique l’application et les autorisations.

```json
{
   "resourceAppId": "1fd5118e-2576-4263-8130-9503064c837a",
   "resourceAccess": [
      {
         "id": "31f1efa3-6f54-4008-ac59-1bf1f0ff9958",
         "type": "Scope"
      }
   ]
}
```

Sélectionnez ensuite le bouton *Enregistrer* pour conserver les modifications. Vous pouvez maintenant voir l’autorisation VoIP Azure Communication Services dans le volet *Autorisations de l’API*.

### <a name="5-enable-custom-teams-endpoint-experience-for-application"></a>5. Activer l’expérience de point de terminaison personnalisé Teams pour l'*application*

L’administrateur AAD remplit le [formulaire](https://forms.office.com/r/B8p5KqCH19) suivant pour activer l’expérience de point de terminaison personnalisé Teams pour l'*application*.

### <a name="6-create-or-select-communication-services-resource"></a>6. Créer ou sélectionner une ressource Communication Services

Votre ressource Azure Communication Services est utilisée pour authentifier toutes les demandes d’échange de jetons utilisateur AAD contre des jetons d’accès Teams. Cet échange peut être déclenché par le biais du kit de développement logiciel (SDK) Azure Communication Services Identity, qui est authentifié à l’aide d’une clé d’accès ou d’Azure RBAC. Vous pouvez récupérer la clé d’accès dans le portail Azure ou configurer le volet Azure RBAC via le volet *Contrôle d’accès (IAM)* .

Si vous souhaitez [créer de nouvelles ressources Communication Services, suivez ce guide](./create-communication-resource.md).

### <a name="7-provision-azure-communication-services-service-principal"></a>7. Approvisionner le principal de service Azure Communication Services

Pour activer l’expérience de point de terminaison personnalisé Teams dans le locataire de Fabrikam, l’administrateur AAD de Fabrikam doit configurer le principal de service nommé Azure Communication Services avec l’ID d’application : *1fd5118e-2576-4263-8130-9503064c837a*. Si vous ne voyez pas cette application dans le volet Applications d’entreprise d’Azure Active Directory, ajoutez-la manuellement.

L’administrateur AAD de Fabrikam se connecte au locataire Azure via PowerShell. 

> [!NOTE]
> Remplacez [Tenant_ID] par l’ID de votre locataire disponible dans la page de présentation d’AAD du portail Azure.

```azurepowershell
Connect-AzureAD -TenantId "[Tenant_ID]"
```

Si la commande est introuvable, cela signifie que le module AzureAD n’est pas installé dans votre instance PowerShell. Fermez PowerShell et exécutez-le avec des droits d’administration. Vous pouvez ensuite installer le package Azure-AD à l’aide de la commande suivante :

```azurepowershell
Install-Module AzureAD
```

Après connexion et authentification auprès d’Azure, exécutez la commande suivante pour configurer le principal de service Communication Services. 

> [!NOTE]
> Le paramètre AppId fait référence aux ressources Azure Communication Services de l’application principale. Ne changez pas cette valeur.

```azurepowershell
New-AzureADServicePrincipal -AppId "1fd5118e-2576-4263-8130-9503064c837a"
```

### <a name="8-provide-admin-consent"></a>8. Fournir le consentement administrateur

Si l'*application* de Contoso n’est pas vérifiée, l’administrateur AAD doit accorder l’autorisation à l'*application* de Contoso pour l’autorisation VoIP Azure Communication Services. L’administrateur AAD de Fabrikam fournit un consentement via un lien unique. Pour créer le lien de consentement administrateur, suivez les instructions ci-dessous :

1. Utilisez le lien suivant *https://login.microsoftonline.com/{Tenant_ID}/adminconsent?client_id={Application_ID}*
1. Remplacez {Tenant_ID} par l’ID de locataire de Fabrikam
1. Remplacez {Application_ID} par l’ID d’application de Contoso
1. L’administrateur AAD de Fabrikam accède au lien dans le navigateur. 
1. L’administrateur AAD de Fabrikam se connecte et accorde des autorisations pour le compte de l’organisation

Dans l’application de Fabrikam, le principal de service de l'*application* de Contoso est créé si le consentement est accordé. L’administrateur de Fabrikam peut vérifier le consentement dans AAD :

1. Connectez-vous au portail Azure en tant qu’administrateur
1. Accédez à Azure Active Directory
1. Accédez au volet « Applications d’entreprise »
1. Définissez le filtre « Type d’application » sur « Toutes les applications »
1. Dans le champ permettant de filtrer les applications, insérez le nom de l’application de Contoso
1. Sélectionnez « Appliquer » pour filtrer les résultats
1. Sélectionnez un principal de service avec le nom requis 
1. Accédez au volet *Autorisations*

Vous pouvez voir que l’autorisation VoIP « Azure Communication Services » présente désormais l’état *Accordé pour {Directory_name}* .

## <a name="developer-actions"></a>Actions du développeur

Le développeur de Contoso doit configurer une *application cliente* pour l’authentification des utilisateurs. Le développeur doit ensuite créer un point de terminaison sur le *serveur* principal pour traiter le jeton d’utilisateur AAD après redirection. Une fois reçu, le jeton d’utilisateur AAD est échangé contre un jeton d’accès Teams et renvoyé à l'*application cliente*. Les actions nécessaires aux développeurs sont présentées dans le diagramme suivant :

![Actions de développeur permettant d’activer l’expérience de point de terminaison personnalisé Teams](./media/teams-identities/teams-identity-developer-overview.png)

1. Le développeur de Contoso configure la bibliothèque MSAL afin d’authentifier l’utilisateur pour l'*application* créée aux étapes précédentes par l’administrateur pour l’autorisation VoIP Azure Communication Services.
1. Le développeur de Contoso initialise le kit de développement logiciel (SDK) d’identité ACS et échange le jeton d’utilisateur AAD entrant contre un jeton d’accès Teams via le SDK. Le jeton d’accès Teams est ensuite renvoyé à l'*application cliente*.

Microsoft Authentication Library (MSAL) permet aux développeurs d’acquérir des jetons d’utilisateur AAD à partir du point de terminaison de la plateforme d’identités Microsoft afin d’authentifier les utilisateurs et d’accéder aux API web sécurisées. Il peut être utilisé pour fournir un accès sécurisé à Azure Communication Services. MSAL prend en charge de nombreuses architectures et plateformes d’application différentes, notamment .NET, JavaScript, Java, Python, Android et iOS.

Pour plus d’informations sur la configuration de différents environnements, consultez la documentation publique. [Présentation de la bibliothèque d’authentification Microsoft (MSAL.NET)](/azure/active-directory/develop/msal-overview).

> [!NOTE]
> Les sections suivantes expliquent comment échanger un jeton d’accès AAD contre un jeton d’accès Teams pour une application console dans .NET.

### <a name="create-new-application"></a>Créer une application

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la nouvelle commande dotnet pour créer une application console portant le nom *TeamsAccessTokensQuickstart*. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : *Program.cs*.

```console
dotnet new console -o TeamsAccessTokensQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande dotnet build pour compiler votre application.

```console
cd TeamsAccessTokensQuickstart
dotnet build
```
#### <a name="install-the-package"></a>Installer le package
Toujours dans le répertoire de l’application, installez le package de la bibliothèque Azure Communication Services Identity pour .NET à l’aide de la commande dotnet « add package ».

```console
dotnet add package Azure.Communication.Identity
dotnet add package Microsoft.Identity.Client
```

#### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

- Ouvrez le fichier Program.cs dans un éditeur de texte
- Ajoutez une directive using pour inclure les espaces de noms suivants : 
    - Azure.Communication
    - Azure.Communication.Identity
    - Microsoft.Identity.Client
- Mettre à jour la déclaration de la méthode Main pour prendre en charge le code asynchrone

Utilisez le code suivant pour commencer :

```csharp
using System;
using System.Text;
using Azure.Communication;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace TeamsAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services – Teams access tokens quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="1-receive-aad-user-token-via-msal-library"></a>1. Recevoir le jeton d’utilisateur AAD via la bibliothèque MSAL

Utilisez la bibliothèque MSAL pour authentifier l’utilisateur auprès d’AAD pour l'*application* de Contoso avec l’autorisation VoIP Azure Communication Services. Configurez le client pour l'*application* de Contoso (*paramètre applicationId*) dans le cloud public (*paramètre authority*). Le jeton d’utilisateur AAD sera renvoyé à l’URI de redirection (*paramètre redirectUri*). Les informations d’identification sont extraites de la fenêtre contextuelle interactive, qui s’ouvre dans votre navigateur par défaut.

> [!NOTE] 
> L’URI de redirection doit correspondre à la valeur définie dans l'*application*. Consultez la première étape du Guide de l’administrateur pour savoir comment configurer l’URI de redirection.

```csharp
const string applicationId = "Contoso's_Application_ID";
const string authority = "https://login.microsoftonline.com/common";
const string redirectUri = "http://localhost";

var client = PublicClientApplicationBuilder
                .Create(applicationId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

const string scope = "https://auth.msft.communication.azure.com/VoIP";

var aadUserToken = await client.AcquireTokenInteractive(new[] { scope }).ExecuteAsync();

Console.WriteLine("\nAuthenticated user: " + aadUserToken.Account.Username);
Console.WriteLine("AAD user token expires on: " + aadUserToken.ExpiresOn);
```

La variable *aadUserToken* contient désormais un jeton d’utilisateur Azure Active Directory valide, qui sera utilisé pour l’échange.

### <a name="2-exchange-aad-user-token-for-teams-access-token"></a>2. Échanger le jeton d’utilisateur AAD contre le jeton d’accès Teams

Le jeton d’utilisateur AAD valide authentifie l’utilisateur auprès d’AAD pour l’application principale avec l’autorisation VoIP Azure Communication Services. Le code suivant est utilisé par le kit de développement logiciel (SDK) des identités ACS pour faciliter l’échange du jeton d’utilisateur AAD contre un jeton d’accès Teams.

> [!NOTE]
> Remplacez la valeur « &lt;Connection-String&gt; » par une chaîne de connexion valide ou utilisez Azure RBAC pour l’authentification. Pour plus d’informations, consultez [ce démarrage rapide](./access-tokens.md).

```csharp
var identityClient = new CommunicationIdentityClient("<Connection-String>");
var teamsAccessToken = identityClient.ExchangeTeamsToken(aadUserToken.AccessToken);

Console.WriteLine("\nTeams access token expires on: " + teamsAccessToken.Value.ExpiresOn);
```

Si toutes les conditions définies dans les conditions requises sont remplies, vous obtenez un jeton d’accès Teams valide pendant 24 heures.

#### <a name="run-the-code"></a>Exécuter le code
Exécutez l’application à partir de votre répertoire d’application avec la commande d’exécution dotnet.

```console
dotnet run
```

La sortie de l’application décrit chaque action terminée :

```console
Azure Communication Services - Teams access tokens quickstart

Authenticated user: john.smith@contoso.com
AAD user token expires on: 6/10/2021 10:13:17 AM +00:00

Teams access token expires on: 6/11/2021 9:13:18 AM +00:00
```

## <a name="user-actions"></a>Actions utilisateur

L’utilisateur représente les utilisateurs de Fabrikam de l'*application* de Contoso. L’expérience utilisateur est présentée dans le diagramme suivant.

![Actions de l’utilisateur permettant d’activer l’expérience de point de terminaison personnalisé Teams](./media/teams-identities/teams-identity-user-overview.png)

1. L’utilisateur de Fabrikam utilise l'*application cliente* de Contoso et est invité à s’authentifier.
1. L’*application cliente* de Contoso utilise la bibliothèque MSAL pour authentifier l’utilisateur auprès du locataire Azure Active Directory Fabrikan pour l'*application* de Contoso avec l’autorisation VoIP Azure Communication Services. 
1. L’authentification est redirigée vers le *serveur* comme défini dans la propriété *URI de redirection* dans MSAL et l'*application* de Contoso
1. Le *serveur* de Contoso échange le jeton d’utilisateur AAD contre un jeton d’accès Teams à l’aide du kit de développement logiciel (SDK) ACS Identity et renvoie le jeton d’accès Teams à l'*application cliente*.

Moyennant un jeton d’accès Teams valide dans l’*application cliente*, le développeur peut intégrer le kit de développement logiciel (SDK) ACS Calling et créer un point de terminaison personnalisé Teams.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Créer et configurer une application dans Azure Active Directory
> * Utiliser la bibliothèque MSAL pour émettre un jeton d’utilisateur Azure Active Directory
> * Utiliser le kit de développement logiciel (SDK) ACS Identity pour échanger un jeton Azure Active Directory contre un jeton d’accès Teams

Les documents suivants peuvent vous intéresser :

- En savoir plus sur le [point de terminaison personnalisé Teams](../concepts/teams-endpoint.md)
- En savoir plus sur [l’interopérabilité Teams](../concepts/teams-interop.md)
