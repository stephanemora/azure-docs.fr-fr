---
title: Charger des fichiers sur Azure IoT Hub à partir d’appareils avec .NET | Microsoft Docs
description: Comment charger des fichiers sur le cloud à partir d’un appareil avec Azure IoT device SDK pour .NET. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: df460ba4163b414ad954dce73125a6376f4cad6f
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112004580"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce tutoriel vous montre comment utiliser les capacités de chargement de fichiers d’IoT Hub à l’aide de l’exemple de chargement de fichiers .NET. 

Le guide de démarrage rapide [Envoyer des données de télémétrie à partir d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md) et le tutoriel [Envoyer des messages cloud-à-appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md) illustrent les fonctionnalités de messages appareil-à-cloud et cloud-à-appareil de base offertes par IoT Hub. Le tutoriel [Configurer le routage des messages avec IoT Hub](tutorial-routing.md) décrit un moyen de stocker de façon fiable les messages appareil-à-cloud dans le stockage d’objets blob Microsoft Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple :

* Fichiers volumineux qui contiennent des images

* Vidéos

* Données de vibration échantillonnées à une fréquence élevée

* Un certain type de données prétraitées

Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory](../data-factory/introduction.md) ou de la pile [Hadoop](../hdinsight/index.yml). Si vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez toujours exploiter la sécurité et la fiabilité d’IoT Hub. Ce didacticiel vous explique les procédures.

> [!NOTE]
> IoT Hub offre la prise en charge de nombreux langages (notamment C, Java, Python et JavaScript) et plateformes d’appareils par le biais des Kits Azure IoT device SDK. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Prérequis

* Visual Studio Code

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

* Téléchargez les exemples C# Azure IoT à partir de [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) et décompressez l’archive ZIP.

* Ouvrez le dossier *FileUploadSample* dans Visual Studio Code, puis ouvrez le fichier *FileUploadSample.cs*.

* Vérifiez que le port 8883 est ouvert dans votre pare-feu. L’exemple décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les différentes façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="examine-the-application"></a>Examiner l’application

Accédez au dossier *FileUploadSample* dans votre téléchargement d’exemples .NET. Ouvrez le dossier dans Visual Studio Code. Le dossier contient un fichier nommé *parameters.cs*. Si vous ouvrez ce fichier, vous verrez que le paramètre *p* est obligatoire et contient la chaîne de connexion. Vous pouvez spécifier le paramètre *t* si vous souhaitez modifier le protocole de transport. Le protocole par défaut est MQTT. Le fichier *program.cs* contient la fonction *main*. Le fichier *FileUploadSample.cs* contient la logique de l’exemple principal. *TestPayload.txt* est le fichier à charger dans votre conteneur de blobs.

## <a name="run-the-application"></a>Exécution de l'application

Vous êtes maintenant prêt à exécuter l’application.

1. Ouvrez une fenêtre de terminal dans Visual Studio Code.
1. Tapez les commandes suivantes :
    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your connection string}"
    ```

La sortie doit ressembler à ce qui suit :

```cmd/sh
  Uploading file TestPayload.txt
  Getting SAS URI from IoT Hub to use when uploading the file...
  Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
  Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
  Successfully uploaded the file to Azure Storage
  Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
  Time to upload file: 00:00:01.5077954.
  Done.
```

## <a name="verify-the-file-upload"></a>Vérifier le chargement du fichier

Procédez comme suit pour vérifier que *TestPayload.txt* a été chargé dans votre conteneur :

1. Dans le volet gauche de votre compte de stockage, sélectionnez **Conteneurs** sous **Stockage des données**.
1. Sélectionnez le conteneur dans lequel vous avez chargé *TestPayload.txt*.
1. Sélectionnez le dossier portant le nom de votre appareil.
1. Sélectionnez *TestPayload.txt*.
1. Téléchargez le fichier pour afficher son contenu localement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programmation](iot-hub-rm-template-powershell.md)

* [Présentation du SDK C](iot-hub-device-sdk-c-intro.md)

* [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/quickstart-linux.md)