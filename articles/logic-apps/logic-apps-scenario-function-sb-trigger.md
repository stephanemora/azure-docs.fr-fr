---
title: Appeler ou déclencher des applications logiques avec Azure Functions et Azure Service Bus
description: Créer des fonctions Azure qui appellent ou déclenchent des applications logiques à l’aide d’Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494954"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Appeler ou déclencher des applications logiques à l’aide d’Azure Functions et Azure Service Bus

Vous pouvez utiliser [Azure Functions](../azure-functions/functions-overview.md) pour déclencher une application logique lorsque vous avez besoin déployer un écouteur ou une tâche longue. Par exemple, vous pouvez créer une fonction Azure qui écoute un [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) de file d’attente et déclenche immédiatement une application logique comme un déclencheur d’émission.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Un espace de noms Azure Service Bus. Si vous n’avez pas un espace de noms, [créez d’abord votre espace de noms](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Une Azure function app, qui est un conteneur pour Azure functions. Si vous n’avez pas une application de fonction, [d’abord créer votre application de fonction](../azure-functions/functions-create-first-azure-function.md)et vous assurer que vous sélectionnez .NET en tant que la pile d’exécution.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Créer une application logique

Pour ce scénario, vous disposez d’une fonction de chaque application logique que vous souhaitez déclencher en cours d’exécution. Tout d’abord, créez une application logique qui commence par un déclencheur de demande HTTP. La fonction appelle ce point de terminaison chaque fois qu’un message de la file d’attente est reçu.  

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et créez une application logique vide.

   Si vous débutez avec logic apps, consultez [Guide de démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Dans la zone de recherche, entrez « http request ». Dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lors de la réception d’une requête HTTP**

   ![Sélectionner le déclencheur](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Avec le déclencheur de requête, vous pouvez éventuellement entrer un schéma JSON à utiliser avec le message de file d’attente. Schémas JSON permettent le Concepteur d’application logique comprendre la structure pour les données d’entrée et de faciliter les sorties que vous pouvez utiliser dans votre flux de travail.

1. Pour spécifier un schéma, entrez-le dans la zone **Schéma JSON de corps de la demande**, par exemple :

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

1. Ajouter d’autres actions que vous souhaitez exécuter après réception du message de file d’attente.

   Par exemple, vous pouvez envoyer un e-mail avec le connecteur Office 365 Outlook.

1. Enregistrez votre application logique, ce qui génère l’URL de rappel du déclencheur dans cette application logique. Vous utilisez une version ultérieure, cette URL de rappel dans le code pour le déclencheur de file d’attente de Bus de Service Azure.

   Le rappel URL s’affiche dans le **URL HTTP POST** propriété.

   ![URL de rappel générée pour le déclencheur](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Créer une fonction Azure

Maintenant, créez la fonction qui agit comme déclencheur et écoute la file d’attente.

1. Sur le Portail Azure, ouvrez et développez votre application de fonction, si ce n’est pas déjà le cas. 

1. Sous le nom de votre application de fonction, développez **Fonctions**. Dans le volet **Fonction**, choisissez **Nouvelle fonction**.

   ![Développez des « Fonctions » et choisissez « Nouvelle fonction »](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Sélectionnez ce modèle, selon que vous avez créé une nouvelle application de fonction où vous avez sélectionné .NET en tant que la pile d’exécution, ou si vous utilisez une application de fonction existante.

   * Pour les nouvelles applications de fonction, sélectionnez ce modèle : **Déclencheur de file d’attente de Bus de service**

     ![Sélectionner un modèle pour la nouvelle application de fonction](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Pour une application de fonction existante, sélectionnez ce modèle : **Déclencheur de file d’attente de Bus de service :C#**

     ![Sélectionner un modèle d’application de fonction existante](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Sur le **déclencheur de file d’attente de Bus de Service Azure** volet, fournissez un nom pour votre déclencheur, puis configurer le **connexion Service Bus** pour la file d’attente, qui utilise le Kit de développement logiciel Azure Service Bus `OnMessageReceive()` écouteur, puis choisissez **Créer**.

1. Écrire une fonction de base pour appeler le point de terminaison application logique créée précédemment en utilisant le message de file d’attente en tant que déclencheur. Cet exemple utilise un type de contenu de message `application/json`, mais vous pouvez le modifier si nécessaire. Si possible, réutiliser l’instance de clients HTTP. Pour plus d’informations, consultez [gérer les connexions dans Azure Functions](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Pour tester la fonction, ajoutez un message de file d’attente à l’aide d’un outil comme [l’Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   L’application logique se déclenche dès que la fonction reçoit le message.

## <a name="next-steps"></a>Étapes suivantes

[Appeler, déclencher ou imbriquer des workflows à l’aide de points de terminaison HTTP](../logic-apps/logic-apps-http-endpoint.md)