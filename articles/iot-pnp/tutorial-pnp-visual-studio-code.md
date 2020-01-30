---
title: Créer et tester un appareil IoT Plug-and-Play (préversion) | Microsoft Docs
description: Avec ce tutoriel destiné aux développeurs d’appareils, vous allez découvrir comment utiliser VS Code pour créer et tester un modèle de capacité d’appareil pour un appareil IoT Plug-and-Play (préversion).
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719719"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Tutoriel : Créer et tester un modèle de capacité d’appareil avec Visual Studio Code

Ce tutoriel destiné aux développeurs d’appareils montre comment utiliser Visual Studio Code pour créer un _modèle de capacité d’appareil_. Vous pouvez utiliser le modèle pour générer le code de squelette qui s’exécutera sur un appareil se connectant à une instance IoT Hub dans le cloud.

La section de ce tutoriel qui explique comment compiler le code de squelette généré suppose que vous utilisez Windows.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un modèle de capacité d’appareil
> * Générer le code de squelette de l’appareil à partir du modèle
> * Implémenter les stubs dans le code généré
> * Exécuter le code pour tester les interactions avec un hub IoT

## <a name="prerequisites"></a>Conditions préalables requises

Pour utiliser le modèle de capacité d’appareil dans le cadre de ce tutoriel, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/download) : VS Code est disponible pour plusieurs plateformes.
* Pack d’extension [Azure IoT Tools pour VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). Pour installer le pack d’extension dans VS Code, effectuez les étapes suivantes :

    1. Dans VS Code, sélectionnez l’onglet **Extensions**.
    1. Recherchez **Azure IoT Tools**.
    1. Sélectionnez **Installer**.

Pour compiler le code C généré sur Windows dans le cadre de ce tutoriel, vous avez besoin des éléments suivants :

* [Build Tools pour Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) avec les **outils de génération C++** et les charges de travail du **composant Gestionnaire de package NuGet**. Ou, si vous l'avez déjà, [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 avec les mêmes charges de travail.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Pour tester votre code d’appareil dans le cadre de ce tutoriel, vous avez besoin des éléments suivants :

* L’[explorateur Azure IoT](https://github.com/Azure/azure-iot-explorer/releases)
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modéliser votre appareil

Vous allez utiliser le _langage de définition pour les jumeaux numériques_ pour créer un modèle de capacité d’appareil. Un modèle se compose généralement de plusieurs fichiers de définition d’_interface_ et d’un fichier de modèle unique. **Azure IoT Tools pour VS Code** inclut des outils vous permettant de créer et de modifier ces fichiers JSON.

### <a name="create-the-interface-file"></a>Créer le fichier d’interface

Pour créer un fichier d’interface qui définit les capacités de votre appareil IoT dans VS Code :

1. Créez un dossier nommé **devicemodel**.

1. Lancez VS Code et appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez la commande **IoT Plug & Play : Créer l’interface**.

1. Localisez, puis sélectionnez le dossier **devicemodel** que vous avez créé.

1. Entrez **EnvironmentalSensor** pour le nom de l’interface, puis appuyez sur **Entrée**. VS Code crée l’exemple de fichier d’interface **EnvironmentalSensor.interface.json**.

1. Remplacez le contenu de ce fichier par le code JSON suivant et remplacez `{your name}` par une valeur unique dans le champ `@id`. Utilisez uniquement les caractères a-z, A-Z, 0-9 et le trait de soulignement. Pour plus d’informations, consultez la section [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format) (Format d’identificateur pour les jumeaux numériques). L’enregistrement de l’interface dans le référentiel nécessite un ID d’interface unique :

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Cette interface définit les propriétés de l’appareil telles que **Customer Name**, les types de données de télémétrie tels que **Temperature** et les commandes telles que **turnon**.

1. Ajoutez une capacité de commande nommée **blink** à la fin de ce fichier d’interface. Veillez à ajouter une virgule avant d’ajouter la commande. Essayez de taper la définition pour voir comment IntelliSense, l’autocomplétion et la validation vous aident à modifier une définition d’interface :

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Enregistrez le fichier .

### <a name="create-the-model-file"></a>Créer le fichier de modèle

Le fichier de modèle spécifie les interfaces implémentées par l’appareil IoT Plug-and-Play. Généralement, un modèle inclut au moins deux interfaces : une ou plusieurs interfaces qui définissent les capacités spécifiques de votre appareil et une interface standard que tous les appareils IoT Plug-and-Play doivent implémenter.

Pour créer un fichier de modèle qui spécifie les interfaces implémentées par votre appareil IoT Plug-and-Play dans VS Code :

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez la commande **IoT Plug & Play : Créer un modèle de capacité**. Entrez **SensorboxModel** pour le nom du modèle. VS Code crée l’exemple de fichier d’interface **SensorboxModel.capabilitymodel.json**.

1. Remplacez le contenu de ce fichier par le code JSON suivant et, dans le champ `@id` et l’interface `EnvironmentalSensor`, remplacez `{your name}` par la valeur que vous avez utilisée dans le fichier **EnvironmentalSensor.interface.json**. L’enregistrement de l’interface dans le référentiel nécessite un ID d’interface unique :

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Le modèle définit un appareil qui implémente votre interface **EnvironmentalSensor** et l’interface standard **DeviceInformation**.

1. Enregistrez le fichier .

### <a name="download-the-deviceinformation-interface"></a>Télécharger l’interface DeviceInformation

Avant de pouvoir générer le code de squelette à partir du modèle, vous devez créer une copie locale de l’interface **DeviceInformation** à partir du *référentiel de modèles public*. Le référentiel de modèles public contient déjà l’interface **DeviceInformation**.

Pour télécharger l’interface **DeviceInformation** à partir du référentiel de modèles public à l’aide de VS Code :

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, sélectionnez la commande **Ouvrir le référentiel de modèles**, puis sélectionnez **Ouvrir le référentiel de modèles public**.

1. Sélectionnez **Interfaces**, sélectionnez l’interface Informations sur l’appareil avec l’ID `urn:azureiot:DeviceManagement:DeviceInformation:1`, puis sélectionnez **Télécharger**.

Vous disposez maintenant des trois fichiers qui composent votre modèle de capacité d’appareil :

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Publier le modèle

Pour que l’explorateur Azure IoT puisse lire le modèle de capacité d’appareil, vous devez le publier dans votre référentiel d’entreprise. Pour le publier à partir de VS Code, vous avez besoin de la chaîne de connexion pour le référentiel d’entreprise :

1. Accédez au [portail Microsoft Azure Certified pour IoT](https://aka.ms/ACFI).

1. Utilisez votre _compte professionnel_ Microsoft pour vous connecter au portail.

1. Sélectionnez **Référentiel d’entreprise**, puis **Chaînes de connexion**.

1. Copiez la chaîne de connexion.

Pour ouvrir votre référentiel d’entreprise dans VS Code :

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez la commande **IoT Plug & Play : Ouvrir le référentiel de modèles**.

1. Sélectionnez **Ouvrir le référentiel de modèles organisationnels**, puis collez la chaîne de connexion.

1. Appuyez sur **Entrée** pour ouvrir le référentiel d’entreprise.

Pour publier votre modèle de capacité d’appareil et vos interfaces dans le référentiel d’entreprise :

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez la commande **IoT Plug & Play : Envoyer les fichiers vers le référentiel de modèles**.

1. Sélectionnez les fichiers **EnvironmentalSensor.interface.json** et **SensorboxModel.capabilitymodel.json**, puis cliquez sur **OK**.

Vos fichiers sont maintenant stockés dans votre référentiel d’entreprise.

## <a name="generate-code"></a>Générer le code

Vous pouvez utiliser **Azure IoT Tools pour VS Code** afin de générer du code de squelette C à partir de votre modèle. Pour générer le code de squelette dans VS Code :

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez la commande **IoT Plug & Play : Générer le stub de code d’appareil**.

1. Sélectionnez le fichier de modèle de capacité **SensorboxModel.capabilitymodel.json**.

1. Entrez le nom de projet **sensorbox_app**.

1. Choisissez le langage **ANSI C**.

1. Choisissez le mode de connexion **Avec une chaîne de connexion d’appareil IoT Hub**.

1. Choisissez **Projet CMake sur Windows** en tant que modèle de projet.

1. Choisissez **Via Vcpkg** pour inclure le kit de développement logiciel (SDK) de l'appareil.

VS Code génère le code de squelette C et enregistre les fichiers dans le dossier **sensorbox_app**, dans le dossier **modelcode**. VS Code ouvre une nouvelle fenêtre contenant les fichiers de code générés.

## <a name="update-the-generated-code"></a>Mettre à jour le code généré

Avant de pouvoir compiler et exécuter le code, vous devez implémenter les propriétés, les données de télémétrie et les commandes qui ont fait l’objet d’un stub.

Pour fournir des implémentations pour le code qui a fait l’objet d’un stub dans VS Code :

1. Ouvrez le fichier **SensorboxModel_impl.c**.

1. Ajoutez le code pour implémenter les fonctions qui ont fait l’objet d’un stub.

1. Enregistrez vos modifications.

## <a name="build-the-code"></a>Générer le code

Avant d’exécuter le code pour tester votre appareil IoT Plug-and-Play avec un hub IoT Azure, vous devez compiler le code.

Suivez les instructions du fichier **Readme.md** dans le dossier **sensorbox_app** pour compiler et exécuter le code sous Windows. La section suivante fournit des instructions sur la récupération de la chaîne de connexion d’appareil à utiliser quand vous exécutez le code de l’appareil.

## <a name="test-the-code"></a>Tester le code

Quand vous exécutez le code, il établit la connexion au hub IoT et commence à envoyer des exemples de valeurs de télémétrie et de propriétés. L’appareil répond également aux commandes envoyées à partir du hub IoT. Pour vérifier ce comportement :

1. Pour créer un hub IoT :

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Ajoutez un appareil à votre hub IoT et récupérez sa chaîne de connexion :

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Prenez note de la chaîne de connexion.

1. Dans une invite de commandes, accédez au dossier **azure-iot-sdk-c** dans lequel vous avez généré le SDK et les exemples. Accédez ensuite au dossier **cmake\\sensorbox_app\\Release**.

1. Exécutez la commande suivante :

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Utilisez l’explorateur Azure IoT pour interagir avec l’appareil IoT Plug-and-Play connecté à votre hub IoT. Pour plus d’informations, consultez [Installer et utiliser l’explorateur Azure IoT](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un appareil IoT Plug-and-Play prêt pour la certification, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Créer un appareil prêt pour la certification](tutorial-build-device-certification.md)
