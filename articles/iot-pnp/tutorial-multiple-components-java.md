---
title: Connecter un exemple de code d’appareil de composant Java IoT Plug-and-Play en préversion à IoT Hub | Microsoft Docs
description: Créez et exécutez un exemple de code d’appareil Java IoT Plug-and-Play en préversion qui utilise plusieurs composants et se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351776"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Tutoriel : Connecter un exemple d’application d’appareil multicomposant IoT Plug-and-Play en préversion à IoT Hub (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ce tutoriel montre comment créer un exemple d’application d’appareil IoT Plug-and-Play incluant plusieurs composants, comment le connecter à votre hub IoT et comment utiliser Azure CLI pour afficher les données de télémétrie qu’il envoie. L’exemple d’application est écrit en Java et est inclus dans l’Azure IoT device SDK pour Java. Un créateur de solutions peut utiliser Azure CLI pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner du code d’appareil.

Ce tutoriel vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play avec des composants et une interface racine, comment le connecter à votre hub IoT, et comment utiliser l’outil Explorateur Azure IoT pour voir les informations qu’il envoie au hub. L’exemple d’application est écrit en Java et est inclus dans l’Azure IoT device SDK pour Java. Un créateur de solutions peut utiliser l’outil Explorateur Azure IoT pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner du code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel sur Windows, vous devez installer les logiciels suivants sur votre environnement Windows local :

* Java SE Development Kit 8. Dans [Prise en charge à long terme de Java pour Azure et Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable), sous **Prise en charge à long terme**, sélectionnez **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

Pour interagir avec l’exemple d’appareil dans la deuxième partie de ce démarrage rapide, vous utilisez l’outil **Explorateur Azure IoT**. [Téléchargez et installez la dernière version de l’Explorateur Azure IoT](./howto-use-iot-explorer.md) pour votre système d’exploitation.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Vous pouvez également utiliser l’outil Explorateur Azure IoT pour rechercher la chaîne de connexion du hub IoT.

Exécutez la commande suivante pour obtenir la _chaîne de connexion_ à l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Téléchargement du code

Dans ce tutoriel, vous préparez un environnement de développement que vous pouvez utiliser pour cloner et générer le SDK Azure IoT Hub Device Java.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub de [kits de développement logiciel (SDK) et de bibliothèques Java Azure IoT](https://github.com/Azure/azure-iot-sdk-java) dans cet emplacement :

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Attendez-vous à ce que cette opération prenne plusieurs minutes.

## <a name="build-the-code"></a>Générer le code

Sous Windows, accédez au dossier racine du dépôt du Kit de développement logiciel (SDK) Java cloné. Accédez ensuite au dossier *\device\iot-device-samples\pnp-device-sample\temerature-controller-device-sample*.

Exécutez la commande suivante pour générer l’exemple d’application :

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Créez une variable d’environnement nommée **IOTHUB_DEVICE_CONNECTION_STRING** pour stocker la chaîne de connexion à l’appareil que vous avez notée précédemment.

Pour exécuter l’exemple d’application, utilisez la commande suivante :

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

L’appareil est désormais prêt à recevoir des commandes et des mises à jour de propriétés, et a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois l’exemple de client d’appareil démarré, utilisez l’outil Explorateur Azure IoT pour vérifier qu’il fonctionne.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Vérifier le code

Cet exemple implémente un appareil contrôleur de température IoT Plug-and-Play. Le modèle implémenté par cet exemple utilise [plusieurs composants](concepts-components.md). Le [fichier de modèle DTDL (Digital Twins Definition Language) pour l’appareil de contrôle de température](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) définit la télémétrie, les propriétés et les commandes implémentées par l’appareil.

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

Une fois que l’appareil se connecte à votre hub IoT, le code inscrit les gestionnaires de commandes.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Il existe des gestionnaires distincts pour les mises à jour des propriétés souhaitées sur les deux composants de thermostat :

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

L’exemple de code envoie des données de télémétrie depuis chaque composant de thermostat :

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

La méthode `sendTemperatureReading` utilise la classe `PnpHhelper` pour créer des messages pour chaque composant :

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

La classe `PnpHelper` contient d’autres exemples de méthodes que vous pouvez utiliser avec un modèle à plusieurs composants.

Utilisez l’outil Explorateur Azure IoT pour visualiser les données de télémétrie et les propriétés des deux composants de thermostat :

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Appareil à plusieurs composants dans l’Explorateur Azure IoT":::

Vous pouvez également utiliser l’outil Explorateur Azure IoT pour appeler des commandes dans l’un ou l’autre des deux composants de thermostat ou dans l’interface racine.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment connecter un appareil IoT Plug-and-Play avec des composants à un hub IoT. Pour découvrir plus d’informations sur les modèles d’appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation avec la préversion d’IoT Plug-and-Play](concepts-developer-guide.md)
