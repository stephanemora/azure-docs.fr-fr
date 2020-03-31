---
title: Messages cloud-à-appareil avec Azure IoT Hub (.NET) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour .NET. Vous modifiez une application d’appareil pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110200"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envoyer des messages du cloud à votre appareil avec IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md) explique comment créer un hub IoT, y provisionner une identité d’appareil et coder une application d’appareil qui envoie des messages appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce tutoriel est basé sur [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md). Il vous montre comment effectuer les tâches suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

* À partir du back-end de votre solution, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécutez deux applications console .NET.

* **SimulatedDevice**. Cette application se connecte à votre hub IoT et reçoit des messages cloud-à-appareil. Cette application est une version modifiée de l’application créée dans le cadre du guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Cette application envoie un message cloud-à-appareil à l’application d’appareil par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de SDK pour de nombreux langages (notamment C, Java, Python et Javascript) et plateformes d’appareils par le biais des [Kits Azure IoT device SDK](iot-hub-devguide-sdks.md). Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel et à Azure IoT Hub de manière générale, consultez le [Guide du développeur IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Conditions préalables requises

* Visual Studio

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

* Assurez-vous que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Recevoir des messages dans l’application d’appareil

Dans cette section, vous allez modifier l’application d’appareil créée dans [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md) pour recevoir des messages cloud-à-appareil en provenance du hub IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice**, ajoutez la méthode suivante à la classe **Program**.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

   ```csharp
   ReceiveC2dAsync();
   ```

La méthode `ReceiveAsync` renvoie de façon asynchrone le message reçu au moment où l’appareil le reçoit. Elle retourne *null* après un délai d’expiration qui peut être spécifié. Dans cet exemple, le délai par défaut (une minute) est utilisé. Lorsque l’application reçoit une valeur *null*, elle doit continuer à attendre de nouveaux messages. C’est pour répondre à cette exigence que la ligne `if (receivedMessage == null) continue` est insérée.

L’appel à `CompleteAsync()` notifie IoT Hub que le message a été traité avec succès. Le message peut être supprimé en toute sécurité de la file d’attente d’appareils. Si l’application de périphérique n’a pas été en mesure de terminer le traitement du message, IoT Hub le remet à nouveau. La logique de traitement du message de l’application d’appareil doit être *idempotente* pour qu’un message identique reçu plusieurs fois produise le même résultat.

Une application peut également abandonner temporairement un message. IoT hub en conserve alors le message dans la file d’attente pour un traitement ultérieur. Ou l’application peut rejeter un message, ce qui le supprime définitivement de la file d'attente. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, consultez [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Quand vous utilisez HTTPS comme moyen de transport au lieu de MQTT ou AMQP, la méthode `ReceiveAsync` est immédiatement renvoyée. Le modèle de prise en charge des messages cloud-à-appareil avec HTTPS est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (moins de toutes les 25 minutes). L’émission d’un nombre plus élevé de réceptions HTTPS conduit IoT Hub à limiter les demandes. Pour plus d’informations sur les différences entre la prise en charge de MQTT, d’AMQP et de HTTPS ainsi que la limitation d’IoT Hub, voir le [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

Dans cet article, vous créez un service back-end pour envoyer des messages cloud-à-appareil par le biais du hub IoT que vous avez créé dans [Envoyer des données de télémétrie d’un appareil vers un hub IoT](quickstart-send-telemetry-dotnet.md). Pour envoyer des messages cloud-à-appareil, votre service a besoin de l'autorisation de **connexion de service**. Par défaut, chaque IoT Hub est créé avec une stratégie d’accès partagé nommée **service** qui accorde cette autorisation.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Vous écrivez maintenant une application console .NET qui envoie des messages cloud-à-appareil à l’application d’appareil.

1. Dans la solution Visual Studio actuelle, sélectionnez **Fichier** > **Nouveau** > **Projet**. Sous **Créer un projet**, sélectionnez **Application console (.NET Framework)** , puis cliquez sur **Suivant**.

1. Nommez le projet *SendCloudToDevice*. Sous **Solution**, sélectionnez **Ajouter à la solution** et acceptez la version la plus récente du .NET Framework. Sélectionnez **Créer** pour créer le projet.

   ![Configurer un nouveau projet dans Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la nouvelle solution, puis sélectionnez **Gérer les packages NuGet**.

1. Sous **Gérer les packages NuGet**, sélectionnez **Parcourir**, puis recherchez et sélectionnez **Microsoft.Azure.Devices**. Sélectionnez **Installer**.

   Cette opération a pour effet de télécharger, d’installer et d’ajouter une référence au [package NuGet de l’Azure IoT service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Ajoutez l’instruction `using` suivante en haut du fichier **Program.cs**.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion du hub IoT que vous avez copiée dans [Obtenir la chaîne de connexion du hub IoT](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Ajoutez la méthode suivante à la classe **Program**. Définissez le nom de l’appareil sur le nom utilisé lorsque vous avez défini l’appareil dans [Envoyer des données de télémétrie depuis un appareil vers un hub IoT](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Cette méthode envoie un nouveau message cloud-à-appareil à l’appareil avec l’ID `myFirstDevice`. Modifiez ce paramètre uniquement si vous avez modifié celui utilisé dans [Send telemetry from a device to an IoT hub](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie à partir d’un appareil à un hub IoT).

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Définir les projets de démarrage**.

1. Sous **Propriétés communes** > **Projet de démarrage**, sélectionnez **Plusieurs projets de démarrage**, puis l’action **Démarrer** pour **ReadDeviceToCloudMessages**, **SimulatedDevice** et **SendCloudToDevice**. Sélectionnez **OK** pour enregistrer vos modifications.

1. Appuyez sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application d’appareil.

   ![Application recevant le message](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Réception des commentaires de remise

Il est possible de demander des accusés de réception (ou d’expiration) à IoT Hub pour chaque message cloud-à-appareil. Cette option permet au serveur principal de solution d’informer facilement d’une nouvelle tentative ou d’une logique de compensation. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, consultez [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

Dans cette section, vous modifiez l’application **SendCloudToDevice** pour demander des commentaires et en recevoir du hub IoT.

1. Dans Visual Studio, dans le projet **SendCloudToDevice**, ajoutez la méthode suivante à la classe **Program**.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Notez que ce modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

1. Ajoutez la ligne suivante à la méthode **Main** juste après `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Pour obtenir des commentaires sur la remise de votre message cloud-à-appareil, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync** . Ajoutez la ligne suivante, immédiatement après la ligne `var commandMessage = new Message(...);`.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Exécutez les applications en appuyant sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application d’appareil et, après quelques secondes, le message de commentaires reçu par votre application **SendCloudToDevice**.

   ![Application recevant le message](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, un backoff exponentiel) comme indiqué dans [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Étapes suivantes

Dans cette procédure, vous avez appris comment envoyer et recevoir des messages de cloud-à-appareil.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de supervision à distance Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub](iot-hub-devguide.md).
