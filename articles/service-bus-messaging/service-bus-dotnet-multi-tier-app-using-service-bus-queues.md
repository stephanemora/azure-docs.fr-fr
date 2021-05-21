---
title: Application multiniveau .NET utilisant Azure Service Bus | Microsoft Docs
description: Un didacticiel .NET qui vous permet de développer dans Azure une application multiniveau qui utilise les files d’attente de Service Bus pour communiquer entre les différents niveaux.
ms.devlang: dotnet
ms.topic: article
ms.date: 04/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 38a1c975df578b32ec2d6cac9ff5c6ad4acb3687
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759181"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Application multiniveau .NET avec les files d’attente Azure Service Bus

Le développement pour Microsoft Azure est simple grâce à Visual Studio et au Kit de développement logiciel (SDK) Azure gratuit pour .NET. Ce didacticiel vous guide lors de la création d’une application qui utilise plusieurs ressources Azure s’exécutant dans votre environnement local.

Vous allez apprendre les opérations suivantes :

* configuration de votre ordinateur pour le développement Azure avec un seul téléchargement et une seule installation ;
* utilisation de Visual Studio pour développer pour Azure ;
* création d'une application multiniveau dans Azure avec les rôles Web et les rôles de travail ;
* Communication entre niveaux à l’aide des files d’attente Service Bus.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

Dans ce tutoriel, vous allez générer et exécuter l’application multiniveau dans un service cloud Azure. Le composant frontal est un rôle web ASP.NET MVC et le composant principal est un rôle de travail qui utilise une file d’attente Service Bus. Vous pouvez créer la même application multiniveau avec le composant frontal comme projet web déployé sur un site web Azure au lieu d’un service cloud. Vous pouvez également essayer le didacticiel [Application hybride .NET locale/dans le cloud](../azure-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

La capture d’écran suivante présente l’application terminée.

:::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png" alt-text="Page Envoyer de l’application":::

## <a name="scenario-overview-inter-role-communication"></a>Vue d’ensemble du scénario : communication entre les rôles
Pour envoyer une commande en traitement, le composant frontal d’interface utilisateur qui s’exécute dans le rôle web doit communiquer avec la logique centrale qui s’exécute dans le rôle de travail. Cet exemple utilise la messagerie Service Bus pour la communication entre les niveaux.

L’utilisation de la messagerie Service Bus entre les niveaux Web et central découple les deux composants. À l’inverse de la messagerie directe (TCP ou HTTP), le niveau web ne se connecte pas directement au niveau central. En effet, il envoie des unités de travail, sous forme de messages, à Service Bus, qui les retient jusqu’à ce que le niveau central soit prêt à les utiliser et à les traiter.

Service Bus fournit deux entités pour prendre en charge la messagerie répartie : les files d’attente et les rubriques. Avec les files d'attente, chaque message envoyé à la file d'attente est utilisé par un seul destinataire. Les rubriques prennent en charge le modèle de publication/d’abonnement, dans lequel chaque message publié est mis à la disposition d’un abonnement inscrit dans la rubrique. Chaque abonnement gère de façon logique sa propre file d’attente de messages. Les abonnements peuvent également être configurés avec des règles de filtrage, qui ne transmettent à la file d’attente de l’abonnement que les messages correspondant aux critères du filtre. L’exemple suivant utilise les files d’attente Service Bus.

:::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png" alt-text="Diagramme montrant la communication entre le rôle Web, Service Bus et le rôle de travail.":::

Ce mécanisme de communication présente plusieurs avantages par rapport à la messagerie directe :

* **Découplage temporel.** Avec le modèle de messagerie asynchrone, les producteurs et les consommateurs n'ont pas besoin d'être en ligne en même temps. Service Bus stocke de manière fiable les messages jusqu'à ce que le destinataire soit prêt à les recevoir. Ceci permet aux composants de l’application distribuée d’être déconnectés, soit volontairement, par exemple pour des raisons de maintenance, soit en raison de l’échec d’un composant, sans que le système dans sa globalité soit affecté. De plus, l’application qui utilise les messages peut n’avoir besoin d’être en ligne que quelques fois par jour.
* **Nivellement de charge.** Dans de nombreuses applications, la charge système varie dans le temps, alors que le temps de traitement nécessaire à chaque élément de travail est normalement constant. L'ajout d'une file d'attente entre les producteurs et les consommateurs des messages fait que l'application de destination (le rôle de travail) n'a besoin d'être configurée que pour une charge de travail moyenne, plutôt que pour une charge de travail maximale. La file d’attente s’allonge et se raccourcit en fonction de la charge entrante. Ceci permet de faire des économies en termes d'infrastructures nécessaires pour faire face à la charge de travail de l'application.
* **Équilibrage de la charge.** À mesure que la charge augmente, d'autres processus de travail peuvent être ajoutés pour lire les éléments de la file d'attente. Chaque message est traité par un seul des processus de travail. De plus, cet équilibrage de la charge basé sur l’extraction permet une utilisation optimale des ordinateurs de travail, même si ceux-ci diffèrent en termes de puissance de traitement, car ils extraient les messages au maximum de leur capacité. Ce modèle est souvent appelé modèle *consommateur concurrent*.
  
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png" alt-text="Diagramme montrant la communication entre le rôle Web, Service Bus et les deux rôles de travail.":::
  
Les sections qui suivent présentent le code de mise en œuvre de cette architecture.

## <a name="prerequisites"></a>Prérequis
Dans ce tutoriel, vous allez utiliser l’authentification Azure Active Directory (Azure AD) pour créer des objets `ServiceBusClient` et `ServiceBusAdministrationClient`. Vous utiliserez également `DefaultAzureCredential`, et pour l’utiliser, vous devez effectuer les étapes suivantes pour tester l’application localement dans un environnement de développement.

1. [Inscrire une application dans Azure AD](../active-directory/develop/quickstart-register-app.md).
1. [Ajoutez l’application au rôle `Service Bus Data Owner`](service-bus-managed-service-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. Définissez les variables d’environnement `AZURE-CLIENT-ID`, `AZURE-TENANT-ID` ET `AZURE-CLIENT-SECRET`. Pour obtenir des instructions, consultez [cet article](/dotnet/api/overview/azure/identity-readme#environment-variables).


## <a name="create-a-namespace"></a>Créer un espace de noms

La première étape consiste à créer un *espace de noms* et à obtenir une [clé de signature d’accès partagé (SAP)](service-bus-sas.md) pour ce dernier. Un espace de noms fournit une limite d’application pour chaque application exposée via Service Bus. Le système génère automatiquement une clé SAP lors de la création d’un espace de noms. La combinaison du nom de l’espace de noms et de la clé SAP fournit à Service Bus des informations d’identification permettant d’authentifier l’accès à une application.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Création d'un rôle web

Dans cette section, vous générez le composant frontal de votre application. Tout d’abord, vous créez les pages affichées par votre application.
Ensuite, ajoutez le code permettant d’envoyer les éléments à une file d’attente Service Bus et d’afficher les informations d’état de la file d’attente.

### <a name="create-the-project"></a>Créer le projet

1. Lancez Visual Studio avec des privilèges d’administrateur : cliquez avec le bouton droit sur l’icône du programme **Visual Studio**, puis cliquez sur **Exécuter en tant qu’administrateur**. L’Émulateur de calcul Azure, présenté plus loin dans cet article, nécessite que Visual Studio soit démarré avec des privilèges d’administrateur.
   
   Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Dans la page **Modèles**, suivez ces étapes :
    1. Sélectionnez **C#** en guise de langage de programmation.
    1. Sélectionnez **Cloud** pour le type de projet.
    1. Sélectionnez **Azure Cloud Service**.
    1. Sélectionnez **Suivant**. 
   
        :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png" alt-text="Capture d’écran de la boîte de dialogue Nouveau projet avec Cloud sélectionné et Azure Cloud Service Visual C# mis en surbrillance et délimité en rouge.":::
3.  Nommez le projet **MultiTierApp**, sélectionnez l’emplacement du projet, puis **Créer**.

    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/project-name.png" alt-text="Spécifier le nom du projet.":::    
1. Dans la page **Rôles**, double-cliquez sur **Rôle Web ASP.net**, puis sélectionnez **OK**. 
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png" alt-text="Sélectionner un rôle Web":::
4. Passez la souris sur **WebRole1** sous **Azure Cloud Service Solution**, cliquez sur l’icône en forme de crayon et renommez le rôle Web **FrontendWebRole**. Cliquez ensuite sur **OK**. (Entrez bien « Frontend » avec un « e » minuscule, et non « FrontEnd ».)
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png" alt-text="Capture d’écran de la boîte de dialogue Nouveau service Microsoft Azure Cloud avec la solution renommée FrontendWebRole.":::
5. Dans la boîte de dialogue **Créer une application web ASP.NET**, sélectionnez **MVC**, puis sélectionnez **Créer**.
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png" alt-text="Capture d’écran de la boîte de dialogue Nouveau projet ASP.NET avec MVC mis en surbrillance et délimité en rouge, ainsi que l’option Modifier l’authentification, délimitée en rouge.":::
8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Références** dans le projet **FrontendWebRole**, puis cliquez sur **Gérer les packages NuGet**.
9. Cliquez sur l’onglet **Parcourir**, puis recherchez **Azure.Messaging.ServiceBus**. Sélectionnez le package **Azure.Messaging.ServiceBus**, sélectionnez **Installer**, puis acceptez les conditions d’utilisation.
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png" alt-text="Capture d’écran de la boîte de dialogue Gérer les packages NuGet avec Azure.Messaging.ServiceBus mis en surbrillance et l’option Installer délimitée en rouge.":::

   Notez que les assemblys client nécessaires sont maintenant référencés et que certains nouveaux fichiers de code ont été ajoutés.
10. Suivez les mêmes étapes pour ajouter le package NuGet `Azure.Identity` au projet.  
10. Dans l’**Explorateur de solutions**, développez **FrontendWebRole**, cliquez avec le bouton droit sur **Modèles** et cliquez sur **Ajouter**, puis sur **Classe**. Dans la zone **Nom**, saisissez le nom **OnlineOrder.cs**. Cliquez ensuite sur **Ajouter**.

### <a name="write-the-code-for-your-web-role"></a>Écriture du code de votre rôle Web
Dans cette section, vous créez les différentes pages affichées par votre application.

1. Dans le fichier OnlineOrder.cs dans Visual Studio, remplacez la définition d'espace de noms existante par le code suivant :
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. Dans l’**Explorateur de solutions**, double-cliquez sur **Controllers\HomeController.cs**. Ajoutez les instructions **using** suivantes au début du fichier pour inclure les espaces de noms pour le modèle que vous venez de créer, ainsi que Service Bus.
   
   ```csharp
    using FrontendWebRole.Models;
    using Azure.Messaging.ServiceBus;    
   ```
3. Également dans le fichier HomeController.cs dans Visual Studio, remplacez la définition d'espace de noms existante par le code suivant. Ce code contient des méthodes pour gérer l’envoi d’éléments dans la file d’attente.
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. Dans le menu **Générer**, cliquez sur **Générer la solution** pour vérifier que votre travail est correct.
5. Maintenant, créez l’affichage de la méthode `Submit()` créée auparavant. Cliquez avec le bouton droit dans la méthode `Submit()` (la surcharge de `Submit()` qui n’accepte aucun paramètre) dans le fichier **HomeController.cs**, puis choisissez **Ajouter une vue**.
6. Dans la boîte de dialogue **Ajouter un nouvel élément généré automatiquement**, sélectionnez **Ajouter**. 
1. Dans la boîte de dialogue **Ajouter une vue**, procédez comme suit :
    1. Dans la liste **Modèle**, choisissez **Créer**. 
    1. Dans la liste **Classe de modèle**, sélectionnez la classe **OnlineOrder**.
    1. Sélectionnez **Ajouter**. 
   
        :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png" alt-text="Capture d’écran de la boîte de dialogue Ajouter une vue avec les listes déroulantes Modèle et Classe de modèle, délimitées en rouge.":::
8. À présent, modifiez le nom affiché de votre application. Dans **l’Explorateur de solutions**, double-cliquez sur le fichier **Views\Shared\\_Layout.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio.
9. Remplacez toutes les occurrences de **Mon application ASP.NET** par **Northwind Traders Products**.
10. Supprimez les liens **Home**, **About** et **Contact**. Supprimez le code en surbrillance :
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png" alt-text="Capture d’écran du code avec trois lignes de code de lien d’action H T M L mis en surbrillance.":::
11. Enfin, modifiez la page d'envoi pour inclure des informations sur la file d'attente. Dans **l’Explorateur de solutions**, double-cliquez sur le fichier **Views\Home\Submit.cshtml** pour l’ouvrir dans l’éditeur de Visual Studio. Ajoutez la ligne suivante après `<h2>Submit</h2>`. À ce stade, `ViewBag.MessageCount` est vide. Vous le remplirez plus tard.
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. Vous avez maintenant implémenté votre interface utilisateur. Vous pouvez appuyer sur **F5** pour exécuter votre application et vérifier qu’elle apparaît bien comme vous le souhaitez.
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png" alt-text="Capture d’écran de la page d’envoi de l’application.":::

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Écriture de code pour l'envoi d'éléments dans une file d'attente Service Bus
Maintenant, ajoutez le code pour envoyer des éléments dans une file d’attente. Tout d’abord, vous créez une classe qui contient les informations de connexion à votre file d’attente Service Bus. Ensuite, initialisez votre connexion à partir de Global.aspx.cs. Enfin, mettez à jour le code d’envoi que vous avez créé précédemment dans HomeController.cs pour qu’il envoie réellement les éléments dans une file d’attente Service Bus.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **FrontendWebRole** (sur le projet, et non sur le rôle). Cliquez sur **Ajouter**, puis sur **Classe**.
2. Donnez le nom **QueueConnector.cs** à la classe. Cliquez sur **Ajouter** pour créer la classe.
3. Maintenant, ajoutez le code qui encapsule les informations de connexion et initialise la connexion à une file d’attente Service Bus. Remplacez tout le contenu de QueueConnector.cs par le code suivant, puis saisissez des valeurs pour `your Service Bus namespace` (nom de votre espace de noms) et `yourKey` qui correspond à la **clé primaire** précédemment obtenue à partir du portail Azure.
   
   ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    using Azure.Messaging.ServiceBus.Administration;
       
   namespace FrontendWebRole
   {
        public static class QueueConnector
        {
            // object to send messages to a Service Bus queue
            internal static ServiceBusSender SBSender;
    
            // object to create a queue and get runtime properties (like message count) of queue
            internal static ServiceBusAdministrationClient SBAdminClient;
        
            // Fully qualified Service Bus namespace
            private const string FullyQualifiedNamespace = "<SERVICE BUS NAMESPACE NAME>.servicebus.windows.net";
            
            // The name of your queue.
            internal const string QueueName = "OrdersQueue";
        
            public static async Task Initialize()
            {
                // Create a Service Bus client that you can use to send or receive messages
                ServiceBusClient SBClient = new ServiceBusClient(FullyQualifiedNamespace, new DefaultAzureCredential());
        
                // Create a Service Bus admin client to create queue if it doesn't exist or to get message count
                SBAdminClient = new ServiceBusAdministrationClient(FullyQualifiedNamespace, new DefaultAzureCredential());
        
                // create the OrdersQueue if it doesn't exist already
                if (!(await SBAdminClient.QueueExistsAsync(QueueName)))
                {
                    await SBAdminClient.CreateQueueAsync(QueueName);
                }
        
                // create a sender for the queue 
                SBSender = SBClient.CreateSender(QueueName);    
            }
        }    
   }
   ```
4. Ensuite, assurez-vous que votre méthode **Initialize** est bien appelée. Dans l’**Explorateur de solutions**, double-cliquez sur **Global.asax\Global.asax.cs**.
5. Ajoutez la ligne de code suivante à la fin de la méthode **Application_Start**.
   
   ```csharp
    FrontendWebRole.QueueConnector.Initialize().Wait();
   ```
6. Enfin, mettez à jour le code web que vous avez créé précédemment, pour envoyer des éléments dans la file d'attente. Dans l’**Explorateur de solutions**, double-cliquez sur **Controllers\HomeController.cs**.
7. Mettez à jour la méthode `Submit()` (la surcharge qui n’accepte aucun paramètre) comme suit pour obtenir le nombre de messages dans la file d’attente.
   
   ```csharp
        public ActionResult Submit()
        {
            QueueRuntimeProperties properties = QueueConnector.adminClient.GetQueueRuntimePropertiesAsync(QueueConnector.queueName).Result;
            ViewBag.MessageCount = properties.ActiveMessageCount;

            return View();
        }
   ```
8. Mettez à jour la méthode `Submit(OnlineOrder order)` (la surcharge qui accepte un paramètre) comme suit pour envoyer des informations de commande à la file d’attente.
   
   ```csharp
        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // create a message 
                var message = new ServiceBusMessage(new BinaryData(order));

                // send the message to the queue
                QueueConnector.sbSender.SendMessageAsync(message);

                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }
   ```
9. Vous pouvez maintenant réexécuter l'application. À chaque fois que vous envoyez une commande, le nombre de messages augmente.
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png" alt-text="Capture d’écran de la page d’envoi de l’application avec le nombre de messages incrémenté à 1.":::

## <a name="create-the-worker-role"></a>Création du rôle de travail
Vous allez maintenant créer le rôle de travail qui traite les commandes envoyées. Cet exemple utilise le modèle de projet Visual Studio **Rôle de travail avec file d’attente Service Bus**. Vous avez déjà obtenu les informations d’identification requises à partir du portail.

1. Assurez-vous d’avoir connecté Visual Studio à votre compte Azure.
2. Dans Visual Studio, dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Rôles** sous le projet **MultiTierApp**.
3. Cliquez sur **Ajouter**, puis sur **Nouveau projet de rôle de travail**. La boîte de dialogue **Ajouter un nouveau projet de rôle** s’affiche.

   :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png" alt-text="Capture d’écran du volet Explorateur de solutions avec l’option Nouveau projet de rôle de travail et l’option Ajouter mises en surbrillance.":::
1. Dans la boîte de dialogue **Ajouter un nouveau projet de rôle** , sélectionnez **Rôle de travail**. Ne sélectionnez pas **Rôle de travail avec file d’attente Service Bus**, car cela génère du code qui utilise le kit de développement logiciel (SDK) Service Bus hérité. 
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png" alt-text="Capture d’écran de la boîte de dialogue Nouveau projet de rôle Active Directory avec l’option Rôle de travail avec file d'attente Service Bus mise en surbrillance et délimitée en rouge.":::
5. Dans la boîte de dialogue **Nom**, saisissez le nom de projet **OrderProcessingRole**. Cliquez ensuite sur **Ajouter**.
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **OrderProcessingRole**, puis sélectionnez **Gérer les packages NuGet**.
9. Sélectionnez l’onglet **Parcourir**, puis recherchez **Azure.Messaging.ServiceBus**. Sélectionnez le package **Azure.Messaging.ServiceBus**, sélectionnez **Installer**, puis acceptez les conditions d’utilisation.
   
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png" alt-text="Capture d’écran de la boîte de dialogue Gérer les packages NuGet avec Azure.Messaging.ServiceBus mis en surbrillance et l’option Installer délimitée en rouge.":::
1. Suivez les mêmes étapes pour ajouter le package NuGet `Azure.Identity` au projet.  
1. Créez une classe **OnlineOrder** pour représenter les commandes à mesure que vous les traitez dans la file d’attente. Vous pouvez réutiliser une classe que vous avez déjà créée. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la classe **OrderProcessingRole** (sur l’icône de la classe, non sur le rôle). Cliquez sur **Ajouter**, puis sur **Élément existant**.
1. Accédez au sous-dossier **FrontendWebRole\Models**, puis double-cliquez sur **OnlineOrder.cs** pour l’ajouter à ce projet.
1. Ajoutez l’instruction `using` suivante au fichier **WorkerRole.cs** dans le projet **OrderProcessingRole**. 

    ```csharp
    using FrontendWebRole.Models;
    using Azure.Messaging.ServiceBus;
    using Azure.Messaging.ServiceBus.Administration; 
    ```    
1. Dans **WorkerRole.cs**, ajoutez les propriétés suivantes. 

    > [!IMPORTANT]
    > Utilisez la chaîne de connexion pour l’espace de noms que vous avez noté dans le cadre des prérequis. 

    ```csharp
        // Fully qualified Service Bus namespace
        private const string FullyQualifiedNamespace = "<SERVICE BUS NAMESPACE NAME>.servicebus.windows.net";

        // The name of your queue.
        private const string QueueName = "OrdersQueue";

        // Service Bus Receiver object to receive messages message the specific queue
        private ServiceBusReceiver SBReceiver;

    ```
1. Mettez à jour la méthode `OnStart` pour créer un objet `ServiceBusClient`, puis un objet `ServiceBusReceiver` pour recevoir des messages de `OrdersQueue`. 
    
    ```csharp
        public override bool OnStart()
        {
            // Create a Service Bus client that you can use to send or receive messages
            ServiceBusClient SBClient = new ServiceBusClient(FullyQualifiedNamespace, new DefaultAzureCredential());

            CreateQueue(QueueName).Wait();

            // create a receiver that we can use to receive the message
            SBReceiver = SBClient.CreateReceiver(QueueName);

            return base.OnStart();
        }
        private async Task CreateQueue(string queueName)
        {
            // Create a Service Bus admin client to create queue if it doesn't exist or to get message count
            ServiceBusAdministrationClient SBAdminClient = new ServiceBusAdministrationClient(FullyQualifiedNamespace, new DefaultAzureCredential());

            // create the OrdersQueue if it doesn't exist already
            if (!(await SBAdminClient.QueueExistsAsync(queueName)))
            {
                await SBAdminClient.CreateQueueAsync(queueName);
            }
        }
    ```
12. Mettez à jour la méthode `RunAsync` pour inclure le code pour recevoir des messages. 

    ```csharp
        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                // receive message from the queue
                ServiceBusReceivedMessage receivedMessage = await SBReceiver.ReceiveMessageAsync();

                if (receivedMessage != null)
                {
                    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());

                    // view the message as an OnlineOrder
                    OnlineOrder order = receivedMessage.Body.ToObjectFromJson<OnlineOrder>();
                    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");

                    // complete message so that it's removed from the queue
                    await SBReceiver.CompleteMessageAsync(receivedMessage);
                }
            }
        }
    ```
14. Vous avez terminé l'application. Vous pouvez tester l’application complète en cliquant avec le bouton droit sur le projet MultiTierApp dans l’Explorateur de solutions, puis en sélectionnant **Définir comme projet de démarrage** et en appuyant sur F5. Notez que le nombre de messages n'augmente pas, car le rôle de travail traite les éléments de la file d'attente et les marque comme terminés. Vous pouvez voir le résultat du suivi de votre rôle de travail en affichant l'interface utilisateur de l'émulateur de calcul Azure. Pour cela, cliquez avec le bouton droit sur l’icône de l’émulateur dans la zone de notification de la barre des tâches, puis sélectionnez l’**interface utilisateur de l’émulateur de calcul Azure**.
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png" alt-text="Capture d’écran de ce qui s’affiche lorsque vous cliquez sur l’icône de l’émulateur. Afficher l’IU de l’émulateur de calcul se trouve dans la liste d’options.":::
    
    :::image type="content" source="./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png" alt-text="Capture d’écran de la boîte de dialogue Émulateur de calcul Microsoft Azure (Express).":::

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Service Bus, consultez les ressources suivantes :  

* [Prise en main des files d’attente Service Bus][sbacomqhowto]
* [Page du service Service Bus][sbacom]  

Pour en savoir plus sur les scénarios à plusieurs niveaux, voir :  

* [Application multiniveau .NET avec tables, files d’attente et objets blob de stockage][mutitierstorage]  



[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
