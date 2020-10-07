---
title: Interagir avec un appareil IoT Plug-and-Play connecté à une solution Azure IoT (Java) | Microsoft Docs
description: Utilisez Java pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution Azure IoT et pour interagir avec lui.
author: ericmitt
ms.author: ericmitt
ms.date: 9/17/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: cd618cf5f2f82b9c87981e961ed401f3409ec9d4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580779"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-java"></a>Démarrage rapide : Interagir avec un appareil IoT Plug-and-Play connecté à votre solution (Java)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug-and-Play simplifie l’IoT en vous permettant d’interagir avec les fonctionnalités d’un appareil sans avoir connaissance de l’implémentation de l’appareil sous-jacent. Ce démarrage rapide vous montre comment utiliser Java pour vous connecter à un appareil IoT Plug-and-Play connecté à votre solution et le contrôler.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pour suivre ce démarrage rapide sur Windows, installez les logiciels suivants sur votre environnement Windows local :

* Java SE Development Kit 8. Dans [Prise en charge à long terme de Java pour Azure et Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true), sous **Prise en charge à long terme**, sélectionnez **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonez le référentiel SDK avec l’exemple de code

Si vous avez terminé [Démarrage rapide : Connecter un exemple d’application pour appareil IoT Plug-and-Play s’exécutant sur Windows à IoT Hub (Java)](quickstart-connect-device-java.md), vous avez déjà cloné le dépôt.

Ouvrez une invite de commandes dans le répertoire de votre choix. Exécutez la commande suivante pour cloner le dépôt GitHub [SDK Microsoft Azure IoT pour Java](https://github.com/Azure/azure-iot-sdk-java) à cet emplacement :

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Créer et exécuter l’exemple d’appareil

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Pour en savoir plus sur l’exemple de configuration, consultez l’[exemple de fichier Lisez-moi](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

Dans ce démarrage rapide, vous utilisez un exemple de thermostat écrit en Java en tant qu’appareil IoT Plug-and-Play. Pour exécuter l’exemple d’appareil :

1. Ouvrez une fenêtre de terminal, puis accédez au dossier local contenant le dépôt SDK Microsoft Azure IoT pour Java que vous avez cloné depuis GitHub.

1. Cette fenêtre de terminal est utilisée en tant que terminal **device**. Accédez au dossier racine du dépôt cloné. Installez toutes les dépendances en exécutant la commande suivante :

1. Exécutez la commande suivante pour générer l’application d’appareil exemple :

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Pour exécuter l’application d’appareil exemple, accédez au dossier *device\iot-device-samples\pnp-device-sample\thermostat-device-sample* et exécutez la commande suivante :

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

L’appareil, désormais prêt à recevoir des commandes et des mises à jour de propriétés, a commencé à envoyer des données de télémétrie au hub. Laissez l’appareil exemple s’exécuter pendant que vous effectuez les étapes suivantes.

## <a name="run-the-sample-solution"></a>Exécuter l’exemple de solution

Dans [Configurer votre environnement pour les guides de démarrage rapide et tutoriels IoT Plug-and-Play](set-up-environment.md), vous avez créé deux variables d’environnement pour configurer l’exemple afin qu’il se connecte à votre hub IoT et à votre appareil :

* **IOTHUB_CONNECTION_STRING** : la chaîne de connexion de hub IoT que vous avez notée précédemment.
* **DEVICE_ID** : `"my-pnp-device"`.

Dans ce démarrage rapide, vous utilisez un exemple de solution IoT écrit en Java pour interagir avec l’exemple d’appareil que vous venez de configurer.

> [!NOTE]
> Cet exemple utilise l’espace de noms **com.microsoft.azure.sdk.iot.service.*;** à partir du **client du service IoT Hub**. Pour en savoir plus sur la récupération de l’ID de modèle, consultez le [Guide du développeur](concepts-developer-guide-device-csharp.md).

1. Ouvrez une autre fenêtre de terminal à utiliser comme terminal **service**.

1. Dans le dépôt du kit SDK Java cloné, accédez au dossier *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Pour exécuter l’exemple d’application de service, exécutez la commande suivante :

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-digital-twin"></a>Obtenir le jumeau numérique

L’extrait de code suivant montre comment récupérer le jumeau d’appareil dans le service :

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-digital-twin"></a>Mettre à jour un jumeau numérique

L’extrait de code suivant montre comment utiliser un *correctif* pour mettre à jour les propriétés par le biais du jumeau numérique :

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

La sortie de l’appareil montre comment l’appareil répond à cette mise à jour de propriété.

### <a name="invoke-a-command"></a>Appeler une commande

L’extrait de code suivant montre comment appeler une commande sur l’appareil :

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

La sortie de l’appareil montre comment l’appareil répond à cette commande.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à connecter un appareil IoT Plug-and-Play à une solution IoT. Pour découvrir plus d’informations sur les modèles d’appareils IoT Plug-and-Play, consultez :

> [!div class="nextstepaction"]
> [Guide du développeur pour la modélisation d’IoT Plug-and-Play](concepts-developer-guide-device-csharp.md)
