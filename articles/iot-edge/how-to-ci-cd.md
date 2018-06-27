---
title: Intégration continue et déploiement continu dans Azure IoT Edge | Microsoft Docs
description: Vue d’ensemble de l’intégration continue et du déploiement continu pour Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 04/30/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a8b58eae9aa08d8f6539370fa6e78a7a4813c18f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631018"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge---preview"></a>Intégration continue et déploiement continu dans Azure IoT Edge - préversion
Ce didacticiel montre comment utiliser les fonctionnalités d’intégration continue et de déploiement continu de Visual Studio Team Services (VSTS) et Microsoft Team Foundation Server (TFS) pour compiler, tester et déployer des applications rapidement et efficacement sur un appareil Azure IoT Edge. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer et archiver un exemple de solution IoT Edge contenant des tests unitaires
> * Installer l’extension Azure IoT Edge pour votre compte VSTS
> * Configurer l’intégration continue (CI) pour compiler la solution et exécuter les tests unitaires
> * Configurer le déploiement continu (CD) pour déployer la solution et afficher les réponses

Comptez 30 minutes pour effectuer ce didacticiel.

![CI et CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Créer un exemple de solution Azure IoT Edge à l’aide de Visual Studio Code

Dans cette section, vous allez créer un exemple de solution IoT Edge contenant des tests unitaires que vous pouvez exécuter dans le cadre du processus de compilation. Avant de suivre les instructions de cette section, effectuez les étapes décrites dans [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Dans la palette de commande VS Code, saisissez et exécutez la commande **Edge : nouvelle solution IoT Edge**. Ensuite, sélectionnez le dossier de votre espace de travail, fournissez le nom de la solution (le nom par défaut est **EdgeSolution**), et créez un module C# (**FilterModule**) en tant que premier module utilisateur dans cette solution. Vous devez également spécifier le référentiel d’image Docker pour votre premier module. Le référentiel d’image par défaut est basé sur un registre Docker local (`localhost:5000/filtermodule`). Vous devez le remplacer par un registre Azure Container Registry (`<your container registry address>/filtermodule`) ou un Hub Docker pour une intégration continue approfondie.

    ![Configuration d’un registre ACR](./media/how-to-ci-cd/acr.png)

2. La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. Vous pouvez éventuellement taper et exécuter **Edge: Add IoT Edge module** pour ajouter d’autres modules. Le dossier racine contient un dossier `modules`, un dossier `.vscode` et un fichier modèle du manifeste de déploiement. L’ensemble des codes du module utilisateur sont des sous-dossiers du dossier `modules`. L’élément `deployment.template.json` est le modèle du manifeste de déploiement. Certains des paramètres de ce fichier sont analysés à partir de l’élément `module.json`, qui existe dans chaque dossier de module.

3. Votre exemple de solution IoT Edge est maintenant prêt. Le module C# par défaut joue le rôle de module de canal de messages. Dans l’élément `deployment.template.json`, vous pouvez voir que cette solution comporte deux modules. Le message sera généré à partir du module `tempSensor`, acheminé directement via `FilterModule`, puis envoyé vers votre IoT hub. Remplacez l’intégralité du fichier **Program.cs** par le contenu ci-dessous. Pour plus d’informations sur cet extrait de code, vous pouvez consulter [Créer un projet de module C# IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                // The Edge runtime gives us the connection string we need -- it is injected as an environment variable
                string connectionString = Environment.GetEnvironmentVariable("EdgeHubConnectionString");

                // Cert verification is not yet fully functional when using Windows OS for the container
                bool bypassCertVerification = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
                if (!bypassCertVerification) InstallCert();
                Init(connectionString, bypassCertVerification).Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Add certificate in local cert store for use by client for secure connection to IoT Edge runtime
            /// </summary>
            static void InstallCert()
            {
                string certPath = Environment.GetEnvironmentVariable("EdgeModuleCACertificateFile");
                if (string.IsNullOrWhiteSpace(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing path to certificate file.");
                }
                else if (!File.Exists(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing certificate file.");
                }
                X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
                store.Open(OpenFlags.ReadWrite);
                store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
                Console.WriteLine("Added Cert: " + certPath);
                store.Close();
            }
            /// <summary>
            /// Initializes the DeviceClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init(string connectionString, bool bypassCertVerification = false)
            {
                Console.WriteLine("Connection String {0}", connectionString);

                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                // During dev you might want to bypass the cert verification. It is highly recommended to verify certs systematically in production
                if (bypassCertVerification)
                {
                    mqttSetting.RemoteCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true;
                }
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                DeviceClient ioTHubModuleClient = DeviceClient.CreateFromConnectionString(connectionString, settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

                // Read TemperatureThreshold from Module Twin Desired Properties
                var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
                var moduleTwinCollection = moduleTwin.Properties.Desired;
                try {
                    temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
                } catch(ArgumentOutOfRangeException) {
                    Console.WriteLine("Proerty TemperatureThreshold not exist");
                }

                // Attach callback for Twin desired properties updates
                await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
            }

            static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
            {
                try
                {
                    Console.WriteLine("Desired property change:");
                    Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                    if (desiredProperties["TemperatureThreshold"] != null)
                        temperatureThreshold = desiredProperties["TemperatureThreshold"];

                }
                catch (AggregateException ex)
                {
                    foreach (Exception exception in ex.InnerExceptions)
                    {
                        Console.WriteLine();
                        Console.WriteLine("Error when receiving desired property: {0}", exception);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
                }
                return Task.CompletedTask;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message {counterValue}: [{messageString}]");

                // Get message body
                var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                {
                    Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                        $"exceeds threshold {temperatureThreshold}");
                    var filteredMessage = new Message(messageBytes);
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    return filteredMessage;
                }
                return null;
            }

            static async Task<MessageResponse> FilterMessages(Message message, object userContext)
            {
                try
                {
                    DeviceClient deviceClient = (DeviceClient)userContext;

                    var filteredMessage = filter(message);
                    if (filteredMessage != null)
                    {
                        await deviceClient.SendEventAsync("output1", filteredMessage);
                    }

                    // Indicate that the message treatment is completed
                    return MessageResponse.Completed;
                }
                catch (AggregateException ex)
                {
                    foreach (Exception exception in ex.InnerExceptions)
                    {
                        Console.WriteLine();
                        Console.WriteLine("Error in sample: {0}", exception);
                    }
                    // Indicate that the message treatment is not completed
                    var deviceClient = (DeviceClient)userContext;
                    return MessageResponse.Abandoned;
                }
                catch (Exception ex)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", ex.Message);
                    // Indicate that the message treatment is not completed
                    DeviceClient deviceClient = (DeviceClient)userContext;
                    return MessageResponse.Abandoned;
                }
            }
        }
    }
    ```

4. Créez un projet de test unitaire .Net Core. Dans l’explorateur de fichiers de VS Code, créez un dossier **tests\FilterModuleTest** dans votre espace de travail. Ensuite, dans le terminal intégré de VS Code (**Ctrl + `**), exécutez les commandes suivantes pour créer un projet de test xunit et ajouter une référence au projet **FilterModule**.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Structure des dossiers](./media/how-to-ci-cd/add-test-project.png)

5. Dans le dossier **FilterModuleTest**, remplacez le nom de fichier de **UnitTest1.cs** par **FilterModuleTest.cs**. Sélectionnez et ouvrez **FilterModuleTest.cs**, remplacez l’intégralité du code par l’extrait de code ci-dessous, qui contient les tests unitaires pour le projet FilterModule.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. Dans le terminal intégré, vous pouvez entrer les commandes suivantes pour exécuter les tests unitaires localement. 
    ```cmd
    dotnet test
    ```

    ![Test unitaire](./media/how-to-ci-cd/unit-test.png)

7. Enregistrez ces projets, puis archivez-les dans votre référentiel VSTS ou TFS.
    

> [!NOTE]
> Pour plus d’informations sur l’utilisation de référentiels de code VSTS, consultez [Share your code with Visual Studio and VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts) (Partager votre code avec Visual Studio et VSTS Git).


## <a name="configure-continuous-integration"></a>Configurer l’intégration continue
Dans cette section, vous allez créer une définition de build configurée pour s’exécuter automatiquement quand vous archivez des modifications apportées à l’exemple de solution IoT Edge et qui exécutera automatiquement les tests unitaires qu’elle contient.

1. Connectez-vous à votre compte VSTS (**https://**_votre-compte_**.visualstudio.com**) et ouvrez le projet dans lequel vous avez activé l’exemple d’application.

    ![Code archivé](./media/how-to-ci-cd/init-project.png)

1. Rendez-vous sur la page [Azure IoT Edge For VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) (Azure IoT Edge pour VSTS) sur Visual Studio Marketplace. Cliquez sur **Get it free** (Obtenir gratuitement) et suivez les instructions de l’Assistant pour ajouter cette extension à votre compte VSTS ou la télécharger sur votre serveur TFS.

    ![Installer l’extension](./media/how-to-ci-cd/install-extension.png)

1. Dans votre compte VSTS, ouvrez le Hub **Générer et publier**, puis, dans l’onglet **Builds**, choisissez **+ Nouvelle définition**. Si vous disposez déjà de définitions de build, choisissez le bouton **+ Nouveau** à la place. 

    ![Nouvelle build](./media/how-to-ci-cd/add-new-build.png)

1. Si vous y êtes invité, sélectionnez le type de source **VSTS Git**, puis sélectionnez le projet, le référentiel et la branche où se trouve votre code. Choisissez **Continuer**.

    ![Sélectionner VSTS Git](./media/how-to-ci-cd/select-vsts-git.png)

1. Dans la fenêtre **Sélectionner un modèle**, cliquez sur **Processus vide**.

    ![Commencer avec un processus vide](./media/how-to-ci-cd/start-with-empty.png)

1. Cliquez sur **+** à droite de **Phase 1** pour ajouter une tâche à la phase. Ensuite, recherchez et sélectionnez **.Net Core**, puis cliquez sur **Ajouter** pour ajouter cette tâche à la phase.

    ![Test dotnet](./media/how-to-ci-cd/add-dot-net-core.png)

1. Définissez le **nom d’affichage** sur **dotnet test**, puis, dans la liste déroulante **Commande**, sélectionnez **test**. Ajoutez le chemin d’accès ci-dessous dans la zone de texte **Chemin des projets**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Configurer le test dotnet](./media/how-to-ci-cd/dotnet-test.png)

1. Cliquez sur **+** à droite de **Phase 1** pour ajouter une tâche à la phase. Ensuite, recherchez et sélectionnez **Azure IoT Edge**, puis cliquez sur le bouton **Ajouter** **deux fois** pour ajouter ces tâches à la phase.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Dans la première tâche Azure IoT Edge, définissez le **nom d’affichage** sur **Module Build and Push**, puis, dans la liste déroulante **Action**, sélectionnez **Build and Push modules** (Générer et envoyer (push) les modules). Dans la zone de texte **Module.json File** (Fichier Module.json), ajoutez le chemin d’accès ci-dessous. Ensuite, choisissez **Type de registre de conteneurs**, puis configurez et sélectionnez le même registre que dans votre code. Cette tâche générera et enverra (push) tous vos modules dans la solution, et effectuera la publication dans le registre de conteneurs que vous avez spécifié. 

    ```
    **/module.json
    ```

    ![Génération et envoi (push) des modules](./media/how-to-ci-cd/module-build-push.png)

1. Dans la deuxième tâche Azure IoT Edge, définissez le **nom d’affichage** sur **Deploy to IoT Edge device**, puis, dans la liste déroulante **Action**, sélectionnez **Deploy to IoT Edge device** (Déployer sur l’appareil IoT Edge). Sélectionnez votre abonnement Azure et saisissez le nom de votre IoT Hub. Vous pouvez spécifier un ID de déploiement IoT Edge et la priorité du déploiement. Vous pouvez également choisir d’effectuer le déploiement sur un ou plusieurs appareils. Si vous effectuez le déploiement sur plusieurs appareils, vous devez spécifier la condition cible des appareils. Par exemple, pour utiliser les balises des appareils comme condition, vous devez mettre à jour vos balises d’appareils correspondantes avant le déploiement. 

    ![Déployer sur l’appareil Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. Cliquez sur le bouton **Processus** et définissez **File d’attente d’agents** sur **Aperçu du système Linux hébergé**.

    ![Configuration](./media/how-to-ci-cd/configure-env.png)

1. Ouvrez l’onglet **Déclencheurs** et activez le déclencheur **Intégration continue**. Assurez-vous que la branche qui contient votre code est incluse.

    ![Déclencheur](./media/how-to-ci-cd/configure-trigger.png)

1. Enregistrez la nouvelle définition de build et mettez une nouvelle build en file d’attente. Cliquez sur le bouton **Enregistrer et mettre en file d’attente**.

1. Cliquez sur le lien vers la build dans la barre de message qui s’affiche. Vous pouvez également accéder à la définition de build pour afficher la dernière tâche de build mise en file d’attente.

    ![Créer](./media/how-to-ci-cd/build-def.png)

1. Une fois la build terminée, vous pouvez consulter le résumé de chaque tâche et les résultats dans le fichier journal dynamique. 
    
    ![Complete](./media/how-to-ci-cd/complete.png)

1. Vous pouvez revenir dans VS Code et consulter l’explorateur d’appareils IoT Hub. L’exécution de l’appareil Edge contenant le module devrait commencer (assurez-vous que vous avez ajouté les informations d’identification du registre au runtime Edge).

    ![Appareil Edge en cours d’exécution](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Déploiement continu sur des appareils IoT Edge

Pour activer le déploiement continu, vous devez configurer des tâches d’intégration continue avec les appareils IoT Edge appropriés, en activant les **déclencheurs** pour vos branches dans votre projet. Dans une pratique DevOps classique, un projet contient deux branches principales. La branche maîtresse doit être la version stable du code, tandis que la branche de développement contient les dernières modifications de code. Chaque développeur de l’équipe doit dupliquer (fork) la branche de développement dans sa propre branche de fonctionnalités quand il commence à mettre à jour le code. Par conséquent, toutes les validations ont lieu sur les branches de fonctionnalités, en dehors de la branche de développement. Par ailleurs, chaque validation envoyée (push) doit être testée via le système d’intégration continue. Une fois le code entièrement testé localement, la branche de fonctionnalités doit être fusionnée avec la branche de développement via une demande de tirage (pull request). Quand le code de la branche de développement est testé via le système d’intégration continue, cette branche peut être fusionnée avec la branche principale via une demande de tirage (pull request).

Par conséquent, le déploiement sur des appareils IoT Edge implique trois environnements principaux.
- Sur la branche de fonctionnalités, vous pouvez utiliser un appareil IoT Edge simulé sur votre ordinateur de développement ou effectuer le déploiement sur un appareil IoT Edge physique.
- Sur la branche de développement, vous devez effectuer le déploiement sur un appareil IoT Edge physique.
- Sur la branche principale, les appareils IoT Edge cibles doivent être les appareils de production.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel montre comment utiliser les fonctionnalités d’intégration continue et de déploiement continu de VSTS ou TFS. 

* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller des modules IoT Edge à l’échelle](how-to-deploy-monitor.md).









