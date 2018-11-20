---
title: Diffuser en continu des fichiers vidéo avec Azure Media Services - .NET | Microsoft Docs
description: Suivez les étapes de ce démarrage rapide pour créer un compte Azure Media Services, encoder un fichier et le diffuser en continu sur Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, diffuser en continu
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: fc8fc1af51332df032e864c84791791a38bc8601
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612218"
---
# <a name="quickstart-stream-video-files---net"></a>Démarrage rapide : Diffuser en continu des fichiers vidéo - .NET

Ce démarrage rapide vous montre combien il est facile de coder et de lancer la diffusion en continu de vidéos sur une grande variété de navigateurs et d’appareils à l’aide d’Azure Media Services. Un contenu d’entrée peut être spécifié à l’aide d’URL HTTPS, d’URL SAP ou de chemins d’accès aux fichiers situés dans le Stockage Blob Azure.
Dans cette rubrique, l’exemple encode du contenu que vous mettez à disposition via une URL HTTPS. Notez qu’AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS.

À la fin du démarrage rapide, vous serez en mesure de diffuser en continu une vidéo.  

![Lire la vidéo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas Visual Studio, vous pouvez obtenir [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- Installez et utilisez l’interface CLI localement. Vous devez avoir Azure CLI 2.0 ou version ultérieure pour cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

    Actuellement, les commandes [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) ne fonctionnent pas toutes dans Azure Cloud Shell. Il est recommandé d’utiliser l’interface CLI localement.

- [Créer un compte Media Services](create-account-cli-how-to.md).

## <a name="download-the-sample"></a>Télécharger l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET de diffusion en continu sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

L’exemple se trouve dans le dossier [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

L’exemple effectue les actions suivantes :

1. Crée une transformation (vérifie d’abord si la transformation spécifiée existe). 
2. Crée une ressource de sortie qui est utilisée en tant que sortie du travail d’encodage.
3. Crée l’entrée du travail qui est basée sur une URL HTTPS.
4. Soumet le travail d’encodage à l’aide de l’entrée et de la sortie qui ont été créées précédemment.
5. Vérifie l’état du travail.
6. Crée un StreamingLocator.
7. Crée des URL de diffusion en continu.

Pour obtenir des explications sur chaque fonction dans l’exemple, examinez le code et lisez les commentaires dans [ce fichier source](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Lorsque vous exécutez l’application, les URL qui peuvent être utilisées pour lire la vidéo à l’aide de différents protocoles s’affichent. 

1. Appuyez sur Ctrl + F5 pour exécuter l’application *EncodeAndStreamFiles*.
2. Choisissez le protocole **HLS** d’Apple (se termine par *manifest(format=m3u8-aapl)*), puis copiez l’URL de diffusion en continu à partir de la console.

![Sortie](./media/stream-files-tutorial-with-api/output.png)

Le [code source](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) de l’exemple montre comment l’URL est construite. Pour générer cette URL, vous devez concaténer le nom d’hôte du point de terminaison de streaming et le chemin d’accès au localisateur de diffusion en continu.  

## <a name="test-with-azure-media-player"></a>Test avec Azure Media Player

Pour tester la diffusion en continu, cet article utilise le lecteur multimédia Azure. 

> [!NOTE]
> Si un lecteur est hébergé sur un site https, veillez à mettre à jour l’URL vers « https ».

1. Ouvrez un navigateur web et accédez à [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Dans le champ **URL :**, collez l’une des valeurs d’URL de diffusion en continu que vous avez obtenues lors de l’exécution de l’application. 
3. Appuyez sur **Mise à jour du Lecteur Windows Media**.

Le lecteur multimédia Azure peut être utilisé pour effectuer des tests, mais ne doit pas être utilisé dans un environnement de production. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes Media Services et de stockage que vous avez créés dans ce guide de démarrage rapide, supprimez le groupe de ressources.

Exécutez la commande CLI suivante :

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Examiner le code

Pour obtenir des explications sur chaque fonction dans l’exemple, examinez le code et lisez les commentaires dans [ce fichier source](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Le tutoriel [Charger, encoder et diffuser en continu des fichiers](stream-files-tutorial-with-api.md) vous donne un exemple de diffusion en continu plus élaboré, avec des explications détaillées. 

## <a name="multithreading"></a>Traitement multithread

Les Kits de développement logiciel (SDK) Azure Media Services v3 ne sont pas thread-safe. Lorsque vous travaillez avec une application multithread, vous devez générer un nouvel objet AzureMediaServicesClient par thread.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Charger, encoder et diffuser en continu des fichiers](stream-files-tutorial-with-api.md)
