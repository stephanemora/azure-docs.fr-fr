---
title: Charger les fichiers d’un appareil sur Azure IoT Hub avec Node | Microsoft Docs
description: Découvrez comment charger les fichiers d’un appareil sur le cloud avec le kit Azure IoT device SDK pour Node.js. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: a95737cd9d15b0ee21249f0db8d1bbb96fb8a3b2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710089"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce didacticiel explique les procédures suivantes :

* Fournissez en toute sécurité à un appareil un URI d’objet blob Azure pour le chargement d’un fichier.

* Utilisez les notifications de chargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur principal d’application.

Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) présente les fonctionnalités de messages appareil-à-cloud de base d’IoT Hub. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple :

* Fichiers volumineux qui contiennent des images
* Vidéos
* Données de vibration échantillonnées à une fréquence élevée
* Un certain type de données prétraitées.

Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory](../data-factory/introduction.md) ou de la pile [Hadoop](../hdinsight/index.yml). Lorsque vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez quand même exploiter la sécurité et la fiabilité d’IoT Hub.

À la fin de cet article, vous exécuterez deux applications de console Node.js :

* **FileUpload.js**, qui charge un fichier dans le stockage à l’aide d’un URI SAS fourni par votre hub IoT.

* **FileUploadNotification**, qui reçoit les notifications de chargement de fichier à partir de votre hub IoT.

> [!NOTE]
> IoT Hub offre la prise en charge de nombreux langages (notamment C, Java, Python et JavaScript) et plateformes d’appareils par le biais des Kits Azure IoT device SDK. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Prérequis

* Node.j version 10.0.x ou ultérieure. La version LTS est recommandée. Vous pouvez télécharger Node.js depuis [nodejs.org](https://nodejs.org).

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

* Vérifiez que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les différentes façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Charger un fichier à partir d’une application d’appareil

Dans cette section, créez une application d’appareil pour charger un fichier sur IoT Hub. Le code est basé sur le code disponible dans l’exemple [upload_to_blob_advanced.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/upload_to_blob_advanced.js) des exemples d’appareil du [SDK Azure IoT node.js](https://github.com/Azure/azure-iot-sdk-node).

1. Créez un dossier vide appelé `fileupload`.  Dans le dossier `fileupload`, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes.  Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

1. Dans l’invite de commandes du dossier `fileupload`, exécutez la commande suivante pour installer les packages SDK d’appareil **azure-iot-device**, **azure-iot-device-mqtt** et **@azure/storage-blob** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt @azure/storage-blob --save
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **FileUpload.js** dans le dossier `fileupload`, puis copiez le code suivant dans celui-ci.

    ```javascript
    'use strict';

    const Client = require('azure-iot-device').Client;
    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    const errors = require('azure-iot-common').errors;
    const path = require('path');

    const {
      AnonymousCredential,
      BlockBlobClient,
      newPipeline
    } = require('@azure/storage-blob');

    // make sure you set these environment variables prior to running the sample.
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    const localFilePath = process.env.PATH_TO_FILE;
    const storageBlobName = path.basename(localFilePath);

    async function uploadToBlob(localFilePath, client) {
      const blobInfo = await client.getBlobSharedAccessSignature(storageBlobName);
      if (!blobInfo) {
        throw new errors.ArgumentError('Invalid upload parameters');
      }

      const pipeline = newPipeline(new AnonymousCredential(), {
        retryOptions: { maxTries: 4 },
        telemetry: { value: 'HighLevelSample V1.0.0' }, // Customized telemetry string
        keepAliveOptions: { enable: false }
      });

      // Construct the blob URL to construct the blob client for file uploads
      const { hostName, containerName, blobName, sasToken } = blobInfo;
      const blobUrl = `https://${hostName}/${containerName}/${blobName}${sasToken}`;

      // Create the BlockBlobClient for file upload to the Blob Storage Blob
      const blobClient = new BlockBlobClient(blobUrl, pipeline);

      // Setup blank status notification arguments to be filled in on success/failure
      let isSuccess;
      let statusCode;
      let statusDescription;

      try {
        const uploadStatus = await blobClient.uploadFile(localFilePath);
        console.log('uploadStreamToBlockBlob success');

        // Save successful status notification arguments
        isSuccess = true;
        statusCode = uploadStatus._response.status;
        statusDescription = uploadStatus._response.bodyAsText;

        // Notify IoT Hub of upload to blob status (success)
        console.log('notifyBlobUploadStatus success');
      }
      catch (err) {
        isSuccess = false;
        statusCode = err.code;
        statusDescription = err.message;

        console.log('notifyBlobUploadStatus failed');
        console.log(err);
      }

      await client.notifyBlobUploadStatus(blobInfo.correlationId, isSuccess, statusCode, statusDescription);
    }

    // Create a client device from the connection string and upload the local file to blob storage.
    const deviceClient = Client.fromConnectionString(deviceConnectionString, Protocol);
    uploadToBlob(localFilePath, deviceClient)
      .catch((err) => {
        console.log(err);
      })
      .finally(() => {
        process.exit();
      });
    ```

1. Enregistrez et fermez le fichier **FileUpload.js**.

1. Copiez un fichier image dans le dossier `fileupload` et attribuez-lui un nom, tel que `myimage.png`.

1. Ajoutez des variables d’environnement pour la chaîne de connexion de votre appareil et le chemin du fichier que vous souhaitez charger. Vous avez obtenu la chaîne de connexion de l’appareil lorsque vous avez [inscrit l’appareil avec votre hub IoT](#register-a-new-device-in-the-iot-hub).
    
    - Pour Windows :

        ```cmd
        set DEVICE_CONNECTION_STRING={your device connection string}
        set PATH_TO_FILE={your image filepath}
        ```

    - Pour Linux/Bash :

        ```bash
        export DEVICE_CONNECTION_STRING="{your device connection string}"
        export PATH_TO_FILE="{your image filepath}"
        ```

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

Dans cet article, vous créez un service back-end pour recevoir les messages de notification de chargement de fichiers depuis le hub IoT que vous avez créé. Pour recevoir les messages de notification de chargement de fichiers, votre service a besoin de l'autorisation de **connexion de service**. Par défaut, chaque IoT Hub est créé avec une stratégie d’accès partagé nommée **service** qui accorde cette autorisation.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez créer une application console Node.js qui reçoit des messages de notification de chargement de fichiers envoyés par IoT Hub.

1. Créez un dossier vide appelé `fileuploadnotification`.  Dans le dossier `fileuploadnotification`, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes.  Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

1. Depuis votre invite de commandes, dans le dossier `fileuploadnotification`, exécutez la commande suivante pour installer le package SDK **azure-iothub** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **FileUploadNotification.js** dans le dossier `fileuploadnotification`.

1. Ajoutez les instructions `require` ci-dessous au début du fichier **FileUploadNotification.js** :

    ```javascript
    'use strict';

    const Client = require('azure-iothub').Client;
    ```

1. Lisez la chaîne de connexion de votre hub IoT à partir de l’environnement :

    ```javascript
    const connectionString = process.env.IOT_HUB_CONNECTION_STRING;
    ```

1. Ajoutez le code suivant pour créer un client de service à partir de la chaîne de connexion :

    ```javascript
    const serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Ouvrez le client et utilisez la fonction **getFileNotificationReceiver** pour recevoir les mises à jour d’état.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Enregistrez et fermez le fichier **FileUploadNotification.js**.

1. Ajouter une variable d’environnement pour votre chaîne de connexion IoT Hub. Vous avez copié cette chaîne précédemment dans [Get Obtenir la chaîne de connexion du hub IoT](#get-the-iot-hub-connection-string).
    
    - Pour Windows :

        ```cmd
        set IOT_HUB_CONNECTION_STRING={your iot hub connection string}
        ```

    - Pour Linux/Bash :

        ```bash
        export IOT_HUB_CONNECTION_STRING="{your iot hub connection string}"
        ```

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

Dans une invite de commandes, exécutez la commande suivante dans le dossier `fileuploadnotification` :

```cmd/sh
node FileUploadNotification.js
```

Dans une invite de commandes, exécutez la commande suivante dans le dossier `fileupload` :

```cmd/sh
node FileUpload.js
```

La sortie suivante provient de l’application **FileUpload** une fois le téléchargement terminé :

```output
uploadStreamToBlockBlob success
notifyBlobUploadStatus success
```

L’exemple de sortie suivant provient de l’application **FileUploadNotification** après la fin du téléchargement :

```output
Service client connected
File upload from device:
{"deviceId":"myDeviceId","blobUri":"https://{your storage account name}.blob.core.windows.net/device-upload-container/myDeviceId/image.png","blobName":"myDeviceId/image.png","lastUpdatedTime":"2021-07-23T23:27:06+00:00","blobSizeInBytes":26214,"enqueuedTimeUtc":"2021-07-23T23:27:07.2580791Z"}
```

## <a name="verify-the-file-upload"></a>Vérifier le chargement du fichier

Vous pouvez utiliser le portail pour afficher le fichier chargé dans le conteneur de stockage que vous avez configuré :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sur le volet gauche de votre compte de stockage, sélectionnez **Conteneurs**.
1. Sélectionnez le conteneur dans lequel vous avez chargé le fichier.
1. Sélectionnez le dossier portant le nom de votre appareil.
1. Sélectionnez l’objet blob dans lequel vous avez chargé votre fichier. Dans cet article, il s’agit de l’objet blob portant le même nom que votre fichier.  

    :::image type="content" source="./media/iot-hub-node-node-file-upload/view-uploaded-file.png" alt-text="Capture d’écran de l’affichage du fichier chargé dans le portail Azure.":::

1. Affichez les propriétés de l’objet blob sur la page qui s’ouvre. Vous pouvez sélectionner **Télécharger** pour télécharger le fichier et visualiser son contenu localement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programmation](iot-hub-rm-template-powershell.md)

* [Présentation du SDK C](iot-hub-device-sdk-c-intro.md)

* [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md)
