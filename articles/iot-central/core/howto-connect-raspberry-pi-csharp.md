---
title: Connecter un appareil Raspberry Pi à votre application Azure IoT Central (C#) | Microsoft Docs
description: Découvrez en tant que développeur d’appareils comment connecter un appareil Raspberry Pi à votre application Azure IoT Central en utilisant C#.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d27f792b39a1809cde0f27186f343af7d7aef60a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454158"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Connecter un appareil Raspberry Pi à votre application Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Raspberry Pi à votre application Microsoft Azure IoT Central en utilisant le langage de programmation C#.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre les étapes décrites dans cet article, vous devez disposer de ce qui suit :

* Une application Azure IoT Central créée à partir du modèle d’application **Application héritée**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Un appareil Raspberry Pi exécutant le système d’exploitation Raspbian. L’appareil Raspberry Pi doit être en mesure de se connecter à Internet. Pour plus d’informations, consultez [Configurer votre appareil Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="add-a-device-template"></a>Ajouter un modèle d’appareil

Dans votre application Azure IoT Central, ajoutez un nouveau modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

* Les données de télémétrie, qui comprennent les mesures suivantes recueillies par l’appareil :
  * Humidité
  * Température
  * Pression
  * Magnétomètre (X, Y, Z)
  * Accéléromètre (X, Y, Z)
  * Gyroscope (X, Y, Z)
* Paramètres
  * Voltage
  * Current
  * Vitesse du ventilateur
  * Bascule IR.
* Propriétés
  * Propriété d’appareil avec numéro gravé
  * Propriété d’emplacement cloud

1. Sélectionnez **+ Nouveau** à partir du ![Modèle d’appareil](media/howto-connect-raspberry-pi-csharp/adddevicetemplate.png) des modèles d’appareil
   

2. Sélectionnez **Raspberry Pi** et créez le modèle d’appareil Raspberry Pi ![Ajouter un modèle d’appareil](media/howto-connect-raspberry-pi-csharp/newdevicetemplate.png)

Pour plus d’informations sur la configuration du modèle d’appareil, consultez [Détails de modèle d’appareil Raspberry Pi](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Raspberry Pi**. Notez les détails de connexion de l’appareil : **ID de l’étendue**, **ID de l’appareil** et **Clé primaire**. Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Créer votre application .NET

Vous créez et vous testez l’application de l’appareil sur votre poste de travail.

Pour effectuer les étapes suivantes, vous pouvez utiliser Visual Studio Code. Pour plus d’informations, consultez [Utilisation de C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Si vous préférez, vous pouvez effectuer les étapes suivantes en utilisant un autre éditeur de code.

1. Pour initialiser votre projet .NET et ajouter les packages NuGet nécessaires, exécutez les commandes suivantes :

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Ouvrez le dossier `pisample` dans Visual Studio Code. Ouvrez ensuite le fichier projet **pisample.csproj**. Ajoutez la balise `<RuntimeIdentifiers>` montrée dans l’extrait de code suivant :

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Les numéros de version de votre package peuvent être supérieurs à ceux indiqués.

1. Enregistrez **pisample.csproj**. Si Visual Studio Code vous invite à exécuter la commande de restauration, choisissez **Restaurer**.

1. Ouvrez **Program.cs** et remplacez le contenu par le code suivant. Mettez à jour `{your Scope ID}`, `{your Device ID}` et `{your Device Primary Key}` avec les valeurs que vous avez notées précédemment :

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
        {
          Random rand = new Random();

          while (true)
          {
            double currentTemperature = baseTemperature + rand.NextDouble() * 20;
            double currentPressure = basePressure + rand.NextDouble() * 100;
            double currentHumidity = baseHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
              humidity = currentHumidity,
              pressure = currentPressure,
              temp = currentTemperature
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));

            token.ThrowIfCancellationRequested();
            await Client.SendEventAsync(message);

            Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
          }
        }


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

## <a name="run-your-net-application"></a>Exécuter votre application .NET

Pour générer et exécuter l’exemple d’application :

1. Exécutez la commande suivante dans votre environnement de ligne de commande :

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Copiez le dossier `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` sur votre appareil Raspberry Pi. Vous pouvez utiliser la commande **scp** pour copier les fichiers, par exemple :

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Pour plus d’informations, consultez [Accès à distance de Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/).

1. Connectez-vous à votre appareil Raspberry Pi et exécutez les commandes suivantes dans un shell :

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Sur votre appareil Raspberry Pi, exécutez la commande suivante :

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Le programme démarre](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Dans votre application Azure IoT Central, vous pouvez voir comment le code s’exécutant sur l’appareil Raspberry Pi interagit avec l’application :

   * Dans la page **Mesures** de votre appareil réel, vous pouvez voir la télémétrie.
   * Dans la page **Propriétés**, vous pouvez voir la valeur de la propriété **Numéro gravé**.
   * Dans la page **Paramètres**, vous pouvez changer différents paramètres de l’appareil Raspberry Pi, comme le voltage et la vitesse du ventilateur.

     La capture d’écran suivante montre l’appareil Raspberry Pi recevant la modification du paramètre :

     ![L’appareil Raspberry Pi reçoit la modification du paramètre](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Détails de modèle d’appareil Raspberry Pi

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimum | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pression       | hPa    | 260     | 1 260    | 0              |
| magnetometerX  | mgauss | -1 000   | 1 000    | 0              |
| magnetometerY  | mgauss | -1 000   | 1 000    | 0              |
| magnetometerZ  | mgauss | -1 000   | 1 000    | 0              |
| accelerometerX | mg     | -2 000   | 2000    | 0              |
| accelerometerY | mg     | -2 000   | 2000    | 0              |
| accelerometerZ | mg     | -2 000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2 000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2 000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2 000   | 2000    | 0              |

### <a name="settings"></a>Paramètres

Paramètres numériques

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimum | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |

Paramètres de bascule

| Nom complet | Nom du champ | Texte pour Activé | Texte pour Désactivé | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVÉ      | OFF      | Off     |

### <a name="properties"></a>Propriétés

| Type            | Nom complet | Nom du champ | Type de données                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | nombre                                 |
| Location        | Location     | location   | {lat: float, long: float, alt?: float} |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil Raspberry Pi à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
