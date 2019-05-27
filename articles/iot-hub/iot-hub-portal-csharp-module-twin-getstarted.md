---
title: Prise en main de l’identité de module et du jumeau de module Azure IoT Hub (portail et .NET) | Microsoft Docs
description: Découvrez comment créer une identité de module et comment mettre à jour un jumeau de module à l’aide du portail et de .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 3b1872699b8b3ac72424f00cd74bb90b8b7be87f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873174"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Prise en main de l’identité de module et du jumeau de module IoT Hub à l’aide du portail et d’un appareil .NET

> [!NOTE]
> [Les identités de module et les jumeaux de module](iot-hub-devguide-module-twins.md) sont similaires aux identités d’appareil et aux jumeaux d’appareil Azure IoT Hub, mais offrent un plus grand niveau de détail. Contrairement à l’identité d’appareil et au jumeau d’appareil Azure IoT Hub qui permettent à l’application principale de configurer un appareil et d’obtenir une visibilité sur l’état de l’appareil, une identité de module et un jumeau de module fournissent ces fonctionnalités pour les composants individuels d’un appareil. Sur les appareils compatibles qui intègrent plusieurs composants, par exemple des appareils basés sur un système d’exploitation ou des appareils avec un microprogramme, ils permettent d’isoler la configuration et les conditions de chacun de ces composants.
>

Ce tutoriel vous apprendra à effectuer les opérations suivantes :

1. Créer une identité de module dans le portail.

2. Comment utiliser une mise à jour SDK .NET appareil jumeau de module à partir de votre appareil.

> [!NOTE]
> Pour plus d’informations sur les SDK Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal, consultez l’article [SDK Azure IoT](iot-hub-devguide-sdks.md).
>

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Récupérer la chaîne de connexion pour le hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Créer une identité de module dans le portail

Vous pouvez créer jusqu’à 20 identités de module dans une même identité d’appareil. Cliquez sur le bouton **Ajouter une identité de module** en haut de l’écran pour créer votre première identité de module, que vous appellerez **myFirstModule**.

  ![Détails sur l'appareil](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Enregistrez et cliquez sur l’identité de module que vous venez de créer. Vous pouvez visualiser les détails de l’identité de module. Enregistrez la clé primaire de la chaîne de connexion. Cette clé sera utilisée dans la section suivante, où vous allez configurer votre module sur l’appareil.

  ![Détails sur l'appareil](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Mettre à jour le jumeau de module à l’aide du SDK d’appareil .NET

Vous venez de créer votre identité de module dans votre IoT Hub. Vous allez maintenant tenter d’établir une communication entre votre appareil simulé et le cloud. Une fois que vous avez créé une identité de module, un jumeau de module est implicitement créé dans IoT Hub. Dans cette section, vous allez créer sur votre appareil simulé une application de console .NET qui met à jour les propriétés déclarées du jumeau de module.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution existante en utilisant le modèle de projet **Application console (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur 4.6.1 ou supérieur. Nommez le projet **UpdateModuleTwinReportedProperties**.

  ![Créer un projet Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installer le dernier SDK d’appareil .NET Azure IoT Hub

L’identité de module et le jumeau de module sont disponibles en préversion publique. Il est uniquement disponible dans les IoT Hub en version préliminaire kits device SDK. Dans Visual Studio, ouvrez Outils > Gestionnaire de package Nuget > Gérer les packages Nuget pour la solution. Recherchez Microsoft.Azure.Devices.Client. Assurez-vous que vous avez vérifié incluent la case à cocher en version préliminaire. Sélectionnez la version la plus récente et installez-la. Vous avez maintenant accès à toutes les fonctionnalités de module.

  ![Installer le kit SDK du service NET Azure IoT Hub V1.16.0-préversion-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Obtenir votre chaîne de connexion du module

Connexion à [Azure portal](https://portal.azure.com/). Accédez à votre IoT Hub, puis cliquez sur Appareils IoT. Recherchez et ouvrez myFirstDevice pour vérifier que myFirstModule a bien été créé. Copiez la chaîne de connexion du module. Vous en aurez besoin à l’étape suivante.

  ![Détails du module du Portail Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Créer l’application de console UpdateModuleTwinReportedProperties

Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion du module.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Ajoutez la méthode suivante **OnDesiredPropertyChanged** à la classe **Program** :

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
        Console.WriteLine("Sending current time as reported property");
        TwinCollection reportedProperties = new TwinCollection
        {
            ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
        };

        await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
    }
```

Enfin, ajoutez les lignes suivantes à la méthode **Main** :

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

        Console.WriteLine("Sending app start time as reported property");
        TwinCollection reportedProperties = new TwinCollection();
        reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

        Client.UpdateReportedPropertiesAsync(reportedProperties);
    }
    catch (AggregateException ex)
    {
        Console.WriteLine("Error in sample: {0}", ex);
    }

    Console.WriteLine("Waiting for Events.  Press enter to exit...");
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Cet exemple de code montre comment récupérer le jumeau de module et mettre à jour les propriétés déclarées avec le protocole AMQP. Dans la préversion publique, AMQP est pris en charge uniquement pour les opérations de jumeau de module.

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications. Dans Visual Studio, dans l’explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sur **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis choisissez l’action **Démarrer** pour l’application de console. Appuyez sur F5 pour démarrer les deux applications.

## <a name="next-steps"></a>Étapes suivantes

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Prise en main à l’aide de la sauvegarde de .NET et de périphérique .NET du jumeau d’identité et le module de module IoT Hub](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Bien démarrer avec IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)