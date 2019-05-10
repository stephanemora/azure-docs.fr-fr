---
title: Boucle de rétroaction - Personalizer
titleSuffix: Azure Cognitive Services
description: Personnalisez le contenu de ce guide de démarrage rapide C# avec le service Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: f0aca3e387d675064cf798b4efdeb66cfe906520
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153545"
---
# <a name="quickstart-personalize-content-using-c"></a>Démarrage rapide : Personnaliser du contenu avec C# 

Affichez du contenu personnalisé dans ce guide de démarrage rapide C# avec le service Personalizer.

Cet exemple montre comment utiliser la bibliothèque cliente de personnalisation pour C# pour effectuer les actions suivantes : 

 * Classer une liste d’actions pour la personnalisation.
 * Indiquer la récompense à allouer à l’action classée en premier en fonction de la sélection de l’utilisateur pour l’événement spécifié.

Démarrer la personnalisation implique les étapes suivantes :

1. Référencement du SDK 
1. Écriture du code pour classer les actions que vous voulez montrer à vos utilisateurs
1. Écriture du code pour envoyer des récompenses pour entraîner la boucle.

## <a name="prerequisites"></a>Prérequis

* Vous devez avoir un [service Personalizer](how-to-settings.md) pour obtenir votre clé d’abonnement et l’URL du service d’émission de jetons. 
* [Visual Studio 2015 ou 2017](https://visualstudio.microsoft.com/downloads/).
* Le package NuGet du SDK Microsoft.Azure.CognitiveServices.Personalization. Vous trouverez ci-dessous des instructions d’installation.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Création d’une application console et référencement du SDK Personalizer 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Dans Visual Studio, créez une application de console Visual C#.
1. Installez le package NuGet de la bibliothèque cliente de personnalisation. Dans le menu, sélectionnez **Outils**, **Gestionnaire de package NuGet**, puis **Gérer les packages NuGet pour la solution**.
1. Sélectionnez l’onglet **Parcourir** puis, dans la zone **Rechercher**, tapez `Microsoft.Azure.CognitiveServices.Personalization`.
1. Sélectionnez **Microsoft.Azure.CognitiveServices.Personalization** quand il apparaît.
1. Cochez la case en regard du nom de votre projet, puis sélectionnez **Installer**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Ajouter le code, et placer vos clés Personalizer et Azure

1. Remplacez le contenu de Program.cs par le code suivant. 
1. Remplacez la valeur de `serviceKey` par votre clé d’abonnement Personalizer valide.
1. Remplacez `serviceEndpoint` par votre point de terminaison de service. Par exemple `https://westus2.api.cognitive.microsoft.com/`.
1. Exécutez le programme.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Ajouter du code pour classer les actions que vous voulez montrer à vos utilisateurs

Le code C# suivant est une liste complète pour passer des informations utilisateur, _features, et des informations sur votre contenu, _actions_, à Personalizer avec le SDK. Personalizer retourne l’action classée en premier à montrer à votre utilisateur.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
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

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
                },

                new RankableAction
                {
                    Id = "ice cream",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
                },

                new RankableAction
                {
                    Id = "juice",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
                },

                new RankableAction
                {
                    Id = "salad",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Exécuter le programme

Générez et exécutez le programme. Ce programme de démarrage rapide pose quelques questions pour recueillir les préférences de l’utilisateur, appelées « caractéristiques », puis fournit l’action classée en premier.

![Ce programme de démarrage rapide pose quelques questions pour recueillir les préférences de l’utilisateur, appelées « caractéristiques », puis fournit l’action classée en premier.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois le démarrage rapide terminé, supprimez tous les fichiers créés pour ce démarrage rapide. 

## <a name="next-steps"></a>Étapes suivantes

[Fonctionnement de Personalizer](how-personalizer-works.md)


