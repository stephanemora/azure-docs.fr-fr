---
title: Démarrage rapide C# sur les flux d’appareil Azure IoT Hub (préversion) | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez exécuter deux exemples d’applications C# qui communiquent par le biais d’un flux d’appareil établi par le biais d’IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 0b39943b318afd6f9aabd6ab0711651d64e975cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121602"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Démarrage rapide : Communiquer avec des applications d’appareil en C# par le biais de flux d’appareil IoT Hub (préversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub prend actuellement en charge les flux d’appareil en tant que [fonctionnalité d’évaluation](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les [flux d’appareil IoT Hub](./iot-hub-device-streams-overview.md) permettent aux applications de service et d’appareil de communiquer de manière sécurisée à travers des pare-feux. Ce guide de démarrage rapide implique deux programmes C# qui exploitent des flux d’appareil pour envoyer des données dans les deux sens (écho).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour le moment, la préversion des flux d’appareil est uniquement prise en charge pour les hubs IoT créés dans les régions suivantes :

  - **USA Centre**
  - **EUAP USA Centre**

Les deux exemples d’applications que vous exécutez dans ce guide de démarrage rapide sont écrits à l’aide de C#. Votre machine de développement doit disposer du Kit SDK .NET Core 2.1.0 ou version ultérieure.

Vous pouvez télécharger le Kit SDK .NET Core pour plusieurs plateformes sur [.NET](https://www.microsoft.com/net/download/all).

Vous pouvez vérifier la version actuelle de C# sur votre machine de développement à l’aide de la commande suivante :

```
dotnet --version
```

Téléchargez l’exemple de projet C# à partir de https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip et extrayez l’archive ZIP. Vous en aurez besoin côté appareil et service.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour ajouter l’extension CLI IoT Hub et créer l’identité d’appareil. 

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

   **MyDevice** : il s’agit du nom donné à l’appareil inscrit. Utilisez MyDevice comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez également utiliser ce nom pour l’ensemble de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Exécutez les commandes suivantes dans Azure Cloud Shell pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Notez la chaîne de connexion de l’appareil, qui ressemble à l’exemple suivant :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.

3. Vous avez également besoin de la _chaîne de connexion du service_ de votre hub IoT pour permettre à l’application côté service de se connecter à votre hub IoT et d’établir un flux d’appareil. La commande suivante récupère cette valeur de votre hub IoT :

   **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Notez la valeur retournée, qui ressemble à ceci :

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Communiquer entre appareil et service par le biais de flux d’appareil

### <a name="run-the-service-side-application"></a>Exécuter l’application côté service

Accédez à `iot-hub/Quickstarts/device-streams-echo/service` dans votre dossier de projet décompressé. Vous aurez besoin des informations suivantes :

| Nom du paramètre | Valeur du paramètre |
|----------------|-----------------|
| `ServiceConnectionString` | Indiquez la chaîne de connexion de service de votre hub IoT. |
| `DeviceId` | Indiquez l’ID de l’appareil que vous avez créé précédemment, par exemple, MyDevice. |

Compilez et exécutez le code comme suit :

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Un délai d’expiration se produit si l’application côté appareil ne répond pas dans les temps.

### <a name="run-the-device-side-application"></a>Exécuter l’application côté appareil

Accédez au répertoire `iot-hub/Quickstarts/device-streams-echo/device` de votre dossier de projet décompressé. Vous aurez besoin des informations suivantes :

| Nom du paramètre | Valeur du paramètre |
|----------------|-----------------|
| `DeviceConnectionString` | Indiquez la chaîne de connexion de service de votre hub IoT. |

Compilez et exécutez le code comme suit :

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

À la fin de la dernière étape, le programme côté service lance un flux à votre appareil et, une fois établi, envoie un tampon de chaîne au service sur le flux. Dans cet exemple, le programme côté service renvoie simplement les mêmes données à l’appareil, ce qui démontre une communication bidirectionnelle réussie entre les deux applications. Reportez-vous à la figure ci-dessous.

Sortie de console côté appareil : ![Texte de remplacement](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Sortie de console côté appareil")

Sortie de console côté service : ![Texte de remplacement](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Sortie de console côté service")

Le trafic envoyé sur le flux est traité par tunnel par le biais d’IoT Hub au lieu d’être directement envoyé. Ce processus offre [ces avantages](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez configuré un hub IoT, inscrit un appareil, établi un flux d’appareil entre des applications C# côté appareil et service, puis utilisé le flux pour envoyer des données dans les deux sens entre les applications.

Utilisez les liens ci-dessous pour en savoir plus sur les flux d’appareil :

> [!div class="nextstepaction"]
> [Vue d’ensemble des flux d’appareil](./iot-hub-device-streams-overview.md)
