---
title: Prise en main de l’identité de module et du jumeau de module Azure IoT Hub (.NET) | Microsoft Docs
description: Découvrez comment créer une identité de module et comment mettre à jour un jumeau de module à l’aide des SDK IoT pour .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 60ed1f6d6b3904055c01aa32b69299a5872c38ac
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404556"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Bien démarrer avec le jumeau de module et l’identité de module IoT Hub en utilisant un backend .NET et un appareil .NET

> [!NOTE]
> [Les identités de module et les jumeaux de module](iot-hub-devguide-module-twins.md) sont similaires aux identités d’appareil et aux jumeaux d’appareil Azure IoT Hub, mais offrent un plus grand niveau de détail. Contrairement à l’identité d’appareil et au jumeau d’appareil Azure IoT Hub qui permettent à l’application principale de configurer un appareil et d’obtenir une visibilité sur l’état de l’appareil, une identité de module et un jumeau de module fournissent ces fonctionnalités pour les composants individuels d’un appareil. Sur les appareils compatibles qui intègrent plusieurs composants, par exemple des appareils basés sur un système d’exploitation ou des appareils avec un microprogramme, ils permettent d’isoler la configuration et les conditions de chacun de ces composants.

À la fin de ce didacticiel, vous disposerez de deux applications de console .NET :

* **CreateIdentities**, qui crée une identité d’appareil, une identité de module et une clé de sécurité associée pour connecter votre appareil et vos clients de module.

* **UpdateModuleTwinReportedProperties**, qui envoie à votre IoT Hub les propriétés à jour de votre jumeau de module.

> [!NOTE]
> Pour plus d’informations sur les SDK Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal, consultez l’article [SDK Azure IoT](iot-hub-devguide-sdks.md).

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio.

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Mettre à jour le jumeau de module à l’aide du SDK d’appareil .NET

Dans cette section, vous allez créer sur votre appareil simulé une application de console .NET qui met à jour les propriétés déclarées du jumeau de module.

1. **Créez un projet Visual Studio :** Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution existante en utilisant le modèle de projet **Application console (.NET Framework)** . Assurez-vous que la version du .NET Framework est définie sur 4.6.1 ou supérieur. Nommez le projet **UpdateModuleTwinReportedProperties**.

    ![Créer un projet Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Installez le dernier SDK d’appareil .NET Azure IoT Hub :** L’identité de module et le jumeau de module sont disponibles en préversion publique. Ils sont uniquement disponibles dans les kits SDK d’appareil IoT Hub en préversion. Dans Visual Studio, ouvrez Outils > Gestionnaire de package Nuget > Gérer les packages Nuget pour la solution. Recherchez Microsoft.Azure.Devices.Client. Assurez-vous de cocher la case Inclure la version préliminaire. Sélectionnez la version la plus récente et installez-la. Vous avez maintenant accès à toutes les fonctionnalités de module. 

    ![Installer le kit SDK du service NET Azure IoT Hub V1.16.0-préversion-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Obtenir la chaîne de connexion de votre module** : vous pouvez le faire dès maintenant si vous vous connectez au [portail Azure](https://portal.azure.com/). Accédez à votre IoT Hub, puis cliquez sur Appareils IoT. Recherchez et ouvrez myFirstDevice pour vérifier que myFirstModule a bien été créé. Copiez la chaîne de connexion du module. Vous en aurez besoin à l’étape suivante.

    ![Détails du module du Portail Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Créer l’application de console UpdateModuleTwinReportedProperties**

    Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion du module.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Ajoutez la méthode suivante **OnDesiredPropertyChanged** à la classe **Program** :

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

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
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Cet exemple de code montre comment récupérer le jumeau de module et mettre à jour les propriétés déclarées avec le protocole AMQP. Dans la préversion publique, AMQP est pris en charge uniquement pour les opérations de jumeau de module.

5. En plus de la méthode **Main** ci-dessus, vous pouvez ajouter le bloc de code suivant pour envoyer un événement à IoT Hub à partir de votre module :

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications. Dans Visual Studio, dans l’explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sur **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis choisissez l’action **Démarrer** pour l’application de console. Appuyez ensuite sur F5 pour démarrer l’application.

## <a name="next-steps"></a>Étapes suivantes

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Prise en main de la gestion d’appareils](iot-hub-node-node-device-management-get-started.md)
* [Bien démarrer avec IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
