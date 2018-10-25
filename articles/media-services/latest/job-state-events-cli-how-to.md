---
title: Superviser les événements Azure Media Services avec Event Grid à partir de l’interface CLI | Microsoft Docs
description: Cet article explique comment s’abonner à Event Grid dans le but de superviser les événements Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 8145b4eb3c39511eb9cd0ed052c36b8338191d4f
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389494"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Créer et superviser des événements Azure Media Services avec Event Grid à partir d’Azure CLI

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans cet article, vous allez utiliser Azure CLI pour vous abonner à des événements pour votre compte Azure Media Services. Vous déclencherez ensuite des événements pour en examiner le résultat. En règle générale, vous envoyez des événements à un point de terminaison qui traite les données d’événement et entreprend des actions. Dans cet article, vous allez envoyer les événements à une application web qui collecte et affiche les messages.

## <a name="prerequisites"></a>Prérequis

- Avoir un abonnement Azure actif.
- [Créer un compte Media Services](create-account-cli-how-to.md).

    Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.

- Installer [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Exécutez `az --version` pour trouver la version qui est à votre disposition. Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/bash).

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner aux événements pour le compte Media Services, créons le point de terminaison pour le message d’événement. En règle générale, le point de terminaison entreprend des actions en fonction des données d’événement. Dans cet article, vous allez déployer une [application web prédéfinie](https://github.com/Azure-Samples/azure-event-grid-viewer) qui affiche les messages d’événement. La solution déployée comprend un plan App Service, une offre App Service Web Apps et du code source en provenance de GitHub.

1. Sélectionnez **Déployer sur Azure** pour déployer la solution sur votre abonnement. Dans le portail Azure, indiquez des valeurs pour les paramètres.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Le déploiement peut prendre quelques minutes. Une fois le déploiement réussi, affichez votre application web pour vérifier qu’elle s’exécute. Dans un navigateur web, accédez à : `https://<your-site-name>.azurewebsites.net`

Si vous basculez vers le site « Azure Event Grid Viewer », vous constatez qu’il n’y a pas encore d’événements.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](http://portal.azure.com) et lancez **CloudShell** pour exécuter les commandes CLI, comme indiqué dans les étapes suivantes.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0 ou une version ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Configurer l’abonnement Azure

Dans la commande suivante, fournissez l’ID d’abonnement Azure que vous souhaitez utiliser pour le compte Media Services. Vous pouvez afficher la liste des abonnements auxquels vous avez accès à en accédant à [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

## <a name="subscribe-to-media-services-events"></a>S’abonner à des événements Media Services

Vous vous abonnez à un article pour indiquer à Event Grid les événements que vous souhaitez suivre. L’exemple suivant s’abonne au compte Media Services que vous avez créé et transmet l’URL du site web que vous avez créé comme point de terminaison de la notification d’événement. 

Remplacez `<event_subscription_name>` par un nom unique pour votre abonnement aux événements. Pour `<resource_group_name>` et `<ams_account_name>`, servez-vous des valeurs que vous avez utilisées au moment de la création du compte Media Services. Pour `<endpoint_URL>`, indiquez l’URL de votre application web et ajoutez `api/updates` à l’URL de la page d’accueil. En spécifiant le point de terminaison au moment de vous abonner, vous permettez à Event Grid de gérer le routage des événements vers ce point de terminaison. 

1. Obtenir l’ID de ressource

    ```azurecli-interactive
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Par exemple : 

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. S’abonner aux événements

    ```azurecli-interactive
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Par exemple : 

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Vous risquez obtenir un avertissement de validation de la négociation. Patientez quelques minutes le temps que la négociation soit validée.

À présent, déclenchons un événement pour voir comment Event Grid distribue le message à votre point de terminaison.

## <a name="send-an-event-to-your-endpoint"></a>Envoyer un événement à votre point de terminaison

Vous pouvez déclencher des événements pour le compte Media Services en exécutant un travail d’encodage. Vous pouvez suivre [ce démarrage rapide](stream-files-dotnet-quickstart.md) pour encoder un fichier et commencer à envoyer des événements. 

Affichez à nouveau votre application web, et notez qu’un événement de validation d’abonnement lui a été envoyé. Event Grid envoie l’événement de validation pour que le point de terminaison puisse vérifier qu’il souhaite recevoir des données d’événement. Le point de terminaison doit définir `validationResponse` sur `validationCode`. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](../../event-grid/security-authentication.md). Vous pouvez examiner le code de l’application web pour voir comment il valide l’abonnement.

> [!TIP]
> Sélectionnez l’icône en forme d’œil pour développer les données d’événements. N’actualisez pas la page si vous voulez afficher tous les événements.

![Afficher l’événement d’abonnement](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Étapes suivantes

[Charger, encoder et diffuser en continu](stream-files-tutorial-with-api.md)

