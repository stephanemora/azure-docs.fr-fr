---
title: Sauvegarder automatiquement les valeurs de clés du magasin Azure App Configuration
description: Découvrez comment configurer une sauvegarde automatique des valeurs de clés entre les magasins App Configuration
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 0b057172c822f893e602d60f77a285f0867cf368
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87367756"
---
# <a name="backup-app-configuration-stores-automatically"></a>Sauvegarder automatiquement les magasins App Configuration

Dans cet article, vous allez apprendre à configurer une sauvegarde automatique des valeurs de clés à partir d’un magasin App Configuration principal vers un magasin secondaire. Cette fonctionnalité tire parti de l’intégration entre Azure Event Grid et App Configuration. Une fois configuré, App Configuration publie des événements sur Event Grid pour toute modification apportée aux valeurs de clés dans un magasin de configuration. Event Grid prend en charge un large éventail de services Azure à partir desquels les utilisateurs peuvent s’abonner aux événements émis à chaque fois que des valeurs de clés sont créées, mises à jour ou supprimées.

## <a name="overview"></a>Vue d’ensemble

Dans ce tutoriel, vous allez utiliser une file d’attente de Stockage Azure pour recevoir des événements d’Event Grid et un déclencheur de minuteur d’Azure Functions pour traiter les événements par lots dans la file d’attente de stockage. Lorsqu’elle est déclenchée, en fonction des événements, la fonction récupère les valeurs les plus récentes des clés qui ont été modifiées dans le magasin App Configuration principal et met à jour le magasin secondaire en conséquence. Cette configuration permet de combiner plusieurs modifications qui se produisent sur une période limitée dans une seule opération de sauvegarde et d’éviter ainsi des demandes excessives envoyées à vos magasins App Configuration.

![Architecture de sauvegarde du magasin App Configuration](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Provisionnement des ressources

La motivation derrière la sauvegarde des magasins App Configuration est d’utiliser plusieurs magasins de configuration dans différentes régions Azure pour augmenter la résilience géographique de votre application. Pour ce faire, vos magasins principaux et secondaires doivent se trouver dans des régions Azure différentes. Toutes les autres ressources créées dans ce tutoriel peuvent être provisionnées dans la région de votre choix. En effet, si la région principale est confrontée à un problème technique, il n’y aura rien de nouveau à sauvegarder jusqu’à ce que la région principale soit à nouveau accessible.

Dans ce tutoriel, vous allez créer un magasin secondaire dans la région `centralus` et toutes les autres ressources dans la région `westus`.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/). Vous pouvez également utiliser Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) avec la charge de travail Développement Azure.
- Téléchargez et installez le [SDK .NET Core](https://dotnet.microsoft.com/download).
- La dernière version d’Azure CLI (2.3.1 ou version ultérieure). Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous utilisez Azure CLI, vous devez d’abord vous connecter à l’aide de `az login`. Vous pouvez également utiliser Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group).

L’exemple suivant crée un groupe de ressources nommé `<resource_group_name>` à l’emplacement `westus`.  Remplacez `<resource_group_name>` par un nom unique pour votre groupe de ressources.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Créer des magasins App Configuration

Créez vos magasins App Configuration principal et secondaire dans des régions différentes.
Remplacez  `<primary_appconfig_name>` et `<secondary_appconfig_name>` par des noms uniques pour vos magasins de configuration. Le nom du magasin doit être unique, car il est utilisé comme nom DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Créer une file d’attente de Stockage Azure

Créez un compte de stockage et une file d’attente de stockage pour recevoir les événements publiés par Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>S’abonner aux événements de votre magasin App Configuration

Vous vous abonnez à ces deux événements à partir du magasin App Configuration principal :

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

La commande suivante crée un abonnement Event Grid pour les deux événements envoyés à votre file d’attente de stockage, où le type de point de terminaison est défini sur `storagequeue` et le point de terminaison est défini sur l’ID de la file d’attente. Remplacez `<event_subscription_name>` par le nom de votre choix pour l’abonnement aux événements.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Créer une fonction Azure Functions pour gérer les événements de la file d’attente de stockage

### <a name="setup-with-ready-to-use-azure-functions"></a>Configuration avec Azure Functions prêt à l’emploi

Dans ce tutoriel, vous allez utiliser C# Azure Functions avec les propriétés suivantes :
- Pile d’exécution .NET Core 3.1
- Runtime Azure Functions version 3.x
- Fonction déclenchée par le minuteur toutes les 10 minutes

Pour faciliter la sauvegarde de vos données, nous avons testé et publié des fonctions [Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) que vous pouvez utiliser sans modifier le code. Téléchargez les fichiers projet et [publiez-les sur votre propre application de fonction Azure à partir de Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> N’apportez aucune modification aux variables d’environnement dans le code que vous avez téléchargé. Vous allez créer les paramètres d’application requis dans la section suivante.
>

### <a name="build-your-own-azure-functions"></a>Créer votre propre fonction Azure Functions

Si l’exemple de code fourni ci-dessus ne répond pas à vos besoins, vous pouvez également créer votre propre fonction Azure Functions. Votre fonction doit être en mesure d’exécuter les tâches suivantes pour effectuer la sauvegarde :
- Lire régulièrement le contenu de votre file d’attente de stockage pour voir si elle contient des notifications d’Event Grid. Pour plus d’informations sur l’implémentation, consultez [Kit SDK de la file d’attente de stockage](/azure/storage/queues/storage-quickstart-queues-dotnet).
- Si votre file d’attente de stockage contient des [notifications d’événements à partir d’Event Grid](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), extraire toutes les valeurs <key, label> uniques des messages d’événement. La combinaison clé/étiquette est l’identificateur unique pour les modifications de la valeur de clé dans le magasin principal.
- Lire tous les paramètres du magasin principal. Mettre à jour uniquement les paramètres dans le magasin secondaire qui ont un événement correspondant dans la file d’attente de stockage. Supprimer tous les paramètres du magasin secondaire qui étaient présents dans la file d’attente de stockage, mais pas dans le magasin principal. Vous pouvez utiliser le [kit SDK App Configuration](https://github.com/Azure/AppConfiguration#sdks) pour accéder programmatiquement à vos magasins de configuration.
- Supprimer les messages de la file d’attente de stockage si aucune exception n’a été levée lors du traitement.
- Implémenter la gestion des erreurs en fonction de vos besoins. Vous pouvez vous référer à l’exemple de code ci-dessus pour voir les exceptions courantes que vous pouvez éventuellement gérer.

Pour en savoir plus sur la création d’une fonction Azure Functions, consultez : [Créer une fonction dans Azure qui est déclenchée par un minuteur](/azure/azure-functions/functions-create-scheduled-function) et [Développer Azure Functions à l’aide de Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Utilisez votre meilleure jugement pour choisir la planification du minuteur en fonction de la fréquence à laquelle vous apportez des modifications à votre magasin de configuration principal. N’oubliez pas que l’exécution trop fréquente de la fonction peut finir par limiter les demandes de votre magasin.
>


## <a name="create-azure-function-app-settings"></a>Créer des paramètres d’application de fonction Azure

Si vous utilisez la fonction Azure Functions que nous avons fournie, vous avez besoin des paramètres d’application suivants dans votre application de fonction Azure :
- `PrimaryStoreEndpoint`: Point de terminaison du magasin App Configuration principal. Par exemple : `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Point de terminaison du magasin App Configuration secondaire. Par exemple : `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: URI de la file d’attente de stockage. Par exemple : `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

La commande suivante crée les paramètres d’application requis dans votre application de fonction Azure. Remplacez `<function_app_name>` par le nom de votre application de fonction Azure.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Accorder l’accès à l’identité managée de l’application de fonction Azure

Utilisez la commande suivante ou le [portail Azure](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) pour ajouter une identité managée attribuée par le système à votre application de fonction Azure.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Pour effectuer les opérations de création de ressource et de gestion des rôles nécessaires, votre compte doit bénéficier des autorisations `'Owner'` avec l’étendue appropriée (votre abonnement ou groupe de ressources). Si vous avez besoin d’aide pour l’attribution de rôle, découvrez [comment ajouter ou supprimer des attributions de rôles Azure à l’aide du portail Azure](/azure/role-based-access-control/role-assignments-portal).

Utilisez les commandes suivantes ou le [portail Azure](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) pour accorder à l’identité managée de votre application de fonction Azure l’accès à vos magasins App Configuration.
- Attribuez le rôle `App Configuration Data Reader` dans le magasin App Configuration principal.
- Attribuez le rôle `App Configuration Data Owner` dans le magasin App Configuration secondaire.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Utilisez la commande suivante ou le [portail Azure](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal) pour accorder à l’identité managée de votre application de fonction Azure l’accès à votre file d’attente de stockage. 
- Attribuez le rôle `Storage Queue Data Contributor` dans la file d’attente de stockage.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Déclencher un événement App Configuration

Pour vérifier que tout fonctionne, vous pouvez créer/mettre à jour/supprimer une valeur de clé à partir du magasin principal. Cette modification doit s’afficher automatiquement dans le magasin secondaire quelques secondes après le déclenchement d’Azure Functions par le minuteur.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Vous avez déclenché l’événement et, dans quelques instants, Event Grid enverra la notification de l’événement à votre file d’attente de stockage Azure. ***Après la prochaine exécution planifiée de votre fonction Azure Functions***, affichez les paramètres de configuration dans votre magasin secondaire pour voir s’il contient la valeur de clé mise à jour du magasin principal.

> [!NOTE]
> Vous pouvez [déclencher votre fonction Azure Functions manuellement](/azure/azure-functions/functions-manually-run-non-http) pendant le test et la résolution des problèmes sans attendre le déclenchement du minuteur planifié.

Après avoir vérifié que la fonction de sauvegarde a été exécutée avec succès, vous pouvez voir que la clé est maintenant présente dans votre magasin secondaire.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Dépannage

Si le nouveau paramètre ne s’affiche pas dans votre magasin secondaire :

- Assurez-vous que la fonction de sauvegarde a été déclenchée ***après*** la création du paramètre dans votre magasin principal.
- Il est possible qu’Event Grid n’ait pas pu envoyer à temps la notification de l’événement à la file d’attente de stockage. Vérifiez si votre file d’attente de stockage contient toujours la notification de l’événement de votre magasin principal et, si c’est le cas, redéclenchez la fonction de sauvegarde.
- Consultez les [journaux Azure Functions](/azure/azure-functions/functions-create-scheduled-function#test-the-function) pour détecter les erreurs ou les avertissements.
- Utilisez le [portail Azure](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) pour vous assurer que l’application de fonction Azure contient des valeurs correctes pour les paramètres d’application que votre fonction Azure Functions tente de lire.
- Vous pouvez également configurer la surveillance et les alertes pour votre fonction Azure Functions à l’aide [d’Azure Application Insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous envisagez de continuer à utiliser ce service App Configuration et l’abonnement à un événement, ne supprimez pas les ressources créées dans cet article. Sinon, utilisez la commande suivante pour supprimer les ressources créées avec cet article.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment configurer la sauvegarde automatique de vos valeurs de clés, apprenez-en plus sur la façon d’optimiser la résilience géographique de votre application :

- [Résilience et récupération d’urgence](concept-disaster-recovery.md)
