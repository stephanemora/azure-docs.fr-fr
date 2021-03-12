---
title: 'Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec PowerShell'
description: Utilisez Azure Event Grid pour vous abonner aux événements Azure Cache pour Redis, envoyer les événements à un Webhook et prendre en charge les événements dans une application web.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508161"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec PowerShell

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans ce guide de démarrage rapide, vous allez utiliser Azure PowerShell pour vous abonner aux événements Azure Cache pour Redis, déclencher un événement et voir les résultats. 

En règle générale, vous envoyez des événements à un point de terminaison qui traite les données d’événement et entreprend des actions. Toutefois, pour simplifier ce guide de démarrage rapide, vous allez envoyer des événements à une application web qui va collecter et afficher les messages. Une fois que vous aurez effectué les étapes décrites dans ce guide de démarrage rapide, vous verrez que les données d’événement ont été envoyées à l’application web.

## <a name="setup"></a>Programme d’installation

Pour ce guide de démarrage rapide, vous devez exécuter la dernière version d’Azure PowerShell. Si vous devez installer ou mettre à niveau, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Connect-AzAccount
```

Cet exemple utilise **westus2** et stocke la sélection dans une variable à utiliser partout ailleurs.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les rubriques Event Grid sont déployées en tant que ressources Azure individuelles et doivent être provisionnées sous un groupe de ressources Azure. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et managées.

Créez un groupe de ressources avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

L’exemple suivant crée un groupe de ressources nommé **gridResourceGroup** à l’emplacement **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Créer une instance Cache Redis Azure 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Pour plus d’informations sur la création d’une instance de cache dans PowerShell, consultez la [documentation de référence d’Azure PowerShell](/powershell/module/az.rediscache/new-azrediscache). 

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner à la rubrique, nous allons créer le point de terminaison pour le message de l’événement. En règle générale, le point de terminaison entreprend des actions en fonction des données d’événement. Pour simplifier ce guide de démarrage rapide, déployez une [application web prédéfinie](https://github.com/Azure-Samples/azure-event-grid-viewer) qui affiche les messages d’événement. La solution déployée comprend un plan App Service, une offre App Service Web Apps et du code source en provenance de GitHub.

Remplacez `<your-site-name>` par un nom unique pour votre application web. Le nom de l’application web doit être unique, car il fait partie de l’entrée DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Le déploiement peut prendre quelques minutes. Une fois le déploiement réussi, affichez votre application web pour vérifier qu’elle s’exécute. Dans un navigateur web, accédez à : `https://<your-site-name>.azurewebsites.net`

Vous devez voir le site sans messages affichés.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Site de visionneuse Event Grid vide.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>S’abonner à l’événement Azure Cache pour Redis

Au cours de cette étape, vous vous abonnez à une rubrique pour indiquer à Event Grid les événements que vous souhaitez suivre. L’exemple suivant permet de s’abonner à l’instance de cache que vous avez créée, et de passer l’URL à partir de votre application web en tant que point de terminaison de la notification d’événements. Le point de terminaison de votre application web doit inclure le suffixe `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Affichez à nouveau votre application web, et notez qu’un événement de validation d’abonnement lui a été envoyé. Sélectionnez l’icône en forme d’œil pour développer les données d’événements. Event Grid envoie l’événement de validation pour que le point de terminaison puisse vérifier qu’il souhaite recevoir des données d’événement. L’application web inclut du code pour valider l’abonnement.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Visionneuse Azure Event Grid.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Déclencher un événement à partir d’Azure Cache pour Redis

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Pour plus d’informations sur l’importation dans PowerShell, consultez la [documentation de référence d’Azure PowerShell](/powershell/module/az.rediscache/import-azrediscache). 

Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Affichez votre application web pour voir l’événement que vous venez d’envoyer.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous comptez continuer à utiliser cette instance d’Azure Cache pour Redis et l’abonnement aux événements, ne supprimez pas les ressources créées dans ce guide de démarrage rapide. Sinon, utilisez la commande suivante pour supprimer les ressources créées dans ce guide de démarrage rapide.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous savez créer des rubriques et des abonnements aux événements, vous pouvez en apprendre davantage sur les événements Azure Cache pour Redis et sur ce qu’Event Grid peut vous offrir :

- [Réaction aux événements Azure Cache pour Redis](cache-event-grid.md)
- [À propos d’Event Grid](../event-grid/overview.md)