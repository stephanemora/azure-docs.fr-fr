---
title: Acheminer des événements avec une identité managée
titleSuffix: Azure Digital Twins
description: Découvrez comment activer une identité affectée par le système pour Azure Digital Twins, et comment l’utiliser pour router des événements à l’aide du portail Azure ou CLI.
author: baanders
ms.author: baanders
ms.date: 6/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: subject-rbac-steps, contperf-fy21q4
ms.openlocfilehash: 4d50c40426d5fb687b28a965b9d921ef6fc4df38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651911"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events"></a>Activer une identité managée pour le routage des événements Azure Digital Twins

Cet article explique comment activer une [identité affectée par le système pour une instance Azure Digital Twins](concepts-security.md#managed-identity-for-accessing-other-resources) et utiliser l’identité lors du transfert d’événements vers des destinations de routage prises en charge. La configuration d'une identité managée n'est pas requise pour le routage, mais elle peut aider l'instance à accéder facilement à d'autres ressources protégées par Azure AD, telles que [Event Hub](../event-hubs/event-hubs-about.md), les destinations [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)  et [Azure Storage Container](../storage/blobs/storage-blobs-introduction.md).

Les étapes décrites dans cet article sont les suivantes : 

1. Créez une instance Azure Digital Twins avec une identité affectée par le système, ou activez l’identité affectée par le système sur une instance Azure Digital Twins existante. 
1. Ajoutez un rôle ou plusieurs rôles appropriés à l’identité. Par exemple, affectez le rôle **Expéditeur de données Azure Event Hub** à l’identité si le point de terminaison est Event Hub, ou le rôle **Expéditeur de données Azure Service Bus** si le point de terminaison est Service Bus.
1. Créez un point de terminaison dans Azure Digital Twins capable d’utiliser des identités affectées par le système pour l’authentification.

## <a name="enable-system-managed-identity-for-the-instance"></a>Activer l’identité gérée par le système pour l’instance 

Lorsque vous activez une identité affectée par le système sur votre instance Azure Digital Twins, Azure crée automatiquement une identité pour celle-ci dans [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Cette identité peut ensuite être utilisée pour s’authentifier auprès des points de terminaison Azure Digital Twins en vue du transfert d’événements.

Vous pouvez activer des identités gérées par le système pour une instance Azure Digital Twins **dans le cadre de la configuration initiale de l’instance**, ou **l’activer ultérieurement sur une instance existante**.

L’une ou l’autre de ces méthodes de création donnera les mêmes options de configuration et le même résultat final pour votre instance. Cette section décrit comment effectuer ces deux opérations.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Ajouter une identité gérée par le système lors de la création de l’instance

Dans cette section, vous allez apprendre à activer une identité gérée par le système pour une instance Azure Digital Twins en cours de création. Vous pouvez activer l'identité si vous créez l'instance avec le [portail Azure](https://portal.azure.com) ou [Azure CLI](/cli/azure/what-is-azure-cli). Utilisez les onglets ci-dessous pour sélectionner des instructions pour votre expérience préférée.

# <a name="portal"></a>[Portail](#tab/portal) 

Pour ajouter une identité gérée lors de la création de l’instance dans le portail, commencez [par créer une instance comme vous le feriez normalement](how-to-set-up-instance-portal.md).

L’option d’identité gérée par le système se trouve dans l’onglet **Avancé** de la configuration de l’instance.

Dans cet onglet, sélectionnez l’option **On** (Activer) pour **Identité gérée par le système** afin d’activer cette fonctionnalité.

:::image type="content" source="media/how-to-route-with-managed-identity/create-instance-advanced.png" alt-text="Capture d’écran du portail Azure, montrant l’onglet Avancé de la boîte de dialogue Créer une ressource pour Azure Digital Twins. L’identité gérée par le système est activée.":::

Vous pouvez ensuite utiliser les boutons de navigation inférieurs pour continuer le reste de la configuration de l’instance.
   
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Dans la CLI, vous pouvez ajouter un paramètre `--assign-identity` à la commande `az dt create` utilisée pour créer l’instance. (Pour plus d’informations sur cette commande, consultez sa [documentation de référence](/cli/azure/dt#az_dt_create) ou les [instructions générales relatives à la configuration d’une instance d’Azure Digital Twins](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Pour créer une instance avec une identité managée par le système, ajoutez le paramètre `--assign-identity` comme ceci :

```azurecli-interactive
az dt create --dt-name <new-instance-name> --resource-group <resource-group> --assign-identity
```

---

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Ajouter une identité gérée par le système à une instance existante

Dans cette section, vous allez ajouter une identité managée par le système à une instance existante d’Azure Digital Twins. Utilisez les onglets ci-dessous pour sélectionner des instructions pour votre expérience préférée.

# <a name="portal"></a>[Portail](#tab/portal) 

Commencez par ouvrir le [portail Azure](https://portal.azure.com) dans le navigateur.

1. Recherchez le nom de votre instance dans la barre de recherche du portail, puis sélectionnez-le pour afficher plus de détails.

1. Sélectionnez **Identité** dans le menu de gauche.

1. Dans cette page, sélectionnez l’option **On** (Activer) pour activer cette fonctionnalité.

1. Sélectionnez le bouton **Enregistrer**, puis **Oui** pour confirmer.

    :::image type="content" source="media/how-to-route-with-managed-identity/identity-digital-twins.png" alt-text="Capture d’écran du portail Azure, montrant la page Identité pour une instance Azure Digital Twins.":::

Une fois la modification enregistrée, d’autres champs s’affichent sur cette page pour les champs **ID d’objet** et **Autorisations** de la nouvelle identité.

Vous pouvez copier l’**ID d’objet** à partir d’ici, si nécessaire, et utiliser le bouton **Autorisations** pour afficher les rôles Azure affectés à l’identité. Pour configurer certains rôles, passez à la section suivante.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Encore une fois, vous pouvez ajouter l'identité à votre instance en utilisant la commande `az dt create` et le paramètre `--assign-identity`. Au lieu de fournir le nouveau nom d’une instance à créer, vous pouvez indiquer le nom d’une instance déjà existante afin de permettre la mise à jour de la valeur `--assign-identity` pour cette instance.

La commande qui permet d’**activer** l’identité managée est la même que la commande qui permet de créer une instance avec une identité managée par le système. La valeur du paramètre de nom d’instance est la seule chose qui change :

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity
```

Pour **désactiver** l’identité managée sur une instance où elle est activée, utilisez la commande suivante afin d’affecter à `--assign-identity` la valeur `false`.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity false
```

---

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

Pour plus d’informations sur les points de terminaison, les routes et les types de destinations pris en charge pour le routage dans Azure Digital Twins, consultez [Routes d'événement](concepts-route-events.md).

### <a name="assign-the-role"></a>Attribuer le rôle

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Utilisez les onglets ci-dessous pour sélectionner des instructions pour votre expérience préférée.

# <a name="portal"></a>[Portail](#tab/portal) 

Pour affecter un rôle à l’identité, commencez par ouvrir le [portail Azure](https://portal.azure.com) dans un navigateur.

1. Accédez à votre ressource de point de terminaison (votre rubrique Event Hub, Service Bus, ou votre conteneur de stockage) en recherchant son nom dans la barre de recherche du portail. 

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle souhaité à l’identité gérée de votre instance Azure Digital Twins à l’aide des informations ci-dessous. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Sélectionnez le rôle souhaité dans le menu déroulant. |
    | Attribuer l’accès à | Sous **Identité managée affectée par le système**, sélectionnez **Digital Twins**. |
    | Membres | Sélectionnez l’identité gérée de votre instance Azure Digital Twins affectée au rôle. Dans la mesure où le nom de l’identité managée correspond au nom de l’instance, vous devez choisir le nom de votre instance d’Azure Digital Twins. |
    
    ![Page Ajouter une attribution de rôle](../../includes/role-based-access-control/media/add-role-assignment-page.png)

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Vous pouvez ajouter le paramètre `--scopes` à la commande `az dt create` pour attribuer l’identité à une ou plusieurs étendues ayant le rôle spécifié. La commande avec ce paramètre peut être utilisée au moment de la création initiale de l’instance, ou plus tard en passant le nom d’une instance qui existe déjà.

Voici un exemple qui crée une instance avec une identité managée par le système, et qui attribue à cette identité un rôle personnalisé appelé `MyCustomRole` dans un hub d’événements.

```azurecli-interactive
az dt create --dt-name <instance-name> --resource-group <resource-group> --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource-group>/providers/Microsoft.EventHub/namespaces/<Event-Hubs-namespace>/eventhubs/<event-hub-name>" --role MyCustomRole
```

Pour plus d’exemples d’attributions de rôles à l’aide de cette commande, consultez la documentation de référence relative à [az dt create](/cli/azure/dt#az_dt_create).

Vous pouvez également utiliser le groupe de commandes [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) pour créer et gérer des rôles. Cette commande permet la prise en charge d’autres scénarios dans lesquels vous ne souhaitez pas regrouper l’attribution de rôle avec la commande create.

---

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Créer un point de terminaison avec une authentification basée sur l’identité

Après la configuration d’une identité gérée par le système pour votre instance Azure Digital Twins et l’affectation du ou des rôles appropriés, vous pouvez créer des [points de terminaison](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins) Azure Digital Twins capables d’utiliser l’identité pour l’authentification. Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

>[!NOTE]
> Vous ne pouvez pas modifier un point de terminaison déjà créé avec une identité basée sur une clé pour passer à l’authentification basée sur l’identité. Vous devez choisir le type d’authentification lorsque le point de terminaison est créé pour la première fois.

Utilisez les onglets ci-dessous pour sélectionner des instructions pour votre expérience préférée.

# <a name="portal"></a>[Portail](#tab/portal) 

Commencez par suivre les [instructions pour créer un point de terminaison Azure Digital Twins](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins).

Lorsque vous accédez à l’étape consistant à fournir les détails de votre type de point de terminaison, veillez à sélectionner **Basée sur l’identité** comme type d’authentification.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Capture d’écran de la création d’un point de terminaison de type Event Hub." lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Terminez la configuration de votre point de terminaison et sélectionnez **Enregistrer**.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

La création du point de terminaison avec la CLI est effectuée en ajoutant un paramètre `--auth-type` à la commande `az dt endpoint create` utilisée pour créer le point de terminaison. (Pour plus d’informations sur cette commande, consultez sa [documentation de référence](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) ou les [instructions générales relatives à la configuration d’un point de terminaison Azure Digital Twins](how-to-manage-routes.md#create-the-endpoint)).

Pour créer un point de terminaison qui utilise l’authentification basée sur l’identité, spécifiez le type d’authentification `IdentityBased` avec le paramètre `--auth-type`. L’exemple ci-dessous illustre cette fonctionnalité pour un point de terminaison Event Hubs.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <endpoint-name> --eventhub-resource-group <eventhub-resource-group> --eventhub-namespace <eventhub-namespace> --eventhub <eventhub-name> --auth-type IdentityBased --dt-name <instance-name>
```

---

## <a name="considerations-for-disabling-system-managed-identities"></a>Considérations relatives à la désactivation des identités gérées par le système

Étant donné qu’une identité est gérée séparément des points de terminaison qui l’utilisent, il est important de prendre en compte les effets que toute modification apportée à l’identité ou à ses rôles peut avoir sur les points de terminaison de votre instance Azure Digital Twins. Si l’identité est désactivée, ou si un rôle nécessaire à un point de terminaison est supprimé de celui-ci, le point de terminaison peut devenir inaccessible, et le flux des événements est interrompu.

Pour continuer à utiliser un point de terminaison configuré avec une identité gérée maintenant désactivée, vous devez supprimer le point de terminaison et [le recréer](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins) avec un autre type d’authentification. L’opération peut prendre jusqu’à une heure avant que les événements ne soient remis au point de terminaison après cette modification.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les identités managées dans Azure AD : 
* [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md)