---
title: Accorder à une application de fonction Azure l’accès à un compte Media Services
description: Supposons que vous souhaitiez créer une signalisation « À l’antenne » pour votre studio de diffusion. Vous pouvez déterminer à quel moment des événements en direct Media Services s’exécutent en utilisant l’API Media Services, mais il peut être difficile d’appeler celle-ci à partir d’un appareil intégré. Au lieu de cela, vous pouvez exposer une API HTTP pour votre appareil intégré à l’aide d’Azure Functions. Azure Functions peut ensuite appeler Media Services pour recueillir l’état de l’événement en direct.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: inhenkel
ms.openlocfilehash: 6352c86581da356f4b2bab1a80dd463d502a9ae3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481748"
---
# <a name="tutorial-give-an-azure-function-app-access-to-a-media-services-account"></a>Tutoriel : Accorder à une application de fonction Azure l’accès à un compte Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Supposons que vous souhaitiez faire savoir aux visiteurs de votre site web ou de votre application que vous êtes « À l’antenne » dans votre studio de diffusion. Vous pouvez déterminer à quel moment des événements en direct Media Services s’exécutent en utilisant l’API Media Services, mais il peut être difficile d’appeler celle-ci à partir d’un appareil intégré. Au lieu de cela, vous pouvez exposer une API HTTP pour votre appareil intégré à l’aide d’Azure Functions. Azure Functions peut ensuite appeler Media Services pour recueillir l’état de l’événement en direct.

:::image type="content" source="media/diagrams/managed-identities-scenario-function-app-access-media-services-account.svg" alt-text="Identités managées donnant à une application de fonction l’accès à un compte Media Services":::

Ce tutoriel utilise l’API Media Services 2020-05-01.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour utiliser l’une des commandes de cet article, vous devez d’abord être connecté à l’abonnement que vous souhaitez utiliser.

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>Définir l’abonnement

Utilisez cette commande pour définir l’abonnement que vous souhaitez utiliser.

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Avant d’aborder ce tutoriel, nous vous recommandons vivement de suivre la section [Créer une fonction C# dans Azure à partir du démarrage rapide de ligne de commande](../../azure-functions/create-first-function-cli-csharp.md).  Cela se justifie par le fait que les étapes de configuration décrites dans celle-ci sont les mêmes que celles à suivre ici.  Cela vous offrira également la possibilité de travailler avec un exemple simple sur lequel le présent tutoriel est basé.

## <a name="resource-names"></a>Noms de ressource

Avant de commencer, choisissez les noms des ressources que vous allez créer.  Ils doivent être aisément identifiables en tant qu’ensemble, surtout si vous ne prévoyez pas de les utiliser une fois les tests terminés. Les règles de nommage varient selon les types de ressources. Il est donc préférable de s’en tenir aux minuscules. Par exemple, « mediatest1rg » pour le nom de votre groupe de ressources et « mediatest1stor » pour le nom de votre compte de stockage. Utilisez les mêmes noms à chacune des étapes de cet article.

Vous verrez ces noms référencés dans les commandes ci-dessous.  Les noms de ressources dont vous aurez besoin sont les suivants :

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-region
- your-function-name : utilisez « OnAir »
- your-live-event-name : utilisez « live1 »
- your-ip-address : utilisez « 0.0.0./32 »

> [!NOTE]
> Les tirets ci-dessus sont uniquement utilisés pour séparer les mots. Compte tenu de l’incohérence de nommage des ressources dans les services Azure, n’utilisez pas de tirets lorsque vous nommez vos ressources.<br/><br/>
> Tout ce qui est représenté par 00000000-0000-0000-0000000000 correspond à l’identificateur unique de la ressource.  Cette valeur est généralement retournée par une réponse JSON. Il est également recommandé de copier et coller les réponses JSON dans le Bloc-notes ou tout autre éditeur de texte, car ces réponses contiennent des valeurs dont vous aurez besoin pour les commandes CLI ultérieures.<br/><br/>
> Par ailleurs, vous ne créez pas le nom de région.  Celui-ci est déterminé par Azure.

### <a name="list-azure-regions"></a>Liste des régions Azure

Si besoin, utilisez cette commande pour accéder à la liste des régions :

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>Séquence

Chacune des étapes ci-dessous suit un ordre particulier car une ou plusieurs valeurs des réponses JSON sont utilisées à l’étape suivante de la séquence.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les ressources que vous allez créer doivent appartenir à un groupe de ressources. Commencez par créer le groupe de ressources. Vous utiliserez `your-resource-group-name` pour l’étape de création du compte Media Services et les étapes suivantes.

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Un compte de stockage doit être associé au compte Media Services que vous allez créer. Commencez par créer le compte de stockage à associer au compte Media Services. Vous allez utiliser `your-storage-account-name` pour les étapes suivantes.

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Créer un compte Media Services

À présent, créez le compte Media Services. Recherchez

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-cli.md)]

## <a name="set-up-the-azure-function"></a>Configurer la fonction Azure

Dans cette section, vous allez configurer votre fonction Azure.

### <a name="get-the-code"></a>Obtenir le code

Utilisez Azure Functions pour créer votre projet de fonction et récupérer le code du modèle HTTP.

```azurecli-interactive
func init MediaServicesLiveMonitor –dotnet
```

### <a name="change-directory"></a>Modifier le répertoire

Veillez à faire de votre répertoire de travail le répertoire du projet.  Dans le cas contraire, vous obtiendrez des erreurs.

```azurecli-interactive
cd .\MediaServicesLiveMonitor\
```

### <a name="name-your-function"></a>Nommer votre fonction

```azurecli-interactive
func new --name OnAir --template "HTTP trigger" --authlevel "anonymous"
```

## <a name="configure-the-functions-project"></a>Configurer le projet de fonctions

### <a name="add-items-to-the-csproj-file"></a>Ajouter des éléments au fichier .csproj

Dans le fichier « .csproj » généré automatiquement, ajoutez les lignes suivantes au premier `<ItemGroup>` :

```xml
<PackageReference Include="Microsoft.Azure.Management.Fluent" Version="1.37.0" />
<PackageReference Include="Microsoft.Azure.Management.Media" Version="3.0.4" />
```

### <a name="edit-the-onaircs-code"></a>Modifier le code de OnAir.cs

Modifiez le fichier `OnAir.cs`. Remplacez les variables `subscriptionId`, `resourceGroup` et `mediaServicesAccountName` par celles que vous avez choisies précédemment.

```aspx-csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.Media.Models;

namespace MediaServicesLiveMonitor
{
    public static class LatestAsset
    {
        [FunctionName("OnAir")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            if (string.IsNullOrWhiteSpace(name))
            {
                return new BadRequestObjectResult("Missing 'name' URL parameter");
            }
            
            var credentials = SdkContext.AzureCredentialsFactory.FromSystemAssignedManagedServiceIdentity(
                MSIResourceType.AppService,
                AzureEnvironment.AzureGlobalCloud);

            var subscriptionId = "00000000-0000-0000-000000000000";    // Update
            var resourceGroup = "<your-resource-group-name>";                                    // Update
            var mediaServicesAccountName = "<your-media-services-account-name>";                    // Update

            var mediaServices = new AzureMediaServicesClient(credentials)
            {
                SubscriptionId = subscriptionId
            };

            var liveEvent = await mediaServices.LiveEvents.GetAsync(resourceGroup, mediaServicesAccountName, name);

            if (liveEvent == null)
            {
                return new NotFoundResult();
            }
            
            return new OkObjectResult(liveEvent.ResourceState == LiveEventResourceState.Running ? "On air" : "Off air");
        }
    }
}
```

## <a name="create-the-function-app"></a>Créer l’application de fonction

Créez l’application de fonction pour héberger la fonction. Son nom est celui que vous avez téléchargé précédemment, `MediaServicesLiveMonitorApp`.

```azurecli-interactive

az functionapp create --resource-group <your-resource-group-name> --consumption-plan-location your-region --runtime dotnet --functions-version 3 --name MediaServicesLiveMonitorApp --storage-account mediatest3store --assign-identity "[system]"

```

Recherchez `principalId` dans la réponse JSON :

```json
{
...  
"identity": {
//Note the principalId value for the following step
    "principalId": "00000000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-000000000000",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  }
...
```

## <a name="grant-the-function-app-access-to-the-media-services-account-resource"></a>Accorder à l’application de fonction l’accès à la ressource de compte Media Services

Pour cette demande :

- `assignee` est le `principalId` figurant dans la réponse JSON à la commande `az functionapp create`.
- `scope` est l’`id` figurant dans la réponse JSON à la commande `az ams account create`.  Examinez l’exemple de réponse JSON ci-dessus.

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Media Services Account Administrator" --scope "/subscriptions/<the-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.Media/mediaservices/<your-media-services-account-name>"
```

## <a name="publish-the-function"></a>Publier la fonction

```azurecli-interactive
func azure functionapp publish MediaServicesLiveMonitorApp
```

## <a name="validation"></a>Validation

Dans un navigateur, accédez à l’URL de la fonction, par exemple :

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

Cela doit avoir pour effet de retourner une erreur 404 (introuvable), car l’événement en direct n’existe pas encore.

### <a name="create-a-live-event"></a>Créer un événement en direct

```azurecli-interactive
az ams live-event create --resource-group test3 --account-name mediatest3 --name live1 --streaming-protocol RTMP
```

Dans un navigateur, accédez à l’URL de la fonction, par exemple :

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

Cela doit avoir pour effet d’afficher « Hors antenne ».

### <a name="start-the-live-event"></a>Démarrer l’événement en direct

Si vous démarrez l’événement en direct, la fonction doit retourner « À l’antenne ».

```azurecli-interactive
az ams live-event start live1
```

Cette fonction accorde l’accès à quiconque. La sécurisation de l’accès à la fonction Azure et le branchement d’un témoin « À l’antenne » sortent du cadre de ce document.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas l’intention d’utiliser les ressources que vous avez créées, supprimez le groupe de ressources.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
