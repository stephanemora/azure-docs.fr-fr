---
title: Fichier Include
description: Fichier include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: e21dab310c41cf6aae0e201d7d1b8e78a8540a30
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255172"
---
[Documentation de référence](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer) | [Conceptuel sur plusieurs emplacements](..\concept-multi-slot-personalization.md) | [Exemples](https://aka.ms/personalizer/ms-dotnet)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="créez une ressource Personalizer"  target="_blank">créer une ressource Personalizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Personalizer. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

[!INCLUDE [Upgrade Personalizer instance to multi-slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core dans votre éditeur ou IDE favori.

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `new` dotnet pour créer une application console avec le nom `personalizer-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

À partir du répertoire de projet, ouvrez le fichier `Program.cs` dans votre éditeur ou IDE favori. Ajoutez les directives using suivantes :

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Text;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
```

## <a name="object-model"></a>Modèle objet

Pour demander le seul meilleur élément du contenu pour chaque emplacement, créez un **MultiSlotRankRequest**, puis envoyez une demande de publication à [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank). La réponse est ensuite analysée dans un **MultiSlotRankResponse**.

Pour envoyer un score de récompense à Personalizer, créez un **MultiSlotReward**, puis envoyez une demande de publication à [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward).

Ce guide de démarrage rapide n’accorde pas d’importance à la détermination du score de récompense. Dans un système de production, déterminer les éléments ayant un impact sur le [score de récompense](../concept-rewards.md) (et dans quelle mesure) peut être un processus complexe, que vous pouvez décider de modifier au fil du temps. Cette décision de conception doit être l’une des principales décisions à prendre pour votre architecture Personalizer.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes en envoyant des requêtes HTTP pour .NET :

* [Créer des URL de base](#create-base-urls)
* [API Multi-Slot Rank](#request-the-best-action)
* [API Multi-Slot Reward](#send-a-reward)


## <a name="create-base-urls"></a>Créer des URL de base

Dans cette section, vous allez effectuer deux opérations :
* Spécifier votre clé et votre point de terminaison
* Construire les URL de classement et de récompense

Commencez par ajouter les lignes suivantes à votre classe Program. Veillez à ajouter votre clé et votre point de terminaison à partir de votre ressource Personalizer.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
//Replace 'PersonalizationBaseUrl' and 'ResourceKey' with your valid endpoint values.
private const string PersonalizationBaseUrl = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>";
private const string ResourceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";
```

Ensuite, construisez les URL de classement et de récompense.

```csharp
private static string MultiSlotRankUrl = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/rank");
private static string MultiSlotRewardUrlBase = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/events/");
```

## <a name="get-content-choices-represented-as-actions"></a>Représenter les choix de contenu sous forme d’actions

Les actions représentent les choix de contenu parmi lesquels Personalizer doit sélectionner l’élément de contenu le mieux adapté. Ajoutez les méthodes suivantes à la classe Program pour représenter l’ensemble d’actions et leurs caractéristiques. 

```csharp
private static IList<Action> GetActions()
{
    IList<Action> actions = new List<Action>
    {
        new Action
        {
            Id = "Red-Polo-Shirt-432",
            Features =
            new List<object>() { new { onSale = "true", price = "20", category = "Clothing" } }
        },
        new Action
        {
            Id = "Tennis-Racket-133",
            Features =
            new List<object>() { new { onSale = "false", price = "70", category = "Sports" } }
        },
        new Action
        {
            Id = "31-Inch-Monitor-771",
            Features =
            new List<object>() { new { onSale = "true", price = "200", category = "Electronics" } }
        },
        new Action
        {
            Id = "XBox-Series X-117",
            Features =
            new List<object>() { new { onSale = "false", price = "499", category = "Electronics" } }
        }
    };
    return actions;
}
```

## <a name="get-slots"></a>Accéder aux emplacements

Les emplacements constituent la page avec laquelle l’utilisateur interagit. Personalizer décidera de l’action à afficher dans chacun des emplacements définis. Les actions peuvent être exclues d’emplacements spécifiques, comme `ExcludeActions`. `BaselineAction` est l’action par défaut pour l’emplacement qui aurait été affiché sans l’utilisation de Personalizer.


Ce guide de démarrage rapide offre des fonctionnalités d’emplacement simples. Dans les systèmes de production, il peut être important de déterminer et d’[évaluer](../concept-feature-evaluation.md) les [caractéristiques](../concepts-features.md).

```csharp
private static IList<Slot> GetSlots()
{
    IList<Slot> slots = new List<Slot>
    {
        new Slot
        {
            Id = "BigHeroPosition",
            Features = new List<object>() { new { size = "large", position = "left" } },
            ExcludedActions = new List<string>() { "31-Inch-Monitor-771" },
            BaselineAction = "Red-Polo-Shirt-432"

        },
        new Slot
        {
            Id = "SmallSidebar",
            Features = new List<object>() { new { size = "small", position = "right" } },
            ExcludedActions = new List<string>() { "Tennis-Racket-133" },
            BaselineAction = "XBox-Series X-117"
        },
    };

    return slots;
}
```

## <a name="classes-for-constructing-rankreward-requests-and-responses"></a>Classes pour la construction de requêtes et de réponses de classement/récompense

Ajoutez les classes imbriquées suivantes qui permettent de construire les requêtes de classement/récompense et d’analyser leurs réponses.

```csharp
private class Action
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class Slot
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public List<object> Features { get; set; }

    [JsonPropertyName("excludedActions")]
    public List<string> ExcludedActions { get; set; }

    [JsonPropertyName("baselineAction")]
    public string BaselineAction { get; set; }
}
```

```csharp
private class Context
{
    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class MultiSlotRankRequest
{
    [JsonPropertyName("contextFeatures")]
    public IList<Context> ContextFeatures { get; set; }

    [JsonPropertyName("actions")]
    public IList<Action> Actions { get; set; }

    [JsonPropertyName("slots")]
    public IList<Slot> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }

    [JsonPropertyName("deferActivation")]
    public bool DeferActivation { get; set; }
}
```

```csharp
private class MultiSlotRankResponse
{
    [JsonPropertyName("slots")]
    public IList<SlotResponse> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }
}
```

```csharp
private class SlotResponse
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("rewardActionId")]
    public string RewardActionId { get; set; }
}
```

```csharp
private class MultiSlotReward
{
    [JsonPropertyName("reward")]
    public List<SlotReward> Reward { get; set; }
}
```

```csharp
private class SlotReward
{
    [JsonPropertyName("slotId")]
    public string SlotId { get; set; }

    [JsonPropertyName("value")]
    public float Value { get; set; }
}
```

## <a name="get-user-preferences-for-context"></a>Obtenir les préférences utilisateur pour le contexte

Ajoutez les méthodes suivantes à la classe Program pour obtenir une entrée d’utilisateur à partir de la ligne de commande pour l’heure de la journée et le type d’appareil sur lequel se trouve l’utilisateur. Elles seront utilisées comme des caractéristiques de contexte.

```csharp
static string GetTimeOfDayForContext()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetDeviceForContext()
{
    string[] deviceFeatures = new string[] { "mobile", "tablet", "desktop" };

    Console.WriteLine("\nWhat is the device type (enter number)? 1. Mobile 2. Tablet 3. Desktop");
    if (!int.TryParse(GetKey(), out int deviceIndex) || deviceIndex < 1 || deviceIndex > deviceFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + deviceFeatures[0] + ".");
        deviceIndex = 1;
    }

    return deviceFeatures[deviceIndex - 1];
}
```

Les deux méthodes utilisent la méthode `GetKey` pour lire la sélection de l’utilisateur à partir de la ligne de commande.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

```csharp
private static IList<Context> GetContext(string time, string device)
{
    IList<Context> context = new List<Context>
    {
        new Context
        {
            Features = new {timeOfDay = time, device = device }
        }
    };

    return context;
}
```

## <a name="make-http-requests"></a>Effectuer des requêtes HTTP

Ajoutez ces fonctions pour envoyer des requêtes de publication au point de terminaison Personalizer pour les appels de classement et de récompense à plusieurs emplacements.

```csharp
private static async Task<MultiSlotRankResponse> SendMultiSlotRank(HttpClient client, string rankRequestBody, string rankUrl)
{
    try
    {
    var rankBuilder = new UriBuilder(new Uri(rankUrl));
    HttpRequestMessage rankRequest = new HttpRequestMessage(HttpMethod.Post, rankBuilder.Uri);
    rankRequest.Content = new StringContent(rankRequestBody, Encoding.UTF8, "application/json");
    HttpResponseMessage response = await client.SendAsync(rankRequest);
    response.EnsureSuccessStatusCode();
    MultiSlotRankResponse rankResponse = JsonSerializer.Deserialize<MultiSlotRankResponse>(await response.Content.ReadAsByteArrayAsync());
    return rankResponse;
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message);
        Console.WriteLine("Please make sure multi-slot feature is enabled. To do so, follow multi-slot Personalizer documentation to update your loop settings to enable multi-slot functionality.");
        throw;
    }
}
```

```csharp
private static async Task SendMultiSlotReward(HttpClient client, string rewardRequestBody, string rewardUrlBase, string eventId)
{
    string rewardUrl = String.Concat(rewardUrlBase, eventId, "/reward");
    var rewardBuilder = new UriBuilder(new Uri(rewardUrl));
    HttpRequestMessage rewardRequest = new HttpRequestMessage(HttpMethod.Post, rewardBuilder.Uri);
    rewardRequest.Content = new StringContent(rewardRequestBody, Encoding.UTF8, "application/json");

    await client.SendAsync(rewardRequest);
}
```

## <a name="create-the-learning-loop"></a>Créer la boucle d’apprentissage

La boucle d’apprentissage de Personalizer est un cycle d’appels [MultiSlotRank](#request-the-best-action) et [MultiSlotReward](#send-a-reward). Dans ce guide de démarrage rapide, chaque appel Rank en vue de personnaliser le contenu est suivi d’un appel Reward qui indique à Personalizer l’efficacité du service.

Le code ci-après applique, en boucle, le cycle suivant : il demande à l’utilisateur d’indiquer ses préférences à partir de la ligne de commande, il envoie ces informations à Personalizer en vue de sélectionner le contenu le mieux adapté pour chaque emplacement, il présente la sélection au client qui peut faire son choix dans la liste, puis il envoie un score de récompense à Personalizer, en indiquant l’efficacité du service concernant le choix du contenu.

```csharp
static async Task Main(string[] args)
{
    Console.WriteLine($"Welcome to this Personalizer Quickstart!\n" +
            $"This code will help you understand how to use the Personalizer APIs (multislot rank and multislot reward).\n" +
            $"Each iteration represents a user interaction and will demonstrate how context, actions, slots, and rewards work.\n" +
            $"Note: Personalizer AI models learn from a large number of user interactions:\n" +
            $"You won't be able to tell the difference in what Personalizer returns by simulating a few events by hand.\n" +
            $"If you want a sample that focuses on seeing how Personalizer learns, see the Python Notebook sample.");

    IList<Action> actions = GetActions();
    IList<Slot> slots = GetSlots();

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", ResourceKey);
        int iteration = 1;
        bool runLoop = true;
        do
        {
            Console.WriteLine($"\nIteration: {iteration++}");
            string timeOfDayFeature = GetTimeOfDayForContext();
            string deviceFeature = GetDeviceForContext();

            IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

            string eventId = Guid.NewGuid().ToString();

            string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
            {
                ContextFeatures = context,
                Actions = actions,
                Slots = slots,
                EventId = eventId,
                DeferActivation = false
            });

            //Ask Personalizer what action to show for each slot
            MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);

            MultiSlotReward multiSlotRewards = new MultiSlotReward()
            {
                Reward = new List<SlotReward>()
            };

            for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
            {
                Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
                SlotReward reward = new SlotReward()
                {
                    SlotId = multiSlotRankResponse.Slots[i].Id
                };

                string answer = GetKey();

                if (answer == "Y")
                {
                    reward.Value = 1;
                    Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
                }
                else if (answer == "N")
                {
                    reward.Value = 0;
                    Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
                }
                else
                {
                    reward.Value = 0;
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
                }
                multiSlotRewards.Reward.Add(reward);
            }

            string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

            // Send the reward for the action based on user response for each slot.
            await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);

            Console.WriteLine("\nPress q to break, any other key to continue:");
            runLoop = !(GetKey() == "Q");
        } while (runLoop);
    }
}
```
Dans les sections suivantes, examinez de plus près les appels de classement et de récompense.
Ajoutez les méthodes suivantes, qui [obtiennent les choix de contenu](#get-content-choices-represented-as-actions), [obtiennent des emplacements](#get-slots) et [envoient des requêtes de classement et de récompense à plusieurs emplacements](#make-http-requests) avant d’exécuter le fichier de code :

* `GetActions`
* `GetSlots`
* `GetTimeOfDayForContext`
* `GetTimeOfDayForContext`
* `GetKey`
* `GetContext`
* `SendMultiSlotRank`
* `SendMultiSlotReward`

Ajoutez les classes suivantes, qui [construisent les corps des requêtes de classement/récompense et analysent leurs réponses](#classes-for-constructing-rankreward-requests-and-responses) avant d’exécuter le fichier de code :

* `Action`
* `Slot`
* `Context`
* `MultiSlotRankRequest`
* `MultiSlotRankResponse`
* `SlotResponse`
* `MultiSlotReward`
* `SlotReward`

## <a name="request-the-best-action"></a>Demander l’action la mieux adaptée

Pour traiter la requête Rank, le programme demande les préférences de l’utilisateur afin de créer un `context` avec les choix de contenu. Le corps de la requête contient le contexte, les actions et les emplacements avec leurs caractéristiques respectives. La méthode `SendMultiSlotRank` utilise un client HTTP, le corps de la requête et l’URL pour envoyer la requête.

Ce guide de démarrage rapide utilise des caractéristiques de contexte simples basées sur l’heure de la journée et l’appareil de l’utilisateur. Dans les systèmes de production, il peut être important de déterminer et d’[évaluer](../concept-feature-evaluation.md) les [actions et caractéristiques](../concepts-features.md).

```csharp
string timeOfDayFeature = GetTimeOfDayForContext();
string deviceFeature = GetDeviceForContext();

IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

string eventId = Guid.NewGuid().ToString();

string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
{
    ContextFeatures = context,
    Actions = actions,
    Slots = slots,
    EventId = eventId,
    DeferActivation = false
});

//Ask Personalizer what action to show for each slot
MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);
```

## <a name="send-a-reward"></a>Envoyer une récompense

Pour obtenir le score de récompense pour la requête Reward, le programme récupère la sélection de l’utilisateur pour chaque emplacement à partir de la ligne de commande, attribue une valeur numérique (score de récompense) à la sélection, puis envoie à l’API Reward, l’ID d’événement unique et le score de récompense pour chaque emplacement sous la forme d’une valeur numérique. Une récompense n’a pas besoin d’être définie pour chaque emplacement.

Dans le cadre de ce guide de démarrage rapide, un simple numéro est attribué en tant que score de récompense : 0 ou 1. Dans les systèmes de production, il peut être important de déterminer ce qui doit être envoyé à l’appel [Reward](../concept-rewards.md) (et à quel moment) selon vos besoins.

```csharp
MultiSlotReward multiSlotRewards = new MultiSlotReward()
{
    Reward = new List<SlotReward>()
};

for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
{
    Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
    SlotReward reward = new SlotReward()
    {
        SlotId = multiSlotRankResponse.Slots[i].Id
    };

    string answer = GetKey();

    if (answer == "Y")
    {
        reward.Value = 1;
        Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
    }
    else if (answer == "N")
    {
        reward.Value = 0;
        Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
    }
    else
    {
        reward.Value = 0;
        Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
    }
    multiSlotRewards.Reward.Add(reward);
}

string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

// Send the reward for the action based on user response for each slot.
await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);
```

## <a name="run-the-program"></a>Exécuter le programme

Exécutez l’application avec la commande `run` dotnet à partir de votre répertoire d’application.

```console
dotnet run
```

![Ce programme de démarrage rapide pose quelques questions pour recueillir les préférences de l’utilisateur, appelées « caractéristiques », puis fournit l’action classée en premier.](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


Le [code source associé à ce guide de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer/multislot-quickstart) est disponible.
