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
ms.openlocfilehash: d16f57db6a3c39be34c13663db62d7be50749f57
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786691"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envoyer des messages du cloud à votre appareil avec IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Présentation

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT) explique comment créer un IoT Hub, y approvisionner une identité d’appareil et coder une application d’appareil qui envoie des messages d’appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce tutoriel est basé sur le démarrage rapide [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT). Il vous montre comment effectuer les étapes suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécutez deux applications console .NET.

* **SimulatedDevice**, une version modifiée de l’application créée dans [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT), qui se connecte à votre hub IoT et reçoit des messages cloud-à-appareil.

* **SendCloudToDevice**, qui envoie un message cloud-à-appareil à l’application d’appareil par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de Kits de développement logiciel (SDK) pour plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des [Kits Azure IoT device SDK](iot-hub-devguide-sdks.md). Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel et à Azure IoT Hub de manière générale, consultez le [Guide du développeur IoT Hub](iot-hub-devguide.md).
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2017

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

## <a name="receive-messages-in-the-device-app"></a>Recevoir des messages dans l’application d’appareil

Dans cette section, vous allez modifier l’application d’appareil créée dans [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT) pour recevoir des messages cloud-à-appareil en provenance du hub IoT.

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

   La méthode `ReceiveAsync` renvoie de façon asynchrone le message reçu au moment où l’appareil le reçoit. Elle renvoie *null* après un délai d’attente pouvant être précisé (dans ce cas, la valeur par défaut est une minute). Lorsque l’application reçoit une valeur *null*, elle doit continuer à attendre de nouveaux messages. C’est pour répondre à cette exigence que la ligne `if (receivedMessage == null) continue` est insérée.

    L’appel à `CompleteAsync()` notifie IoT Hub que le message a été traité avec succès. Le message peut être supprimé en toute sécurité de la file d’attente d’appareils. Si l’application de périphérique n’a pas été en mesure de terminer le traitement du message, IoT Hub le remet à nouveau. Il est alors important que la logique de traitement du message de l’application d’appareil soit *idempotente*, afin qu’un message identique reçu plusieurs fois produise le même résultat. 

    Une application peut également abandonner temporairement un message. IoT hub en conserve alors le message dans la file d’attente pour un traitement ultérieur. Ou l’application peut rejeter un message, ce qui le supprime définitivement de la file d'attente. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, consultez [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Quand vous utilisez HTTPS comme moyen de transport au lieu de MQTT ou AMQP, la méthode `ReceiveAsync` est immédiatement renvoyée. Le modèle de prise en charge des messages cloud-à-appareil avec HTTPS est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (moins de toutes les 25 minutes). L’émission d’un nombre plus élevé de réceptions HTTPS conduit IoT Hub à limiter les demandes. Pour plus d’informations sur les différences entre la prise en charge de MQTT, d’AMQP et de HTTPS ainsi que la limitation d’IoT Hub, voir le [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).
   >

2. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion IoT Hub

Récupérez tout d’abord la chaîne de connexion IoT Hub à partir du portail.

1. Se connecter à la [Azure portal](https://portal.azure.com), sélectionnez **groupes de ressources**.

2. Sélectionnez le groupe de ressources que vous utilisez pour cette procédure.

3. Sélectionnez le IoT Hub que vous utilisez.

4. Dans le volet du Hub, sélectionnez **stratégies d’accès partagé**.

5. sélectionnez **iothubowner**. Il montre les chaînes de connexion sur le **iothubowner** Panneau de configuration. Sélectionnez l’icône de copie pour le **chaîne de connexion--clé primaire**. Enregistrez la chaîne de connexion en vue d’une utilisation ultérieure.

   ![Obtenir la chaîne de connexion IoT Hub](./media/iot-hub-csharp-csharp-c2d/get-iot-hub-connection-string.png)

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Maintenant, vous écrivez une application console .NET qui envoie des messages cloud-à-appareil à l’application d’appareil.

1. Dans la solution actuelle de Visual Studio, avec le bouton droit sur la solution et sélectionnez Ajouter > Nouveau projet. Sélectionnez **Windows Desktop** , puis **application Console (.NET Framework)**. Nommez le projet **SendCloudToDevice** et sélectionnez la version la plus récente du .NET Framework, puis **OK** pour créer le projet.

   ![Nouveau projet dans Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**.

   La fenêtre **Gérer les packages NuGet** s’ouvre.

3. Recherchez **Microsoft.Azure.Devices**, sélectionnez l’onglet Parcourir. Lorsque vous trouvez le package, cliquez sur **installer**, puis acceptez les conditions d’utilisation.

   Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure IoT - Service SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Ajoutez le code suivant `using` instruction en haut de la **Program.cs** fichier.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé avec la chaîne de connexion IoT hub, que vous avez enregistré précédemment dans cette section. 

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Ajoutez la méthode suivante à la classe **Program**. La valeur est le nom de l’appareil que vous avez utilisé lors de la définition de l’appareil dans [envoyer la télémétrie à partir d’un appareil à un hub IoT... ](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myDevice", commandMessage);
   }
   ```

   Cette méthode envoie un nouveau message cloud-à-appareil à l’appareil avec l’ID `myFirstDevice`. Modifiez ce paramètre uniquement si vous avez modifié celui utilisé dans [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie à partir d’un appareil à un hub IoT).

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage...**. Sélectionnez **Plusieurs projets de démarrage**, puis l’action **Démarrer** pour les applications **ReadDeviceToCloudMessages**, **SimulatedDevice** et **SendCloudToDevice**.

9. Appuyez sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application d’appareil.

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

2. Ajoutez la méthode suivante dans le **Main** (méthode), juste après le `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` ligne.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Pour obtenir des commentaires sur la remise de votre message cloud-à-appareil, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync** . Ajoutez la ligne suivante, juste après le `var commandMessage = new Message(...);` ligne.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Exécutez les applications en appuyant sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application d’appareil et, après quelques secondes, le message de commentaires reçu par votre application **SendCloudToDevice**.

   ![Application recevant le message](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Étapes suivantes

Dans cette procédure, vous avez appris comment envoyer et recevoir des messages de cloud-à-appareil.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de supervision à distance Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub](iot-hub-devguide.md).