---
title: Connecter un appareil Raspberry Pi à votre application Azure IoT Central (C#) | Microsoft Docs
description: En tant que périphérique développeur, comment connecter un Raspberry Pi à votre application Azure IoT Central en utilisant C#.
author: viv-liu
ms.author: viviali
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8a7b2d15884f7a1cb8b6e72937b0f1e3b6cb8ca9
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616014"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Connecter un appareil Raspberry Pi à votre application Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Raspberry Pi à votre application Microsoft Azure IoT Central en utilisant le langage de programmation C#.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre les étapes décrites dans cet article, vous devez disposer de ce qui suit :

* Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
* Un appareil Raspberry Pi exécutant le système d’exploitation Raspbian. Le Raspberry Pi doit être en mesure de se connecter à internet. Pour plus d’informations, consultez [configurer votre appareil Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exemple d’application Devkits**

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

- Les données de télémétrie, qui comprennent les mesures suivantes recueillies par l’appareil :
  - Humidité
  - Température
  - Pression
  - Magnétomètre (X, Y, Z)
  - Accéléromètre (X, Y, Z)
  - Gyroscope (X, Y, Z)
- Paramètres
  - Voltage
  - Current
  - Vitesse du ventilateur
  - Bascule IR.
- properties
  - Propriété d’appareil avec numéro gravé
  - Propriété d’emplacement cloud

Pour plus d’informations de la configuration du modèle d’appareil, consultez le [détails du modèle Raspberry Pi appareil](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir de la **Raspberry Pi** modèle de périphérique. Prenez note de l’appareil détails de connexion (**ID de portée**, **ID d’appareil**, et **clé primaire**). Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

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
   dotnet restore
   ```

1. Ouvrez le dossier `pisample` dans Visual Studio Code. Ouvrez ensuite le fichier projet **pisample.csproj**. Ajoutez la balise `<RuntimeIdentifiers>` montrée dans l’extrait de code suivant :

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Le numéro de version de votre package **Microsoft.Azure.Devices.Client** peut être supérieur à celui indiqué.

1. Enregistrez **pisample.csproj**. Si Visual Studio Code vous invite à exécuter la commande de restauration, choisissez **Restaurer**.

1. Ouvrez **Program.cs** et remplacez le contenu par le code suivant :

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
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
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
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

    > [!NOTE]
    > Vous remplacez l’espace réservé `{your device connection string}` à l’étape suivante.

## <a name="run-your-net-application"></a>Exécuter votre application .NET

Ajoutez la chaîne de connexion spécifique à votre appareil au code pour que l’appareil puisse s’authentifier auprès d’Azure IoT Central. Suivez ces instructions pour [générer la chaîne de connexion d’appareil](howto-generate-connection-string.md) à l’aide de la **ID de portée**, **ID d’appareil**, et **clé primaire** apportées un Notez de précédemment.

1. Remplacez `{your device connection string}` dans le **Program.cs** fichier avec la chaîne de connexion que vous avez généré.

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

## <a name="raspberry-pi-device-template-details"></a>Détails du modèle Raspberry Pi appareil

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Raspberry Pi** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimale | Maximale | Nombre de décimales |
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

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimale | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltage      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |

Paramètres de bascule

| Nom complet | Nom du champ | Texte pour Activé | Texte pour Désactivé | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ACTIVÉ      | ÉTEINT      | Off     |

### <a name="properties"></a>properties

| Type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Texte            | Lieu     | location   | S.O.       |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un Raspberry Pi à votre application Azure IoT Central, l’étape suivante suggérée consiste à apprendre comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
