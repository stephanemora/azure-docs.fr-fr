---
title: Activer une identité managée pour le routage des événements (préversion) - CLI
titleSuffix: Azure Digital Twins
description: Découvrez comment activer une identité affectée par le système pour Azure Digital Twins, et comment l’utiliser pour router des événements à l’aide d’Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e9eb5950b5cf7d4e7d0270deed72866ee28e3962
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106902"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Activer une identité managée pour le routage des événements Azure Digital Twins (préversion) : Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Cet article explique comment activer une [identité affectée par le système pour une instance Azure Digital Twins](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (actuellement en préversion) et utiliser l’identité lors du transfert d’événements vers des destinations prises en charge, par exemple les destinations [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) , et [Conteneur Stockage Azure](../storage/blobs/storage-blobs-introduction.md).

Cet article décrit le processus d’utilisation d’[**Azure CLI**](/cli/azure/what-is-azure-cli).

Les étapes décrites dans cet article sont les suivantes : 

1. Créez une instance Azure Digital Twins avec une identité affectée par le système, ou activez l’identité affectée par le système sur une instance Azure Digital Twins existante. 
1. Ajoutez un rôle ou plusieurs rôles appropriés à l’identité. Par exemple, affectez le rôle **Expéditeur de données Azure Event Hub** à l’identité si le point de terminaison est Event Hub, ou le rôle **Expéditeur de données Azure Service Bus** si le point de terminaison est Service Bus.
1. Créez un point de terminaison dans Azure Digital Twins capable d’utiliser des identités affectées par le système pour l’authentification.

## <a name="enable-system-managed-identities-for-an-instance"></a>Activer des identités gérées par le système pour une instance 

Lorsque vous activez une identité affectée par le système sur votre instance Azure Digital Twins, Azure crée automatiquement une identité pour celle-ci dans [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Cette identité peut ensuite être utilisée pour s’authentifier auprès des points de terminaison Azure Digital Twins en vue du transfert d’événements.

Vous pouvez activer des identités gérées par le système pour une instance Azure Digital Twins **dans le cadre de la configuration initiale de l’instance**, ou **l’activer ultérieurement sur une instance existante**.

L’une ou l’autre de ces méthodes de création donnera les mêmes options de configuration et le même résultat final pour votre instance. Cette section décrit comment effectuer ces deux opérations.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Ajouter une identité gérée par le système lors de la création de l’instance

Dans cette section, vous allez apprendre à activer une identité gérée par le système sur une instance Azure Digital Twins en cours de création. 

Pour ce faire, ajoutez un paramètre `--assign-identity` à la commande `az dt create` utilisée pour créer l’instance. (Pour plus d’informations sur cette commande, consultez sa [documentation de référence](/cli/azure/dt#az_dt_create) ou les [instructions générales relatives à la configuration d’une instance d’Azure Digital Twins](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Pour créer une instance avec une identité managée par le système, ajoutez le paramètre `--assign-identity` comme ceci :

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Ajouter une identité gérée par le système à une instance existante

Dans cette section, vous allez ajouter une identité managée par le système à une instance existante d’Azure Digital Twins.

Là encore, vous pouvez utiliser la commande `az dt create` et le paramètre `--assign-identity`. Au lieu de fournir le nouveau nom d’une instance à créer, vous pouvez indiquer le nom d’une instance déjà existante afin de permettre la mise à jour de la valeur `--assign-identity` pour cette instance.

La commande qui permet d’**activer** l’identité managée est la même que la commande qui permet de créer une instance avec une identité managée par le système. La valeur du paramètre de nom d’instance est la seule chose qui change :

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Pour **désactiver** l’identité managée sur une instance où elle est activée, utilisez la commande suivante afin d’affecter à `--assign-identity` la valeur `false`.

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Affecter des rôles Azure à l’identité 

Une fois l’identité affectée par le système créée pour votre instance Azure Digital Twins, vous devez lui attribuer les rôles appropriés pour l’authentification avec différents types de [points de terminaison](concepts-route-events.md) pour transférer des événements vers des destinations prises en charge. Cette section décrit les options de rôle et comment les assigner à l’identité affectée par le système.

>[!NOTE]
> Il s’agit d’une étape importante : sans elle, l’identité ne pourra pas accéder à vos points de terminaison et les événements ne seront pas remis.

### <a name="supported-destinations-and-azure-roles"></a>Destinations et rôles Azure pris en charge 

Voici les rôles minimaux dont une identité a besoin pour accéder à un point de terminaison, en fonction du type de destination. Les rôles avec des autorisations plus élevées (comme les rôles de propriétaire des données) conviennent également.

| Destination | Rôle Azure |
| --- | --- |
| Hubs d'événements Azure | Expéditeur de données Azure Event Hubs |
| Azure Service Bus | Expéditeur de données Azure Service Bus |
| Conteneur de stockage Windows Azure | Contributeur aux données Blob du stockage |

Pour plus d’informations sur les points de terminaison, les routes et les types de destinations pris en charge pour le routage dans Azure Digital Twins, consultez [*Concepts : Routes d’événements*](concepts-route-events.md).

### <a name="assign-the-role"></a>Attribuer le rôle

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Vous pouvez ajouter le paramètre `--scopes` à la commande `az dt create` pour attribuer l’identité à une ou plusieurs étendues ayant le rôle spécifié. Vous pouvez l’effectuer au moment de la création initiale de l’instance, ou plus tard en passant le nom d’une instance qui existe déjà.

Voici un exemple qui crée une instance avec une identité managée par le système, et qui attribue à cette identité un rôle personnalisé appelé `MyCustomRole` dans un hub d’événements.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Pour plus d’exemples d’attributions de rôles à l’aide de cette commande, consultez la [documentation de référence relative à **az dt create**](/cli/azure/dt#az_dt_create).

Vous pouvez également utiliser le groupe de commandes [**az role assignment**](/cli/azure/role/assignment) pour créer et gérer des rôles. Cela permet la prise en charge de scénarios supplémentaires dans lesquels vous ne souhaitez pas regrouper l’attribution de rôle avec la commande create.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Créer un point de terminaison avec une authentification basée sur l’identité

Après la configuration d’une identité gérée par le système pour votre instance Azure Digital Twins et l’affectation du ou des rôles appropriés, vous pouvez créer des [points de terminaison](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) Azure Digital Twins capables d’utiliser l’identité pour l’authentification. Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

>[!NOTE]
> Vous ne pouvez pas modifier un point de terminaison déjà créé avec une identité basée sur une clé pour passer à l’authentification basée sur l’identité. Vous devez choisir le type d’authentification lorsque le point de terminaison est créé pour la première fois.

Pour ce faire, ajoutez le paramètre `--auth-type` à la commande `az dt endpoint create` utilisée pour créer le point de terminaison. (Pour plus d’informations sur cette commande, consultez sa [documentation de référence](/cli/azure/dt/endpoint/create) ou les [instructions générales relatives à la configuration d’un point de terminaison Azure Digital Twins](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Pour créer un point de terminaison qui utilise l’authentification basée sur l’identité, spécifiez le type d’authentification `IdentityBased` avec le paramètre `--auth-type`. L’exemple ci-dessous en fournit l’illustration pour un point de terminaison Event Hubs.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Considérations relatives à la désactivation des identités gérées par le système

Étant donné qu’une identité est gérée séparément des points de terminaison qui l’utilisent, il est important de prendre en compte les effets que toute modification apportée à l’identité ou à ses rôles peut avoir sur les points de terminaison de votre instance Azure Digital Twins. Si l’identité est désactivée, ou si un rôle nécessaire à un point de terminaison est supprimé de celui-ci, le point de terminaison peut devenir inaccessible, et le flux des événements est interrompu.

Pour continuer à utiliser un point de terminaison configuré avec une identité gérée maintenant désactivée, vous devez supprimer le point de terminaison et [le recréer](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) avec un autre type d’authentification. L’opération peut prendre jusqu’à une heure avant que les événements ne soient remis au point de terminaison après cette modification.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les identités managées dans Azure AD : 
* [*Identités gérées pour les ressources Azure*](../active-directory/managed-identities-azure-resources/overview.md)