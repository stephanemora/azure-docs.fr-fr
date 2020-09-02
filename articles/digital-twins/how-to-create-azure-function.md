---
title: Configurer une fonction Azure pour le traitement des données
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une fonction Azure qui peut accéder à des jumeaux numériques et être déclenchée par ceux-ci.
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 66f514f4c5d299ef11efda541f16f4ef2fe61aed
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930160"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Connecter des applications Azure Functions pour le traitement des données

Dans la préversion, la mise à jour des jumeaux numériques basée sur les données s’effectue à l’aide de [**routes d’événements**](concepts-route-events.md) par le biais de ressources de calcul, telles qu’[Azure Functions](../azure-functions/functions-overview.md). Une fonction Azure peut être utilisée pour mettre à jour un jumeau numérique en réponse à :
* Des données de télémétrie d’appareil provenant d’IoT Hub
* La modification d’une propriété ou autres données provenant d’un autre jumeau numérique au sein du graphe jumeau

Cet article explique comment créer une fonction Azure en vue de l’utiliser avec Azure Digital Twins. 

Voici un aperçu des étapes qui vont être présentées :

1. Créer une application Azure Functions dans Visual Studio
2. Écrire une fonction Azure avec un déclencheur [Event Grid](../event-grid/overview.md)
3. Ajouter du code d’authentification à la fonction (pour pouvoir accéder à Azure Digital Twins)
4. Publier l’application de fonction dans Azure
5. Configurer l’accès de [sécurité](concepts-security.md). Pour que la fonction Azure puisse recevoir un jeton d’accès au moment de l’exécution en vue d’accéder au service, vous devez configurer Managed Service Identity dans l’application de fonction.

Le reste de cet article explique chacune des étapes de configuration d’une fonction Azure.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Créer une application Azure Functions dans Visual Studio

Dans Visual Studio 2019, sélectionnez *Fichier > Nouveau projet*. Recherchez le modèle *Azure Functions*, sélectionnez-le, puis appuyez sur « Suivant ».

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio : boîte de dialogue Nouveau projet":::

Spécifiez un nom pour l’application de fonction, puis appuyez sur « Créer ».

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio : boîte de dialogue Configurer le projet":::

Sélectionnez le *déclencheur Event Grid*, puis appuyez sur « Créer ».

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio : boîte de dialogue montrant le déclencheur de projet Azure Functions":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Écrire une fonction Azure avec un déclencheur Event Grid

Le code suivant crée une fonction Azure courte que vous pouvez utiliser pour journaliser les événements : 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Il s’agit de votre fonction Azure de base.

### <a name="run-and-debug-the-azure-function-app"></a>Exécuter et déboguer une application de fonction Azure

Vous pouvez maintenant compiler et exécuter la fonction. Même si les fonctions Azure sont destinées à s’exécuter dans le cloud, vous pouvez également les exécuter et les déboguer localement.

Pour plus d’informations à ce sujet, consultez [*Déboguer localement un déclencheur Event Grid*](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Ajouter le SDK Azure Digital Twins à votre application de fonction Azure

L’application de fonction interagit avec Azure Digital Twins à l’aide de la [bibliothèque de client Azure IoT Digital Twins pour .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Pour utiliser le SDK, vous devez inclure les packages suivants dans votre projet :
* `Azure.DigitalTwins.Core` (version `1.0.0-preview.2`)
* `Azure.Identity`

Pour configurer le pipeline du SDK Azure en vue de configurer correctement Azure Functions, vous aurez également besoin des éléments suivants :
* `Azure.Net.Http`
* `Azure.Core`

Selon les outils que vous choisissez, vous pouvez procéder à la configuration à l’aide du gestionnaire de package Visual Studio ou de l’outil en ligne de commande `dotnet`. 

Ajoutez les instructions using qui suivent à votre fonction Azure.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Ajouter du code d’authentification à la fonction Azure

Ensuite, ajoutez le code d’authentification qui permettra à la fonction d’accéder à Azure Digital Twins.

Ajoutez des variables à votre classe de fonction pour les valeurs suivantes : 
* L’ID d’application Azure Digital Twins
* L’URL de votre instance Azure Digital Twins. Il est recommandé de lire l’URL du service à partir d’une variable d’environnement, plutôt que de la coder en dur dans la fonction.
* Une variable statique destinée à contenir une instance HttpClient. HttpClient est relativement coûteux à créer. Nous voulons donc éviter d’avoir à le faire pour chaque appel de fonction.

Ajoutez également une variable locale à l’intérieur de votre fonction afin de stocker votre instance du client Azure Digital Twins dans le projet de fonction. Vous *ne devez pas* transformer cette variable en variable statique dans votre classe.

Enfin, modifiez la signature de la fonction pour la rendre asynchrone.

Une fois ces modifications effectuées, le code de votre fonction doit ressembler à ce qui suit :

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Pour que votre application Functions puisse accéder à Azure Digital Twins, elle doit disposer d’une identité managée par le système et disposer des autorisations nécessaires pour accéder à votre instance Azure Digital Twins.

Utilisez la commande suivante pour créer l’identité gérée par le système. Prenez note du champ *principalId* dans la sortie.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Utilisez la valeur *principalId* dans la commande suivante afin d'attribuer l'identité de l'application de fonction au rôle *Propriétaire Azure Digital Twins (préversion)* pour votre instance d'Azure Digital Twins. L'instance sera ainsi autorisée à effectuer des activités de plan de données.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Pour plus d’informations sur les identités managées, consultez [*Guide pratique pour utiliser des identités managées avec App Service et Azure Functions*](../app-service/overview-managed-identity.md).

Enfin, vous pouvez rendre l’URL de votre instance Azure Digital Twins accessible à votre fonction en définissant une variable d’environnement. Pour plus d’informations, consultez [*Variables d’environnement*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> L’URL de l’instance Azure Digital Twins est créée en ajoutant *https://* au début du *nom d’hôte* de votre instance Azure Digital Twins. Pour afficher le nom d’hôte, ainsi que toutes les propriétés de votre instance, vous pouvez exécuter `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Publier l’application de fonction Azure

Pour publier l’application de fonction dans Azure, sélectionnez le projet de fonction (et non la solution) dans l’Explorateur de solutions, puis choisissez *Publier*.

L’onglet suivant s’affiche :

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio : boîte de dialogue de publication de la fonction, page 1":::

Sélectionnez ou créez un plan App Service à utiliser avec Azure Functions. Si vous n’êtes pas sûr de savoir comment procéder, commencez par utiliser le plan de consommation par défaut.

> [!IMPORTANT] 
> La publication d’une fonction Azure entraîne des frais supplémentaires dans votre abonnement, indépendamment d’Azure Digital Twins.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio : boîte de dialogue de publication de la fonction, page 2":::

Dans la page suivante, entrez le nom souhaité pour la nouvelle application de fonction, un groupe de ressources, ainsi que d’autres informations.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Configurer l’accès de sécurité pour l’application de fonction Azure

Le squelette de fonction Azure des exemples précédents nécessite qu’un jeton de porteur lui soit transmis, afin de pouvoir s’authentifier auprès d’Azure Digital Twins. Pour garantir que ce jeton du porteur sera transmis, vous devez configurer [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) pour l’application de fonction. Cette opération ne doit être effectuée qu’une seule fois pour chaque application de fonction.

Pour cela, ouvrez le [portail Azure](https://portal.azure.com/), puis accédez à votre application de fonction.

Sous l’onglet *Fonctionnalités de la plateforme*, sélectionnez *Identité* :

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Portail Azure : sélection de l’option Identité pour une fonction Azure":::

Dans la page Identité, configurez le bouton bascule *État* sur *Activé*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Portail Azure : activation de l’état de l’identité":::

Notez également l’**ID d’objet** qui figure dans cette page, car vous en aurez besoin dans la section suivante.

### <a name="assign-access-roles"></a>Attribuer des rôles d’accès

Étant donné qu’Azure Digital Twins utilise le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources (pour plus d’informations, consultez [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md)), vous devez également ajouter un rôle pour chaque application de fonction que vous souhaitez autoriser à accéder à Azure Digital Twins.

Pour affecter un rôle, vous avez besoin de l’**ID de ressource** de l’instance Azure Digital Twins que vous avez créée. Si vous ne l’avez pas enregistré lors de la création de votre instance, vous pouvez le récupérer à l’aide de cette commande :

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
L’ID de ressource sera inclus dans la sortie, et se présentera sous la forme d’une chaîne longue nommée « id » commençant par « /subscriptions/… ».

Utilisez l’ID de ressource avec l’ID d’objet de la fonction Azure de tout à l’heure dans la commande ci-dessous :

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu les étapes permettant de configurer une fonction Azure en vue d’une utilisation avec Azure Digital Twins. Ensuite, vous pouvez vous abonner à votre fonction Azure pour Event Grid afin d’écouter un point de terminaison. Ce point de terminaison peut être :
* Un point de terminaison Event Grid attaché à Azure Digital Twins pour traiter les messages provenant d’Azure Digital Twins (tels que les messages de changement de propriété, les messages de données de télémétrie générés par les [jumeaux numériques](concepts-twins-graph.md) dans le graphique jumeau ou les messages concernant le cycle de vie)
* Des rubriques du système IoT utilisées par IoT Hub pour envoyer des données de télémétrie et d’autres événements d’appareil
* Un point de terminaison Event Grid qui reçoit des messages d’autres services

Dans la suite, vous allez voir comment créer une fonction Azure de base pour ingérer des données IoT Hub dans Azure Digital Twins :
* [*Guide pratique : Ingérer la télémétrie depuis IoT Hub*](how-to-ingest-iot-hub-data.md)