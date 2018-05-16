---
title: Prise en main de l’identité de module et du jumeau de module Azure IoT Hub (portail et .NET) | Microsoft Docs
description: Découvrez comment créer une identité de module et comment mettre à jour un jumeau de module à l’aide du portail et de .NET.
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99b0acbf6461750c2606cc3d4338c10b03a4b430
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Prise en main de l’identité de module et du jumeau de module IoT Hub à l’aide du portail et d’un appareil .NET

> [!NOTE]
> [Les identités de module et les jumeaux de module](iot-hub-devguide-module-twins.md) sont similaires aux identités d’appareil et aux jumeaux d’appareil Azure IoT Hub, mais offrent un plus grand niveau de détail. Contrairement à l’identité d’appareil et au jumeau d’appareil Azure IoT Hub qui permettent à l’application principale de configurer un appareil et d’obtenir une visibilité sur l’état de l’appareil, une identité de module et un jumeau de module fournissent ces fonctionnalités pour les composants individuels d’un appareil. Sur les appareils compatibles qui intègrent plusieurs composants, par exemple des appareils basés sur un système d’exploitation ou des appareils avec un microprogramme, ils permettent d’isoler la configuration et les conditions de chacun de ces composants.

Ce tutoriel vous apprendra à effectuer les opérations suivantes : 
1. créer une identité de module dans le portail ; 
2. utiliser le kit de développement logiciel (SDK) .NET pour mettre à jour le jumeau de module à partir de votre appareil.

> [!NOTE]
> Pour plus d’informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal, voir l’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit] [lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Créer une identité d’appareil sur le portail

Vous disposez maintenant de votre IoT Hub. Ouvrez le [portail](https://portal.azure.com) pour accéder à votre IoT Hub. Cliquez sur Appareils IoT, puis cliquez sur Ajouter pour créer une identité d’appareil. Nommez-la **MyFirstDevice**. 

![Création d’une identité d’appareil][8]

Une fois l’identité enregistrée, vous pouvez voir dans votre liste d’identités d’appareil que votre identité MyFirstDevice a bien été créée.

![Identité d’appareil créée][11]

Cliquez maintenant sur la ligne. Vous obtenez les détails de l’appareil.

![Détails de l’appareil][10]

## <a name="create-a-module-identity-in-the-portal"></a>Créer une identité de module dans le portail

Vous pouvez créer jusqu’à 20 identités de module dans une même identité d’appareil. Cliquez sur le bouton **Ajouter une identité de module** en haut de l’écran pour créer votre première identité de module, que vous appellerez **myFirstModule**. 

![Détails de l’appareil][9]

Enregistrez et cliquez sur l’identité de module que vous venez de créer. Vous pouvez visualiser les détails de l’identité de module. Enregistrez la clé primaire de la chaîne de connexion. Cette clé sera utilisée dans la section suivante, où vous allez configurer votre module sur l’appareil.

![Détails de l’appareil][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>Mettre à jour le jumeau de module à l’aide du SDK d’appareil .NET

Vous venez de créer votre identité de module dans votre IoT Hub. Vous allez maintenant tenter d’établir une communication entre votre appareil simulé et le cloud. Une fois que vous avez créé une identité de module, un jumeau de module est implicitement créé dans IoT Hub. Dans cette section, vous allez créer sur votre appareil simulé une application de console .NET qui met à jour les propriétés déclarées du jumeau de module.

1. **Créer un projet Visual Studio** : dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution existante en utilisant le modèle de projet **Application console (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur 4.6.1 ou supérieur. Nommez le projet **UpdateModuleTwinReportedProperties**.

    ![Créer un projet visual studio][13]

2. **Installer le SDK d’appareil .NET Azure IoT Hub V1.16.0, préversion-005** : l’identité de module et le jumeau de module sont disponibles en préversion publique. Ils sont uniquement disponibles dans les kits de développement logiciel d’appareil IoT Hub en préversion. Dans Visual Studio, ouvrez Outils > Gestionnaire de package Nuget > Gérer les packages Nuget pour la solution. Recherchez Microsoft.Azure.Devices.Client. Assurez-vous de cocher la case Inclure la version préliminaire. Sélectionnez la version V1.16.0-préversion-005 et installez-la. Vous avez maintenant accès à toutes les fonctionnalités de module. 

    ![Installer le kit SDK du service NET Azure IoT Hub V1.16.0-préversion-005][14]

3. **Obtenir la chaîne de connexion de votre module** : vous pouvez le faire dès maintenant si vous vous connectez au [portail Azure][lnk-portal]. Accédez à votre IoT Hub, puis cliquez sur Appareils IoT. Recherchez et ouvrez MyFirstDevice pour vérifier que myFirstModule a bien été créé. Copiez la chaîne de connexion du module. Vous en aurez besoin à l’étape suivante.

    ![Détails du module du portail Azure][15]

4. **Créer l’application de console UpdateModuleTwinReportedProperties** : ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion du module.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static DeviceClient Client = null;
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
            Client = DeviceClient.CreateFromConnectionString(ModuleConnectionString, transport);
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
        Client.CloseAsync().Wait();
    }
    ```

    Cet exemple de code montre comment récupérer le jumeau de module et mettre à jour les propriétés déclarées avec le protocole AMQP. Dans la préversion publique, AMQP est pris en charge uniquement pour les opérations de jumeau de module.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
