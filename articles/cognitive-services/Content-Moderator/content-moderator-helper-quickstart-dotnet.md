---
title: 'Démarrage rapide : Créer un client de modération pour .NET - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Renvoi d’un client Content Moderator à l’aide du SDK Azure Content Moderator pour .NET.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 08902df231fedd2c1c5653052540cb1cdabba9cf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224937"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>Démarrage rapide : Code d’assistance pour renvoyer un client Content Moderator

Cet article fournit des informations et des exemples de code pour vous aider à faire vos premiers pas avec le SDK Azure Content Moderator pour .NET afin de créer un client Content Moderator pour votre abonnement.

La bibliothèque est utilisée par d’autres démarrages rapides dans cette section.

Cet article part du principe que vous connaissez déjà Visual Studio et C#.

> [!IMPORTANT]
> Cette bibliothèque de classes contient du code servant à des fins de démonstration uniquement.
> Si vous adaptez ce code pour une utilisation en production, utilisez une méthode sécurisée pour stocker et utiliser votre clé d’abonnement Content Moderator.

## <a name="sign-up-for-content-moderator-services"></a>S’inscrire aux services Content Moderator

Avant de pouvoir utiliser les services Content Moderator par le biais de l’API REST ou du kit SDK, vous avez besoin d’une clé d’abonnement.
Consultez le guide de démarrage rapide [Essayer Content Moderator sur le web](quick-start.md) pour savoir comment obtenir la clé.

## <a name="create-your-visual-studio-project"></a>Créer votre projet Visual Studio

1. Créer un nouveau projet **Bibliothèque de classes (.NET Framework)**.

   Dans l’exemple de code, le projet se nomme **ModeratorHelper**.

1. Ajoutez une référence à l’assembly Framework **System.Configuration**.

### <a name="install-required-packages"></a>Installer les packages nécessaires

Installez les packages NuGet suivants :

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Créer le client Content Moderator

Remplacez le contenu du fichier ModeratorHelper.cs par le code suivant :

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> Mettez à jour les champs **AzureRegion** et **CMSubscriptionKey** avec les valeurs de votre identificateur de région et de votre clé d’abonnement.

Vous disposez désormais d’un moyen rapide pour créer un client Content Moderator pour votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

[Téléchargez la solution Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pour ce guide de démarrage rapide et d’autres guides de démarrage rapide Content Moderator pour .NET, puis commencez votre intégration.
