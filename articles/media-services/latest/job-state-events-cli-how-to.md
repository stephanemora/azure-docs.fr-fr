---
title: Acheminer des événements Azure Media Services vers un point de terminaison web personnalisé | Microsoft Docs
description: Utilisez Azure Event Grid pour vous abonner à un événement de modification de l’état du travail Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6a098f43819bb6581b2c5978fbcc4a378a8514c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638498"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Acheminer des événements Azure Media Services vers un point de terminaison web personnalisé à l’aide de CLI

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans cet article, vous utilisez Azure CLI pour vous abonner à des événements de modification de l’état du travail Azure Media Services et déclencher l’événement pour afficher le résultat. 

En règle générale, vous envoyez des événements à un point de terminaison qui répond à l’événement, comme un webhook ou une fonction Azure. Ce didacticiel montre comment créer et définir un webhook.

En suivant les instructions de cet article, vous voyez que les données d’événement ont été envoyées à un point de terminaison.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](http://portal.azure.com) et lancez **CloudShell** pour exécuter les commandes CLI, comme indiqué dans les étapes suivantes.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0 ou une version ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du compte Media Services, le nom de stockage et le nom de ressource.

## <a name="enable-event-grid-resource-provider"></a>Activer le fournisseur de ressources Event Grid

La première chose à faire est de vous assurer que le fournisseur de ressources Event Grid est activé sur votre abonnement. 

Dans le portail **Azure**, procédez comme suit :

1. Accédez aux abonnements.
2. Sélectionnez votre abonnement.
3. Sous Paramètres, sélectionnez Fournisseurs de ressources.
4. Recherchez « EventGrid ».
5. Assurez-vous qu’Event Grid est inscrit. S’il ne l’est pas, appuyez sur le bouton **Inscrire**.  

## <a name="create-a-generic-azure-function-webhook"></a>Créer un webhook Fonction Azure générique 

### <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de s’abonner à l’article d’Event Grid, créez un point de terminaison qui collecte les messages, afin que vous puissiez les consulter.

Créez une fonction déclenchée par un webhook générique, comme décrit dans l’article [Webhook générique](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). Dans ce didacticiel, le code **C#** est utilisé.

Une fois le webhook créé, copiez l’URL en cliquant sur le lien *Obtenir l’URL de la fonction* en haut de la fenêtre du portail **Azure**. Vous n’avez pas besoin de la dernière partie de l’URL (*&clientID=default*).

![Créer un webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Valider le webhook

Lorsque vous inscrivez votre propre point de terminaison webhook auprès d’Event Grid, il vous envoie une requête POST avec un code de validation simple pour prouver que vous êtes le propriétaire du point de terminaison. Votre application doit répondre en renvoyant le code de validation. Event Grid ne remet aucun événement aux points de terminaison webhook qui n’ont pas été validés. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](https://docs.microsoft.com/azure/event-grid/security-authentication). Cette section spécifie deux parties qui doivent être définies pour que la validation réussisse.

#### <a name="update-the-source-code"></a>Mettre à jour le code source

Une fois que vous avez créé votre webhook, le fichier **run.csx** s’affiche dans le navigateur. Remplacez le code par défaut par le code suivant. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Mettre à jour le corps de la demande test

À droite de la fenêtre du portail **Azure** se trouvent deux onglets : **Afficher les fichiers** et **Test**. Sélectionnez l’onglet **Test**. Dans le champ **Corps de la demande**, entrez le JSON suivant. Vous pouvez le coller tel quel, nul besoin de modifier des valeurs.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Appuyez sur **Enregistrer et exécuter** en haut de la fenêtre.

![Corps de la demande](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>S’inscrire à l’abonnement Event Grid 

Vous vous abonnez à un article pour indiquer à Event Grid les événements que vous souhaitez suivre. L’exemple suivant montre l’abonnement au compte Media Services que vous avez créé et transmet l’URL à partir du webhook Fonction Azure que vous avez créé en tant que point de terminaison de la notification d’événement. 

Remplacez `<event_subscription_name>` par un nom unique pour votre abonnement aux événements. Pour `<resource_group_name>` et `<ams_account_name>`, utilisez les valeurs que vous avez créées précédemment.  Pour `<endpoint_URL>`, collez votre URL de point de terminaison. Supprimez *&clientID=default* de l’URL. En spécifiant un point de terminaison lors de l’abonnement, Event Grid gère le routage d’événements vers ce point de terminaison. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

La valeur ID de ressource du compte Media Services ressemble à ceci :

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Tester les événements

Exécutez un travail d’encodage. Par exemple, comme décrit dans le démarrage rapide [Diffuser des fichiers vidéo en continu](stream-files-dotnet-quickstart.md).

Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Recherchez le webhook que vous avez créé précédemment. Cliquez sur **Surveiller** et **Actualiser**. Vous voyez les événements de modification de l’état du travail : « En file d’attente », « Planifié », « Traitement », « Terminé », « Erreur », « Annulé », « Annulation en cours ».  Pour plus d’informations, consultez les [schémas d’événement Media Services](media-services-event-schemas.md).

Par exemple : 

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Tester les événements](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à utiliser ce compte de stockage et l’abonnement à un événement, ne supprimez pas les ressources créées dans cet article. Sinon, utilisez la commande suivante pour supprimer les ressources créées avec cet article.

Remplacez `<resource_group_name>` par le nom du groupe de ressources que vous avez créé plus haut.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Étapes suivantes

[Réaction aux événements](reacting-to-media-services-events.md)## Voir aussi

## <a name="see-also"></a>Voir aussi

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
