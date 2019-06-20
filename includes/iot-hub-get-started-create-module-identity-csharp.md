---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177716"
---
## <a name="create-a-module-identity"></a>Création d’un module d’identité

Dans cette section, vous allez créer une application console .NET qui crée une identité d’appareil et une identité de module dans le registre d’identités de votre IoT Hub. Un appareil ou un module ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Pour plus d’informations, consultez la [section Registre des identités du Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). En exécutant cette application console, une clé et un ID uniques sont générés pour chaque appareil et module. Votre appareil et le module utilisent ces valeurs pour s’identifier lorsqu’ils envoient des messages d’appareil-à-cloud à IoT Hub. Les ID sont sensibles à la casse.


1. **Créer un projet Visual Studio** - Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à une nouvelle solution en utilisant le modèle de projet **Application console (.NET Framework)** . Assurez-vous que la version du .NET Framework est définie sur 4.6.1 ou supérieur. Nommez le projet **CreateIdentity** et nommez la solution **IoTHubGetStarted**.

    ![Créer une solution Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Installer le kit SDK du service NET Azure IoT Hub V1.16.0-préversion-001** - L’identité de module et le jumeau de module sont disponibles en préversion publique. Ils sont uniquement disponibles dans les SDK des services en préversion IoT Hub. Dans Visual Studio, ouvrez Outils > Gestionnaire de package Nuget > Gérer les packages Nuget pour la solution. Rechercher Microsoft.Azure.Devices. Assurez-vous de cocher la case Inclure la version préliminaire. Sélectionnez la version 1.16.0-préversion-001 et installez-la. Vous avez maintenant accès à toutes les fonctionnalités de module. 

    ![Installer le kit SDK du service NET Azure IoT Hub V1.16.0-préversion-001](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section précédente.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Ajoutez le code suivant à la classe **Main**.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Ajoutez les méthodes suivantes à la classe **Program** :

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    La méthode AddDeviceAsync() crée une identité d’appareil avec l’ID **myFirstDevice**. (si cet ID d’appareil existe déjà dans le registre d’identité, le code récupère simplement les informations d’appareil existantes.) L’application affiche ensuite la clé primaire pour cette identité. Vous utilisez cette clé dans l’application de périphérique simulé pour vous connecter à votre IoT Hub.

    La méthode AddModuleAsync() crée une identité de module avec l’ID **myFirstModule** sous l’appareil **myFirstDevice**. (si cet ID de module existe déjà dans le registre d’identité, le code récupère simplement les informations existantes du module.) L’application affiche ensuite la clé primaire pour cette identité. Vous utilisez cette clé dans l’application de module simulé pour vous connecter à votre IoT Hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Exécutez cette application et notez la clé de l’appareil et la clé du module.

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils et des modules pour permettre un accès sécurisé à IoT Hub. Le registre des identités stocke les ID et les clés d’appareil à utiliser en tant qu’informations d’identification de sécurité. Il stocke également un indicateur activé/désactivé pour chaque appareil pouvant être utilisé pour désactiver l’accès de cet appareil. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Il n’y a aucun indicateur d’activation/désactivation pour les identités de module. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
