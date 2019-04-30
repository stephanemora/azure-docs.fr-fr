---
title: 'Scénario d’application logique : créer un déclencheur à l’aide d’Azure Functions et d’Azure Service Bus | Microsoft Docs'
description: Créer des fonctions qui déclenchent des applications logiques avec Azure Functions et Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 1d3c4039ae823d3797e768af5892333d4d925268
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996110"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Scénario : Déclencher des applications logiques avec Azure Functions et Azure Service Bus

Vous pouvez utiliser Azure Functions afin de créer un déclencheur pour une application logique lorsque vous avez besoin de déployer un écouteur ou une tâche de longue durée. Par exemple, vous pouvez créer une fonction qui écoute une file d’attente, puis déclenche immédiatement une application logique en tant que déclencheur d’émission.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Pour pouvoir créer une fonction Azure, [créez une application de fonction](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Créer une application logique

Dans cet exemple, une fonction est en cours d’exécution pour chaque application logique à déclencher. Créez d’abord une application logique comportant un déclencheur de demande HTTP. La fonction appelle ce point de terminaison chaque fois qu’un message de la file d’attente est reçu.  

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et créez une application logique vide. 

   Si vous débutez avec logic apps, consultez [Guide de démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dans la zone de recherche, entrez « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lors de la réception d’une requête HTTP**

   ![Sélectionner le déclencheur](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Avec le déclencheur **Requête**, il est possible en option d’entrer un schéma JSON pour une utilisation avec le message de file d’attente. Les schémas JSON aident le Concepteur d’applications logiques à comprendre la structure des données d’entrée et facilitent la sélection des sorties dans le workflow. 

   Pour spécifier un schéma, entrez-le dans la zone **Schéma JSON de corps de la demande**, par exemple : 

   ![Spécifier un schéma JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Si vous n’avez pas de schéma, mais un exemple de charge utile au format JSON, vous pouvez générer un schéma à partir de cette charge utile.

   1. Dans le déclencheur Requête, choisissez **Utiliser l’exemple de charge utile pour générer le schéma**.

   1. Sous **Entrer ou coller un exemple de charge utile JSON**, entrez votre exemple de charge utile, puis choisissez **Terminé**.
      
      ![Entrer un exemple de charge utile](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Cet exemple de charge utile génère ce schéma, qui apparaît dans le déclencheur :

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Ajoutez les autres actions qui devront se produire après réception du message de file d’attente. 

   Par exemple, vous pouvez envoyer un e-mail avec le connecteur Office 365 Outlook.

1. Enregistrez votre application logique, ce qui génère l’URL de rappel du déclencheur dans cette application logique. Cette URL apparaît dans la propriété **URL HTTP POST**.

   ![URL de rappel générée pour le déclencheur](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Créer une fonction Azure

Maintenant, créez la fonction qui agit comme déclencheur et écoute la file d’attente. 

1. Sur le Portail Azure, ouvrez et développez votre application de fonction, si ce n’est pas déjà le cas. 

1. Sous le nom de votre application de fonction, développez **Fonctions**. Dans le volet **Fonction**, choisissez **Nouvelle fonction**. Sélectionnez ce modèle : **Déclencheur de file d’attente de Bus de service :C#**
   
   ![Sélectionner le portail Azure Functions](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Donnez un nom à votre déclencheur, puis configurez la connexion à la file d’attente Service Bus qui utilise l’écouteur `OnMessageReceive()` du Kit SDK Azure Service Bus.

1. Écrivez une fonction de base pour appeler le point de terminaison d’application logique créé précédemment en utilisant le message de la file d’attente comme déclencheur, par exemple : 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   Cet exemple utilise un type de contenu de message `application/json`, mais vous pouvez le modifier si nécessaire.

1. Pour tester la fonction, ajoutez un message de file d’attente à l’aide d’un outil comme [l’Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer). 

   L’application logique se déclenche dès que la fonction reçoit le message.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

