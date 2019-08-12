---
title: Planifier des travaux avec Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Procédure de planification d’une tâche Azure IoT Hub pour appeler une méthode directe sur plusieurs appareils. Vous utilisez le Kit de développement logiciel Azure IoT device SDK pour .NET afin d’implémenter les applications d’appareil simulé et une application de service pour exécuter le travail.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: robinsh
ms.openlocfilehash: c4f2994413fca07f4a168cf12ba7967b00b6b0e2
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668102"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Planifier et diffuser des travaux (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilisez Azure IoT Hub pour planifier et suivre des travaux qui mettent à jour des millions d’appareils. Utilisez les travaux pour :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

Un travail encapsule l’une de ces actions et suit l’exécution sur un ensemble d’appareils, défini par une requête de jumeau d’appareil. Par exemple, une application principale peut utiliser un travail pour appeler une méthode directe sur 10 000 appareils, qui les redémarre. Vous spécifiez l’ensemble des appareils avec une requête de jumeau d’appareil et planifiez le travail à exécuter ultérieurement. Le travail suit la progression à mesure que chacun de ces appareils reçoit et exécute la méthode directe de redémarrage.

Pour plus d’informations sur chacune de ces fonctionnalités, consultez les pages :

* Jumeau d’appareil et propriétés : [Prise en main des jumeaux d’appareils](iot-hub-csharp-csharp-twin-getstarted.md) et [Didacticiel : Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)

* Méthodes directes : [Guide du développeur IoT Hub - méthodes directes](iot-hub-devguide-direct-methods.md) et [Didacticiel : Utiliser des méthodes directes](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel vous explique les procédures suivantes :

* Création d’une application pour appareils implémentant une méthode directe nommée **LockDoor**, qui peut être appelée par l’application back-end.

* Création d’une application back-end qui crée un travail pour appeler la méthode directe **LockDoor** sur plusieurs appareils. Un autre travail envoie les mises à jour de propriétés souhaitées à plusieurs appareils.

À la fin de ce didacticiel, vous disposerez de deux applications console .NET (C#) :

**SimulateDeviceMethods**, qui se connecte à votre Hub IoT et implémente la méthode directe **LockDoor**.

**ScheduleJob**, qui utilise des travaux pour appeler la méthode directe **LockDoor** et mettre à jour les propriétés souhaitées du jumeau d’appareil sur plusieurs appareils.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio.
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application console .NET qui répond à une méthode directe appelée par le serveur principal de la solution.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SimulateDeviceMethods**.
   
    ![Nouvelle application pour appareil Windows classique Visual C#](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulateDeviceMethods**, puis cliquez sur **Gérer les packages NuGet...** .

3. Dans la fenêtre **Gestionnaire de package NuGet**, sélectionnez **Parcourir**, puis recherchez **Microsoft.Azure.Devices.Client**. Sélectionnez **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence au package NuGet [Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) et ses dépendances.
   
    ![Application cliente dans la fenêtre du gestionnaire de package NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion de l’appareil que vous avez notée dans la section précédente :

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Ajoutez le code suivant pour implémenter la méthode directe sur l’appareil :

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

7. Ajoutez le code suivant pour implémenter l’écouteur des jumeaux d’appareils sur l’appareil :

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Enfin, ajoutez le code suivant à la méthode **Main** pour ouvrir la connexion à votre IoT Hub et initialiser l’écouteur de la méthode :
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
9. Enregistrez votre travail et générez votre solution.         

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une nouvelle tentative de connexion), comme suggéré dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).
> 

## <a name="get-the-iot-hub-connection-string"></a>Obtention de la chaîne de connexion de l’IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planifier des travaux pour appeler une méthode directe et envoyer des mises à jour de jumeaux d’appareils

Dans cette section, vous allez créer une application console .NET (en C#) qui utilise des travaux pour appeler la méthode directe **LockDoor** et envoyer les mises à jour des propriétés souhaitées à plusieurs appareils.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **ScheduleJob**.

    ![Nouveau projet Visual C# Bureau classique Windows](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ScheduleJob**, puis cliquez sur **Gérer les packages NuGet...** .

3. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **Microsoft.Azure.Devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette étape lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/) et ses dépendances.

    ![Fenêtre du gestionnaire de package NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Ajoutez l’instruction `using` suivante si elle ne figure pas déjà dans les instructions par défaut.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Ajoutez les champs suivants à la classe **Program** . Remplacez les espaces réservés par la chaîne de connexion de l’IoT Hub que vous avez précédemment copiée dans [Obtention de la chaîne de connexion de l’IoT Hub](#get-the-iot-hub-connection-string), puis nommez votre appareil.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Ajoutez une autre méthode à la classe **Program** :

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Pour plus d’informations sur la syntaxe de requête, consultez l’article [Langage de requête IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

11. Enregistrez votre travail et générez votre solution. 

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur votre solution, puis cliquez sur **Générer**, **Plusieurs projets de démarrage**. Assurez-vous que `SimulateDeviceMethods` figure en haut de la liste, suivi de `ScheduleJob`. Définissez leurs deux actions sur **Démarrer**, puis cliquez sur **OK**.

2. Exécutez les projets en cliquant sur **Démarrer** ou accédez au menu **Déboguer** et cliquez sur **Démarrer le débogage**.

3. La sortie de l’appareil et des applications principales s’affiche.

    ![Exécuter les applications pour planifier des tâches](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé un travail pour planifier une méthode directe sur un appareil et la mise à jour des propriétés de représentation de l’appareil.

Pour approfondir la prise en main d’IoT Hub et des modèles de gestion d’appareils, comme la mise à jour du microprogramme à distance, consultez [Didacticiel : Mettre à jour un microprogramme](tutorial-firmware-update.md).

Pour en savoir plus sur le déploiement de l’intelligence artificielle sur des appareils de périphérie avec Azure IoT Edge, consultez [Bien démarrer avec IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).