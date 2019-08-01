---
title: 'Démarrage rapide : Créer une application web qui lance le lecteur immersif avec C#'
titlesuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une application web à partir de zéro et lui ajoutez les fonctions de l’API Lecteur immersif.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 978b56f5c38a154c79544c7deb5d49396b1deff9
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688863"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Démarrage rapide : Créer une application web qui lance le lecteur immersif (C#)

Le [lecteur immersif](https://www.onenote.com/learningtools) est un outil conçu de façon inclusive qui implémente des techniques éprouvées pour améliorer la compréhension de la lecture.

Dans ce guide de démarrage rapide, vous allez créer une application web à partir de zéro et intégrer le lecteur immersif à l’aide du SDK Lecteur immersif. Un exemple complet fonctionnel de ce guide de démarrage rapide est disponible [ici](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Ressource Lecteur immersif configurée pour l’authentification Azure Active Directory (Azure AD). Suivez [ces instructions](./azure-active-directory-authentication.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’exemple de projet. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.

## <a name="create-a-web-app-project"></a>Créer un projet d’application web

Créez un nouveau projet dans Visual Studio à l’aide du modèle d’application web ASP.NET Core avec modèle-vue-contrôleur intégré.

![Nouveau projet](./media/vswebapp.png)

![Nouvelle application web ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquérir un jeton d’authentification Azure AD

Vous avez besoin de certaines valeurs de l’étape prérequise de configuration de l’authentification Azure AD ci-dessus pour cette partie. Reportez-vous au fichier texte que vous avez enregistré pour cette session.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Cliquez avec le bouton droit sur le projet dans _l’Explorateur de solutions_ et choisissez **Gérer les données secrètes de l’utilisateur**. Cela ouvrira un fichier appelé _secrets.json_. Remplacez le contenu de ce fichier par le code suivant, en fournissant les valeurs de vos propriétés personnalisées ci-dessus.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Ouvrez _Controllers\HomeController.cs_ et remplacez le fichier par le code suivant.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Ajouter le package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory

Le code ci-dessus utilise des objets du package NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**. Vous devrez donc ajouter une référence à ce package dans votre projet.

Ouvrez la console du Gestionnaire de package NuGet dans **Outils-> Gestionnaire de package NuGet-> Console du Gestionnaire de package** et tapez ce qui suit :

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Ajouter un contenu exemple

Maintenant, nous allons ajouter des exemples de contenu à cette application web. Ouvrez _Views\Home\Index.cshtml_ et remplacez le code généré automatiquement par cet exemple :

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Dans la barre de menus, sélectionnez **Déboguer > Démarrer le débogage**, ou appuyez sur **F5** pour démarrer l’application.

Dans votre navigateur, vous devriez voir :

![Exemple d'application](./media/quickstart-result.png)

Lorsque vous cliquez sur le bouton « Lecteur immersif », vous verrez le lecteur immersif lancé avec le contenu de la page.

![Lecteur immersif](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Étapes suivantes

* Consulter le [didacticiel](./tutorial.md) pour voir que vous pouvez faire d’autre avec le SDK Lecteur immersif
* Explorer le [SDK Lecteur Immersive](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)
