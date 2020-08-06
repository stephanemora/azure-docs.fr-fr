---
title: Connecter l’exemple de code d’appareil de composant C# IoT Plug-and-Play en préversion à IoT Hub | Microsoft Docs
description: Créez et exécutez l’exemple de code d’appareil C# IoT Plug-and-Play en préversion qui utilise plusieurs composants et se connecte à un hub IoT. Utilisez l’outil Azure IoT Explorer pour afficher les informations envoyées par l’appareil au hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 67b71399332fb29a277381a8c2806dbe7fb31d85
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552116"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Tutoriel : Connecter une application d’appareil multicomposant IoT Plug-and-Play s’exécutant sur Windows à IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ce tutoriel vous montre comment créer un exemple d’application d’appareil IoT Plug-and-Play avec des composants et une interface racine, comment le connecter à votre hub IoT, et comment utiliser l’outil Explorateur Azure IoT pour voir les informations qu’il envoie au hub. L’exemple d’application est écrit en C# et est inclus dans le SDK Azure IoT Device pour C#. Un créateur de solutions peut utiliser l’outil Explorateur Azure IoT pour comprendre les fonctionnalités d’un appareil IoT Plug-and-Play sans avoir besoin d’examiner le code d’appareil.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel sur Windows, vous devez installer les logiciels suivants sur votre environnement Windows local :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

Pour interagir avec l’exemple d’appareil dans la deuxième partie de ce tutoriel, vous utilisez l’outil **Explorateur Azure IoT**. [Téléchargez et installez la dernière version de l’Explorateur Azure IoT](./howto-use-iot-explorer.md) pour votre système d’exploitation.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Exécutez la commande suivante pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce tutoriel :

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Vous pouvez également utiliser l’outil Explorateur Azure IoT pour rechercher la chaîne de connexion du hub IoT.

Exécutez la commande suivante pour obtenir la _chaîne de connexion d’appareil_ pour l’appareil que vous avez ajouté au hub. Prenez note de cette chaîne de connexion, car vous l’utiliserez plus loin dans ce tutoriel :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Téléchargement du code

Dans ce tutoriel, vous préparez un environnement de développement que vous pouvez utiliser pour cloner et générer le SDK Azure IoT Hub Device C#.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [Azure IoT C# SDKs and Libraries](https://github.com/Azure/azure-iot-sdk-csharp) à cet emplacement :

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>Générer le code

Ouvrez le fichier solution **azureiot.sln** dans Visual Studio 2019 et définissez le projet **TemperatureController** comme projet de démarrage. Dans l’**Explorateur de solutions**, vous pouvez trouver ce fichier projet dans **iothub > device > samples**.

Vous pouvez maintenant générer l’exemple dans Visual Studio et l’exécuter en mode débogage.

## <a name="run-the-device-sample"></a>Exécuter l’exemple d’appareil

Créez une variable d’environnement appelée **IOTHUB_DEVICE_CONNECTION_STRING** pour stocker la chaîne de connexion à l’appareil que vous avez notée précédemment.

Pour effectuer le suivi de l’exécution du code dans Visual Studio sur Windows, ajoutez un point d’arrêt sur la fonction `main` dans le fichier program.cs.

L’appareil est désormais prêt à recevoir des commandes et des mises à jour de propriétés, et a commencé à envoyer des données de télémétrie au hub. Laissez l’exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

Une fois l’exemple de client d’appareil démarré, utilisez l’outil Explorateur Azure IoT pour vérifier qu’il fonctionne.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Vérifier le code

Cet exemple implémente un appareil contrôleur de température IoT Plug-and-Play. Le modèle implémenté par cet exemple utilise [plusieurs composants](concepts-components.md). Le [fichier de modèle DTDL (Digital Twins Definition Language) pour l’appareil de contrôle de température](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) définit la télémétrie, les propriétés et les commandes implémentées par l’appareil.

Le code de l’appareil se connecte à votre hub IoT en utilisant la méthode `CreateFromConnectionString` standard. L’appareil envoie l’ID de modèle du modèle DTDL qu’il implémente dans la demande de connexion. Un appareil qui envoie un ID de modèle est un appareil IoT Plug-and-Play :

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

L’ID de modèle est stocké dans le code, comme indiqué dans l’extrait de code suivant :

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Une fois que l’appareil se connecte à votre hub IoT, le code inscrit les gestionnaires de commandes. La commande `reboot` est définie dans l’interface racine. La commande `getMaxMinReport` est définie dans chacun des deux composants de thermostat :

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

Il existe des gestionnaires distincts pour les mises à jour des propriétés souhaitées sur les deux composants de thermostat :

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

L’exemple de code envoie des données de télémétrie depuis chaque composant de thermostat :

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

La méthode `SendTemperature` utilise la classe `PnpHhelper` pour créer des messages pour chaque composant :

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

La classe `PnpHelper` contient d’autres exemples de méthodes que vous pouvez utiliser avec un modèle à plusieurs composants.

Utilisez l’outil Explorateur Azure IoT pour visualiser les données de télémétrie et les propriétés des deux composants de thermostat :

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Appareil à plusieurs composants dans l’Explorateur Azure IoT":::

Vous pouvez également utiliser l’outil Explorateur Azure IoT pour appeler des commandes dans l’un ou l’autre des deux composants de thermostat ou dans l’interface racine.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment connecter un appareil IoT Plug-and-Play avec des composants à un hub IoT. Pour découvrir plus d’informations sur les modèles d’appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation avec la préversion d’IoT Plug-and-Play](concepts-developer-guide.md)
