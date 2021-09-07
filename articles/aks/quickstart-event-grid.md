---
title: S’abonner aux événements Azure Kubernetes Service avec Azure Event Grid (préversion)
description: Utilisez Azure Event Grid pour vous abonner à des événements Azure Kubernetes Service.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: cc4801dc588216cbcfb2c6c2742643b2e0546e63
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734007"
---
# <a name="quickstart-subscribe-to-azure-kubernetes-service-aks-events-with-azure-event-grid-preview"></a>Démarrage rapide : S’abonner aux événements Azure Kubernetes Service (AKS) avec Azure Event Grid (préversion)

Azure Event Grid est un service de routage d’événements entièrement géré qui permet une consommation d’événements uniforme avec un modèle publication-abonnement.

Dans ce guide de démarrage rapide, vous allez créer un cluster AKS et vous abonner à des événements AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI](/cli/azure/install-azure-cli).

### <a name="register-the-eventgridpreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `EventgridPreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `EventgridPreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `EventgridPreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EventgridPreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EventgridPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Créez un cluster AKS avec la commande [az aks create][az-aks-create]. L’exemple suivant crée un groupe de ressources *MyResourceGroup* et un cluster nommé *MyAKS* avec un nœud dans le groupe de ressources *MyResourceGroup* :

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus  --node-count 1 --generate-ssh-keys
```

## <a name="subscribe-to-aks-events"></a>S’abonner à des événements AKS

Créez un espace de noms et un hub d’événements à l’aide des commandes [az eventhubs namespace create][az-eventhubs-namespace-create] et [az eventhubs eventhub create][az-eventhubs-eventhub-create]. L’exemple suivant crée un espace de noms *MyNamespace* et un hub d’événements *MyEventGridHub* dans *MyNamespace*, tous deux dans le groupe de ressources *MyResourceGroup*.

```azurecli
az eventhubs namespace create --location eastus --name MyNamespace -g MyResourceGroup
az eventhubs eventhub create --name MyEventGridHub --namespace-name MyNamespace -g MyResourceGroup
```

> [!NOTE]
> Le *nom* de votre espace de noms doit être unique.

Abonnez-vous aux événements AKS à l’aide de la commande [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] :

```azurecli
SOURCE_RESOURCE_ID=$(az aks show -g MyResourceGroup -n MyAKS --query id --output tsv)
ENDPOINT=$(az eventhubs eventhub show -g MyResourceGroup -n MyEventGridHub --namespace-name MyNamespace --query id --output tsv)
az eventgrid event-subscription create --name MyEventGridSubscription \
--source-resource-id $SOURCE_RESOURCE_ID \
--endpoint-type eventhub \
--endpoint $ENDPOINT
```

Vérifiez votre abonnement aux événements AKS à l’aide de `az eventgrid event-subscription list` :

```azurecli
az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
```

L’exemple de sortie suivant indique que vous êtes abonné à des événements à partir du cluster *MyAKS* et que ces événements sont remis au hub d’événements *MyEventGridHub* :

```output
$ az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
[
  {
    "deadLetterDestination": null,
    "deadLetterWithResourceIdentity": null,
    "deliveryWithResourceIdentity": null,
    "destination": {
      "deliveryAttributeMappings": null,
      "endpointType": "EventHub",
      "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNamespace/eventhubs/MyEventGridHub"
    },
    "eventDeliverySchema": "EventGridSchema",
    "expirationTimeUtc": null,
    "filter": {
      "advancedFilters": null,
      "enableAdvancedFilteringOnArrays": null,
      "includedEventTypes": [
        "Microsoft.ContainerService.NewKubernetesVersionAvailable"
      ],
      "isSubjectCaseSensitive": null,
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    },
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/MyAKS/providers/Microsoft.EventGrid/eventSubscriptions/MyEventGridSubscription",
    "labels": null,
    "name": "MyEventGridSubscription",
    "provisioningState": "Succeeded",
    "resourceGroup": "MyResourceGroup",
    "retryPolicy": {
      "eventTimeToLiveInMinutes": 1440,
      "maxDeliveryAttempts": 30
    },
    "systemData": null,
    "topic": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/microsoft.containerservice/managedclusters/MyAKS",
    "type": "Microsoft.EventGrid/eventSubscriptions"
  }
]
```

Lorsque des événements AKS se produisent, ces événements s’affichent dans votre hub d’événements. Par exemple, quand la liste des versions Kubernetes disponibles pour vos clusters change, un événement `Microsoft.ContainerService.NewKubernetesVersionAvailable` s’affiche. Pour plus d’informations sur les événements émis par AKS, consultez [Azure Kubernetes Service (AKS) en tant que source Event Grid][aks-events].

## <a name="delete-the-cluster-and-subscriptions"></a>Supprimer le cluster et les abonnements

Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le cluster AKS, l’espace de noms, le hub d’événements ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS][sp-delete].
> 
> Si vous avez utilisé une identité managée, l’identité est managée par la plateforme et n’a pas besoin d’être supprimée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un cluster Kubernetes, puis vous vous êtes abonné aux événements AKS dans Azure Event Hub.

Pour en savoir plus sur AKS et parcourir le code complet de l’exemple de déploiement, passez au tutoriel sur le cluster Kubernetes.

> [!div class="nextstepaction"]
> [Didacticiel AKS][aks-tutorial]

[aks-events]: ../event-grid/event-schema-aks.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-eventhubs-namespace-create]: /cli/azure/eventhubs/namespace?view=azure-cli-latest&preserve-view=true#az-eventhubs-namespace-create
[az-eventhubs-eventhub-create]: /cli/azure/eventhubs/eventhub?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-create
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true#az-eventgrid-event-subscription-create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
