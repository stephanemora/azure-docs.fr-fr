---
title: Guide pratique pour encoder et streamer des fichiers vidéo avec Node.JS
description: Guide pratique pour streamer des fichiers vidéo avec Node.JS Suivez les étapes de tutoriel pour créer un compte Azure Media Services, encoder un fichier et le diffuser en continu sur Lecteur multimédia Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, streamer, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: b7efa37747e6d116b4cf26b4cf54377037f22cdd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212754"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Guide pratique pour encoder et streamer des fichiers vidéo avec Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ce démarrage rapide vous montre combien il est facile de coder et de lancer la diffusion en continu de vidéos sur une grande variété de navigateurs et d’appareils à l’aide d’Azure Media Services. Un fichier vidéo d’entrée peut être spécifié à l’aide d’URL HTTPS, d’URL SAS ou de chemins de fichiers situés dans le stockage Blob Azure.

À la fin de ce guide de démarrage rapide, vous saurez :

- Encoder avec Node.JS
- Streamer avec Node.JS
- Charger un fichier à partir d’une URL HTTPS avec Node.JS
- Utiliser un lecteur client TLS ou DASH avec Node.JS

Dans cet article, l’exemple encode du contenu que vous mettez à disposition via une URL HTTPS. Notez qu’AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS.

![Lire la vidéo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Installez [Node.js](https://nodejs.org/en/download/)
- [Créer un compte Media Services](./create-account-howto.md).<br/>Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md) et enregistrez les informations d’identification. Vous en aurez besoin pour accéder à l’API.
- Parcourez la procédure [Configurer et se connecter à Node.js](./configure-connect-nodejs-howto.md) pour comprendre comment utiliser le SDK client Node.js.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez un dépôt GitHub qui contient l’exemple Node.js de streaming sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

L’exemple se trouve dans le dossier [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Ouvrez [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) dans votre projet téléchargé. Mettez à jour le fichier *sample.env* dans le dossier racine avec les valeurs et les informations d’identification que vous avez obtenues pour [accéder aux API](./access-api-howto.md). Renommez le fichier *sample.env* en *.env* (Oui, uniquement l’extension).

L’exemple effectue les actions suivantes :

1. Crée une **transformation** avec une [présélection de l’encodage sensible au contenu](./content-aware-encoding.md). Il vérifie d’abord si la transformation spécifiée existe.
1. Crée un **actif multimédia** de sortie qui est utilisé par le **travail** d’encodage pour contenir la sortie
1. Charge éventuellement un fichier local à l’aide du SDK Blob de stockage
1. Crée l’entrée de **travail** qui est basée sur une URL HTTPS ou un fichier chargé
1. Soumet le **travail** d’encodage à l’aide de l’entrée et de la sortie qui ont été créées
1. Vérifie l’état du travail
1. Télécharge la sortie de la tâche d’encodage dans un dossier local
1. Crée un **localisateur de streaming** à utiliser dans le lecteur
1. Crée des URL de streaming pour TLS et DASH
1. Lit le contenu dans une application de lecteur : Lecteur multimédia Azure

## <a name="run-the-sample"></a>Exécution de l'exemple

1. L’application télécharge les fichiers encodés. Créez un dossier où vous souhaitez placer les fichiers de sortie et mettez à jour la valeur de la variable **outputFolder** dans le fichier [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59). Elle est définie par défaut sur « Temp ».
1. Ouvrez une **invite de commandes**, puis accédez au répertoire de l’exemple.
1. Accédez au dossier AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Installez les packages utilisés dans le fichier *packages.json*.

    ```bash
    npm install 
    ```

1. Accédez au dossier *StreamFilesSample*.

    ```bash
    cd StreamFilesSample
    ```

1. Lancez Visual Studio Code à partir du dossier *AMSv3Samples*. (C’est nécessaire pour effectuer le lancement à partir du dossier où se trouvent le dossier *.vscode* et les fichiers *tsconfig.js*.)

    ```bash
    cd ..
    code .
    ```

Ouvrez le dossier *StreamFilesSample*, puis ouvrez le fichier *index.ts* dans l’éditeur Visual Studio Code.
Tout en étant dans le fichier *index.ts*, appuyez sur F5 pour lancer le débogueur.

## <a name="test-with-azure-media-player"></a>Tester avec le Lecteur multimédia Azure

Utilisez le Lecteur multimédia Azure pour tester le flux. Vous pouvez également utiliser n’importe quel lecteur compatible avec TLS ou DASH, comme le lecteur Shaka, HLS.js ou Dash.js.

Vous devez être en mesure de cliquer sur le lien généré dans l’exemple et de lancer le lecteur AMP avec le manifeste DASH déjà chargé.

> [!NOTE]
> Si un lecteur est hébergé sur un site https, veillez à mettre à jour l’URL vers « https ».

1. Ouvrez un navigateur web et accédez à [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Dans la zone **URL :** , collez l’une des valeurs d’URL de streaming que vous avez obtenues quand vous avez exécuté l’application. Vous pouvez coller l’URL au format TLS, Dash ou Smooth ; le Lecteur multimédia Azure basculera vers un protocole de streaming approprié pour effectuer la lecture sur votre appareil.
3. Appuyez sur **Mise à jour du Lecteur Windows Media**.

Le lecteur multimédia Azure peut être utilisé pour effectuer des tests, mais ne doit pas être utilisé dans un environnement de production.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes Media Services et de stockage que vous avez créés dans ce tutoriel rapide, supprimez le groupe de ressources.

Exécutez la commande CLI suivante :

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Documentation supplémentaire pour les développeurs pour Node.js sur Azure

- [Azure pour les développeurs JavaScript et Node.js](/azure/developer/javascript/)
- [Code source de Media Services dans le dépôt GitHub @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentation sur les packages Azure pour les développeurs Node.js](/javascript/api/overview/azure/)

## <a name="see-also"></a>Voir aussi

- [Codes d’erreur des tâches](/rest/api/media/jobs/get#joberrorcode).
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure pour les développeurs JavaScript et Node.js](/azure/developer/javascript/)
- [Code source de Media Services dans le dépôt @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Étapes suivantes

> [Concepts Media Services](concepts-overview.md)
