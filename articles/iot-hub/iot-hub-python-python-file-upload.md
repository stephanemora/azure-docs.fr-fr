---
title: Charger les fichiers d’un appareil sur Azure IoT Hub avec Python | Microsoft Docs
description: Guide pratique pour charger les fichiers d’un appareil sur le cloud avec Azure IoT device SDK pour Python. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110748"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Cet article explique comment utiliser les [fonctionnalités de chargement d’IoT Hub](iot-hub-devguide-file-upload.md) pour charger un fichier sur le [Stockage Blob Azure](../storage/index.yml). Ce didacticiel explique les procédures suivantes :

* Fournir en toute sécurité un conteneur de stockage pour le chargement d’un fichier

* Utiliser le client Python pour charger un fichier par le biais de votre hub IoT

Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md) présente les fonctionnalités de messages appareil-à-cloud de base d’IoT Hub. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Lorsque vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez quand même exploiter la sécurité et la fiabilité d’IoT Hub.

> [!NOTE]
> À l’heure actuelle, le kit IoT Hub Python SDK prend uniquement en charge le chargement de fichiers basés sur des caractères, tels que les fichiers **.txt**.

À la fin de ce didacticiel, vous allez exécuter l’application console Python :

* **FileUpload.py**, qui charge un fichier sur le stockage à l’aide du kit Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Ce guide utilise le kit SDK Python v1 déconseillé, car la fonctionnalité de chargement de fichiers n’a pas encore été implémentée dans le nouveau kit SDK v2.

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Vérifiez que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les différentes façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Charger un fichier à partir d’une application d’appareil

Dans cette section, créez l’application d’appareil pour charger un fichier sur IoT Hub.

1. À partir de votre invite de commandes, exécutez la commande suivante pour installer le package **azure-iothub-device-client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. À l’aide d’un éditeur de texte, créez un fichier de test que vous allez charger dans le stockage blob.

    > [!NOTE]
    > À l’heure actuelle, le kit IoT Hub Python SDK prend uniquement en charge le chargement de fichiers basés sur des caractères, tels que les fichiers **.txt**.

3. À l’aide d’un éditeur de texte, créez un fichier **FileUpload.py** dans votre dossier de travail.

4. Ajoutez les variables et instructions `import` ci-dessous au début du fichier **FileUpload.py**. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Dans votre fichier, remplacez `[Device Connection String]` par la chaîne de connexion de l’appareil de votre hub IoT. Remplacez `[Full path to file]` par le chemin d’accès au fichier test que vous avez créé ou à tout fichier de votre appareil que vous souhaitez télécharger. Remplacez `[File name for storage]` par le nom que vous voulez donner à votre fichier après qu’il a été chargé dans le stockage Blob. 

6. Créez un rappel pour la fonction **upload_blob** :

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Ajoutez le code suivant pour connecter le client et charger le fichier. Ajoutez également la routine `main` :

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Enregistrez et fermez le fichier **UploadFile.py**.

## <a name="run-the-application"></a>Exécution de l'application

Vous êtes maintenant prêt à exécuter l’application.

1. À partir d’une invite de commandes dans votre dossier de travail, exécutez la commande suivante :

    ```cmd/sh
    python FileUpload.py
    ```

2. La capture d’écran ci-dessous montre la sortie de l’application **FileUpload** :

    ![Sortie de l’application simulated-device](./media/iot-hub-python-python-file-upload/1.png)

3. Vous pouvez utiliser le portail pour afficher le fichier chargé dans le conteneur de stockage que vous avez configuré :

    ![Fichier chargé](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programmation](iot-hub-rm-template-powershell.md)

* [Présentation du SDK C](iot-hub-device-sdk-c-intro.md)

* [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md)
