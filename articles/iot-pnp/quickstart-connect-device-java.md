---
title: Connecter un exemple de code Java pour appareil IoT Plug-and-Play à IoT Hub | Microsoft Docs
description: Créez et exécutez un exemple de code pour appareil IoT Plug-and-Play, qui se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6dfd63f92b8c420a3c5281ee2962b211b8779dd1
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045055"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-java"></a>Démarrage rapide : Connecter un exemple d’application pour appareil IoT Plug-and-Play s’exécutant sur Windows à IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ce démarrage rapide montre comment créer un exemple d’application d’appareil IoT Plug-and-Play, comment le connecter à votre hub IoT, et comment utiliser l’outil Explorateur Azure IoT pour afficher la télémétrie qu’il envoie. L’exemple d’application est écrit en Java et est inclus dans l’Azure IoT device SDK pour Java. Un créateur de solutions peut utiliser l’outil Explorateur Azure IoT pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner du code d’appareil.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pour suivre ce démarrage rapide sur Windows, installez les logiciels suivants sur votre environnement Windows local :

* Java SE Development Kit 8. Dans [Prise en charge à long terme de Java pour Azure et Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable), sous **Prise en charge à long terme**, sélectionnez **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Téléchargement du code

Dans ce démarrage rapide, vous préparez un environnement de développement pour cloner et générer le Kit de développement logiciel (SDK) Java d’appareil Azure IoT Hub.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub de [kits de développement logiciel (SDK) et de bibliothèques Java Azure IoT](https://github.com/Azure/azure-iot-sdk-java) dans cet emplacement :

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>Générer le code

Sous Windows, accédez au dossier racine du dépôt du Kit de développement logiciel (SDK) Java cloné.

Exécutez la commande suivante pour générer l’exemple d’application :

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Pour en savoir plus sur l’exemple de configuration, consultez l’[exemple de fichier Lisez-moi](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md).

Accédez au dossier *\device\iot-device-samples\pnp-device-sample\thermostat-device-sample*.

Pour exécuter l’exemple d’application, utilisez la commande suivante :

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

L’appareil est désormais prêt à recevoir des commandes et des mises à jour de propriétés, et a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois l’exemple de client d’appareil démarré, utilisez l’outil Explorateur Azure IoT pour vérifier qu’il fonctionne.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Vérifier le code

Cet exemple implémente un appareil à thermostat IoT Plug-and-Play simple. Le modèle implémenté par cet exemple n’utilise pas de [composants](concepts-components.md) IoT Plug-and-Play. Le [fichier de modèle DTDL pour le thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) définit la télémétrie, les propriétés et les commandes que l’appareil implémente.

Le code d’appareil utilise la classe `DeviceClient` standard pour se connecter à votre hub IoT. L’appareil envoie l’ID de modèle du modèle DTDL qu’il implémente dans la demande de connexion. Un appareil qui envoie un ID de modèle est un appareil IoT Plug-and-Play :

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

L’ID de modèle est stocké dans le code, comme indiqué dans l’extrait de code suivant :

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Le code qui met à jour les propriétés, gère les commandes et envoie les données de télémétrie est identique au code d’un appareil qui n’utilise pas les conventions IoT Plug-and-Play.

L’exemple utilise une bibliothèque JSON pour analyser les objets JSON dans les charges utiles envoyées à partir de votre hub IoT :

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à un hub IoT. Pour en savoir plus sur la création d’une solution qui interagit avec vos appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide pratique pour Se connecter à un appareil et interagir avec celui-ci](./quickstart-service-node.md)