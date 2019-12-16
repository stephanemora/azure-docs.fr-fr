---
title: 'Démarrage rapide : Bibliothèque de client Personalizer pour .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Commencez à utiliser la bibliothèque de client Personalizer pour .NET à l’aide d’une boucle d’apprentissage.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 411bd82ade2ca7b904b36a3a4408c1a00852fc2c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927837"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Démarrage rapide : Bibliothèque de client Personalizer pour .NET

Affichez du contenu personnalisé dans ce guide de démarrage rapide C# avec le service Personalizer.

Commencez à utiliser la bibliothèque de client Personalizer pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.

 * Classer une liste d’actions pour la personnalisation.
 * Envoyer le score de récompense indiquant la réussite de l’action classée en premier.

[Documentation de référence](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Exemples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="using-this-quickstart"></a>Utilisation de ce guide de démarrage rapide

Plusieurs étapes sont nécessaires pour utiliser ce guide de démarrage rapide :

* Dans le portail Azure, créer une ressource Personalizer
* Dans le portail Azure, pour la ressource Personalizer, dans la page **Configuration**, changez la fréquence de mise à jour du modèle.
* Dans un éditeur de code, créer un fichier de code et le modifier
* À partir de la ligne de commande ou du terminal, installer le SDK
* À partir de la ligne de commande ou du terminal, exécuter le fichier de code

## <a name="create-a-personalizer-azure-resource"></a>Créer une ressource Azure Personalizer

Créez une ressource pour Personalizer en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle sera disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, créez [deux variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) :

* `PERSONALIZER_RESOURCE_KEY` pour la clé de ressource
* `PERSONALIZER_RESOURCE_ENDPOINT` pour le point de terminaison de ressource

Dans le portail Azure, les valeurs de clé et de point de terminaison sont disponibles à partir de la page de **démarrage rapide**.

## <a name="change-the-model-update-frequency"></a>Changer la fréquence de mise à jour du modèle

Dans le portail Azure, dans la ressource Personalizer dans la page **Configuration**, affectez à la **fréquence de mise à jour du modèle** la valeur 10 secondes. Avec cette courte durée, le service est entraîné rapidement, ce qui vous permet de voir comment la première action change pour chaque itération.

![Changer la fréquence de mise à jour du modèle](./media/settings/configure-model-update-frequency-settings.png)

Quand une boucle Personalizer est instanciée pour la première fois, aucun modèle n’est disponible puisqu’il n’y a pas encore d’appels d’API Reward à partir desquels s’entraîner. Les appels Rank retourneront des probabilités égales pour chaque élément. Votre application doit toujours classer le contenu à l’aide de la sortie de RewardActionId.

## <a name="create-a-new-c-application"></a>Créer une application C#

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

## <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Dans le répertoire de l’application, installez la bibliothèque de client Personalizer pour .NET avec la commande suivante :

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

## <a name="object-model"></a>Modèle objet

Le client Personalizer est un objet [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient votre clé.

Pour demander un classement du contenu, créez une classe [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview), puis transmettez-la à la méthode [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview). La méthode Rank retourne un RankResponse contenant le contenu classé. 

Pour envoyer une récompense à Personalizer, créez une classe [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview), puis transmettez-la à la méthode [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview). 

Dans le cadre de ce guide de démarrage rapide, il n’est pas important de déterminer la récompense. Dans un système de production, déterminer les éléments ayant un impact sur le [score de récompense](concept-rewards.md) (et dans quelle mesure) peut être un processus complexe, que vous pouvez décider de modifier au fil du temps. Cette décision de conception doit être l’une des principales décisions à prendre pour votre architecture Personalizer. 

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Personalizer pour .NET :

* [Créer un client Personalizer](#create-a-personalizer-client)
* [Demander un classement](#request-a-rank)
* [Envoyer une récompense](#send-a-reward)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier **Program.cs** dans votre éditeur ou votre IDE favori. Remplacez le code `using` existant par les directives `using` suivantes :

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Ajouter des informations sur la ressource Personalizer

Dans la classe **Program**, créez des variables pour le point de terminaison et la clé Azure de votre ressource, extraits à partir des variables d’environnement `PERSONALIZER_RESOURCE_KEY` et `PERSONALIZER_RESOURCE_ENDPOINT`. Si vous avez créé les variables d’environnement après le lancement de l’application, l’éditeur, l’IDE ou l’interpréteur de commandes les exécutant doit être fermé et rechargé pour y accéder. Les méthodes seront créées ultérieurement dans le cadre de ce guide de démarrage rapide.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Créer un client Personalizer

Ensuite, créez une méthode pour retourner un client Personalizer. Le paramètre de la méthode est `PERSONALIZER_RESOURCE_ENDPOINT` et l’ApiKey est `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Obtenir des produits alimentaires sous forme d’actions à classer

Les actions représentent les choix de contenu qui doivent être classés par Personalizer. Ajoutez les méthodes suivantes à la classe Program pour représenter l’ensemble d’actions à classer.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Obtenir les préférences utilisateur pour le contexte

Ajoutez les méthodes suivantes à la classe Program pour obtenir les entrées d’un utilisateur à partir de la ligne de commande : heure de la journée et préférence alimentaire actuelle. Elles seront utilisées comme contexte lors du classement des actions.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Les deux méthodes utilisent la méthode `GetKey` pour lire la sélection de l’utilisateur à partir de la ligne de commande. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Créer la boucle d’apprentissage

La boucle d’apprentissage Personalizer est un cycle d’appels de classement et de récompense. Dans ce guide de démarrage rapide, chaque appel de classement, qui personnalise le contenu, est suivi d’un appel de récompense, qui indique à Personalizer avec quelle efficacité le service a classé le contenu. 

Le code ci-après dans la méthode `main` du programme applique, en boucle, le cycle suivant : il demande à l’utilisateur d’indiquer ses préférences à partir de la ligne de commande, envoie ces informations à Personalizer en vue de leur classement, présente la sélection classée au client, qui peut faire son choix dans la liste, puis envoie une récompense à Personalizer, indiquant avec quelle efficacité le service a classé la sélection.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Ajoutez les méthodes suivantes, qui [obtiennent les choix de contenu](#get-food-items-as-rankable-actions) avant d’exécuter le fichier de code :

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-a-rank"></a>Demander un classement

Pour traiter la demande de classement, le programme demande les préférences de l’utilisateur pour créer un `currentContent` des choix de contenu. Le processus peut créer du contenu à exclure du classement, indiqué en tant que `excludeActions`. La demande de classement a besoin des éléments suivants pour recevoir la réponse classée : actions, currentContext, excludeActions et un ID d’événement de classement unique (en tant que GUID). 

Ce guide de démarrage rapide utilise des caractéristiques de contexte simples basées sur l’heure de la journée et les préférences alimentaires de l’utilisateur. Dans les systèmes de production, il peut être important de déterminer et d’[évaluer](concept-feature-evaluation.md) les [actions et caractéristiques](concepts-features.md).  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Envoyer une récompense

Pour traiter la demande de récompense, le programme récupère la sélection de l’utilisateur à partir de la ligne de commande, attribue une valeur numérique à chaque sélection, puis envoie l’ID d’événement de classement unique et la valeur numérique à la méthode de récompense.

Dans le cadre de ce guide de démarrage rapide, un simple numéro est attribué en tant que récompense : 0 ou 1. Dans les systèmes de production, il peut être important de déterminer ce qui doit être envoyé à l’appel de [récompense](concept-rewards.md) (et à quel moment) selon vos besoins spécifiques. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Exécuter le programme

Exécutez l’application avec la commande `run` dotnet à partir de votre répertoire d’application.

```console
dotnet run
```

![Ce programme de démarrage rapide pose quelques questions pour recueillir les préférences de l’utilisateur, appelées « caractéristiques », puis fournit l’action classée en premier.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

Le [code source de ce guide de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) est disponible dans le dépôt GitHub d’exemples Personalizer.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Fonctionnement de Personalizer](how-personalizer-works.md)

* [Qu’est-ce que Personalizer ?](what-is-personalizer.md)
* [Où utiliser Personalizer ?](where-can-you-use-personalizer.md)
* [Dépannage](troubleshooting.md)

