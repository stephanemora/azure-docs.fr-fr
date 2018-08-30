---
title: Démarrage rapide Azure - Créer un Event Hub à l’aide de PowerShell | Microsoft Docs
description: Ce démarrage rapide décrit la création d’un Event Hub à l’aide de Azure PowerShell puis l’envoi et la réception des événements à l’aide du Kit de développement .NET Standard.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b1f0c0d06f6c5f99a843a384e1dda667b967a02b
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42743905"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Démarrage rapide : créer un Event Hub à l’aide de Azure PowerShell

Azure Event Hubs est une plateforme de flux de données très scalable et un service d’ingestion capable de recevoir et de traiter des millions d’événements par seconde. Ce guide de démarrage rapide montre comment créer un hub d’événements avec Azure PowerShell, puis comment envoyer et recevoir depuis et vers un hub d’événements à l’aide du kit SDK .NET Standard.

Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit][] avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants :

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) ou ultérieur.
- [Kit SDK .NET Standard](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous utilisez PowerShell localement, vous devez exécuter la dernière version de PowerShell pour suivre ce guide de démarrage rapide. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installer et configurer Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Provisionner les ressources

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est une collection logique de ressources Azure, dont vous avez besoin pour créer un hub d’événements. 

L’exemple suivant crée un groupe de ressources dans la région USA Est. Remplacez `myResourceGroup` par le nom du groupe de ressources que vous souhaitez utiliser :

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Une fois que vous avez créé votre groupe de ressources, créez un espace de noms Event Hubs au sein de ce groupe de ressources. Un espace de noms Event Hubs fournit un nom de domaine complet unique dans lequel vous pouvez créer votre hub d’événements. Remplacez `namespace_name` par un nom unique pour votre espace de noms :

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Créer un hub d’événements

Maintenant que vous avez un espace de noms Event Hubs, créez un hub d’événements au sein de cet espace de noms :

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Créer un compte de stockage pour Event Processor Host

Event Processor Host simplifie la réception d’événements provenant d’Event Hubs en gérant les points de contrôle et les destinataires parallèles. Pour les points de contrôle, Event Processor Host nécessite un compte de stockage. Pour créer un compte de stockage et obtenir ses clés, exécutez les commandes suivantes :

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Obtenir la chaîne de connexion

Une chaîne de connexion est nécessaire pour se connecter à votre hub d’événements et traiter les événements. Pour obtenir votre chaîne de connexion, exécutez :

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Envoyer le flux vers Event Hubs

Vous pouvez maintenant envoyer le flux d’événements à votre hub d’événements. Vous pouvez télécharger les exemples ou cloner Git à partir du [dépôt Event Hubs](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Ingérer les événements

Pour démarrer le flux d’événements, téléchargez [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) à partir de GitHub, ou clonez le [dépôt Event Hubs GitHub](https://github.com/Azure/azure-event-hubs) en émettant la commande suivante :

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Accédez au dossier \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender et chargez le fichier SampleSender.sln sur Visual Studio.

Ensuite, ajoutez le package Nuget [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) au projet.

Dans le fichier Program.cs, remplacez les espaces réservés suivants par le nom et la chaîne de connexion de votre hub d’événements :

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Maintenant, générez et exécutez l’exemple. Vous pouvez voir les événements en cours d’ingestion dans votre hub d’événements :

![][3]

### <a name="receive-and-process-events"></a>Recevoir et traiter les événements

Téléchargez maintenant l’exemple de destinataire Event Processor Host, qui reçoit les messages que vous venez d’envoyer. Téléchargez [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) à partir de GitHub ou clonez le [dépôt Event Hubs GitHub](https://github.com/Azure/azure-event-hubs) en émettant la commande suivante :

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Accédez au dossier \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver et chargez le fichier solution SampleEphReceiver.sln sur Visual Studio.

Ensuite, ajoutez les packages Nuget [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) et [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) dans le projet.

Dans le fichier Program.cs, remplacez les constantes suivantes par leurs valeurs correspondantes :

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Maintenant, générez et exécutez l’exemple. Vous pouvez voir les événements en cours de réception dans votre application exemple :

![][4]

Dans le portail Azure, vous pouvez voir la vitesse à laquelle les événements sont traités pour un espace de noms Event Hubs donné, comme illustré :

![][5]

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous avez terminé ce guide de démarrage rapide, vous pouvez supprimer votre groupe de ressources et l’espace de noms, ainsi que le compte de stockage et le hub d’événements qu’il contient. Remplacez `myResourceGroup` par le nom du groupe de ressources que vous avez créé. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un espace de noms Event Hubs et d’autres ressources nécessaires pour envoyer et recevoir des événements depuis votre hub d’événements. Pour en savoir plus, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Visualiser les anomalies de données sur les flux de données Event Hubs](event-hubs-tutorial-visualize-anomalies.md)

[créez un compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
